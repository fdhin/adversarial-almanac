---
technology: "Microsoft Azure attack surface (ARM / Entra ID / Managed Identity)"
kind: overview
audience: offensive-security
status: upstream-only
family: cloud
layer: public-cloud control plane — Azure Resource Manager, Microsoft Entra ID (formerly Azure AD) directory, Managed Identity token service, and the Storage / Key Vault / Automation / Functions data planes behind them
homepage: https://azure.microsoft.com
source: "six2dez/pentest-book (enumeration/cloud/azure.md, no license — rewritten with attribution), accessed 2026-11-13"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — key techniques verified against Microsoft Learn (Instance Metadata Service: link-local 169.254.169.254, in-VM-only, Metadata:true header + api-version; Run Command via VM agent with RunShellScript/RunPowerShellScript, elevated by default; RBAC built-in roles Owner/Contributor/User Access Administrator; blob anonymous access prohibited by default at account and container level) + rewritten from six2dez/pentest-book (enumeration/cloud/azure.md, no license — rewritten with attribution), accessed 2026-11-13; supplemented 2026-11-14 from NetSPI/MicroBurst source analysis (elevateAccess root-scope semantics per Microsoft Learn elevate-access doc, automation RunAs pivots, Kudu command/token paths — function set verified against repo master)"
---

# Microsoft Azure attack surface (ARM / Entra ID / Managed Identity)

> Azure is a control plane you talk to with a token, not a port you scan: whoever holds a user's context file or a VM's identity endpoint owns the subscription behind it.

## What it is

This unit covers the Azure an attacker actually touches: the Azure Resource Manager (ARM) control plane that every `az` CLI and Az PowerShell call speaks, the Microsoft Entra ID directory (the identity service formerly branded Azure AD) that gates it, and the resource-level surfaces those credentials unlock — storage accounts, Key Vaults, Automation accounts, VMs, and Function apps. The classic deployment model (ASM) still lingers in management certificates and legacy portals, but ARM is where service principals, resource groups, and role assignments live, and where every post-compromise chain runs.

The design property that matters: **authorization is token-shaped and role-assignment-driven, not network-shaped**. No open port is required to own a subscription — a stolen context file, a sprayed password, a service-principal secret in a build script, or a token minted from a VM's Managed Identity all authenticate to the same public endpoints. Microsoft's own Instance Metadata Service documentation spells out the boundary: IMDS answers at the non-routable `169.254.169.254`, reachable only from inside the VM, never leaving the host. Which means the perimeter you must beat is wherever a token is stored or minted, and nothing else.

The characteristic mistake is treating cloud identities as second-class credentials. A `.publishsettings` file is a base64 management certificate with no password. A developer's `%USERPROFILE%\.azure\` directory holds `TokenCache.dat` and `AzureRmContext.json`, which can be replayed on another machine with `Import-AzContext`. A runbook written for convenience holds the service-account password for a production database. And the four classic RBAC roles hand out far more than their names suggest: Contributor can manage every resource in scope (it merely cannot *assign roles*), while User Access Administrator exists specifically to change who has which role — usually the one-step path to Owner.

## The anatomy that matters on an engagement

    get an identity in the tenant:
      password spray → login.microsoftonline.com (MSOLSpray-class tooling, AADSTS codes tell you why it failed)
      phishing → Evilginx2/Modlishka session hijack (rides MFA), then act fast before session expiry
      loot → .azure\ TokenCache.dat + AzureRmContext.json · .publishsettings certs · .cspkg zips ·
             web.config/app.config connection strings · Azure DevOps PATs
    → authenticate:  az login / Connect-AzAccount / Import-AzContext (stolen profile)
                     (token-only: a raw bearer token from SSRF / Cloud Shell / Automation /
                      an App Service answers ARM REST directly — no login, no modules)
    → enumerate:     Get-AzSubscription · Get-AzRoleAssignment · Get-AzADApplication / az ad sp list --all ·
                     Get-AzResource(Group) · runbooks · function apps · keyvaults · storage accounts
    → escalate:      User Access Administrator → New-AzRoleAssignment Owner · add credential to an
                     existing app (New-AzADAppCredential) · VM Contributor → run-command on any VM ·
                     Global Administrator → elevateAccess POST → UAA at root scope → all subscriptions
    → move laterally: IMDS token from a VM/Function's Managed Identity (169.254.169.254) →
                     management.azure.com / vault.azure.net / storage.azure.com tokens
    → persist:        new service principal + Owner · app credential injection · Automation webhook ·
                     new cloud-only user · long-lived SAS token · consent-phishing OAuth app
    hybrid leg (Entra crosslinks): AD Connect sync account (PHS = on-prem hash dump) ·
                     AZUREADSSOACC$ Kerberos impersonation · conditional-access bypass via sync account

Three structural properties drive most findings:

- **Context files are portable sessions.** `Save-AzContext` writes an importable JSON profile; `TokenCache.dat` plus `AzureRmContext.json` copied off a workstation replays the session elsewhere. Tokens cached for one Microsoft endpoint routinely work on others.
- **Role assignments are the real target model.** Enumerating *which* role the stolen identity holds (Get-AzRoleAssignment) matters more than enumerating resources — Owner, Contributor, User Access Administrator, VM Contributor, Application Administrator each name a different one-command escalation path.
- **Compute is reachable without ports.** The Run Command feature pushes scripts through the VM agent even when SSH/RDP are firewalled shut, running elevated by default — a VM Contributor never needs network access to the box to execute on it.
- **Automation accounts hold identities, not just scripts.** An account's RunAs connection is a service principal with its own role assignments, frequently broader than the operator's. Scope what each automation identity can reach, run a temporary runbook that authenticates as it (dumping vaults it can read), or lift the RunAs certificate off a Hybrid Worker VM and sign in as that principal — the automation layer is both a pivot and a persistence substrate.

## Where you'll meet it

- **O365-first engagements.** The tenant is Entra ID whether or not anyone bought "Azure": spray `login.microsoftonline.com`, read `getuserrealm.srf` and the `.well-known/openid-configuration` document for tenant ID and auth configuration before touching anything authenticated.
- **Developer and CI workstations.** Visual Studio publish directories, `.cspkg` packages, `.publishsettings` downloads, and the `.azure` directory are the loot that converts host access into cloud access.
- **Web apps hosted on App Service / Functions.** SSRF inside the app reaches the identity endpoint; app settings pages carry secrets; the Kudu console (`<app>.scm.azurewebsites.net`) is a file system and command console behind deployment credentials.
- **Hybrid on-prem AD shops.** Where Password Hash Sync or Seamless SSO is deployed, the sync account and the `AZUREADSSOACC$` computer account are bridges between a Kerberos foothold ([[kerberos]]) and the cloud directory; the NTLM/hash side is covered in [[ntlm]].
- **Sister clouds.** The same stolen-CLI-credential-to-control-plane pattern is documented per-provider in [[aws]] and [[gcp]].

## Reading the framework's mind

1. **Unauthenticated tenant recon is built in.** `https://login.microsoftonline.com/getuserrealm.srf?login=<user>@<tenant>.onmicrosoft.com&xml=1` reveals whether the domain is Entra-managed (`NameSpaceType: Managed`) or federated; the tenant's openid-configuration document hands over the tenant ID. Federated tenants (ADFS) mean password attacks should target the on-prem portal, not Microsoft's.
2. **Failure codes are an oracle.** The AADSTS error set distinguishes wrong password, no MFA, locked, disabled, expired — spray tooling (MSOLSpray-class) classifies per-user outcomes from it; Smart Lockout is IP-based, so a rotating-frontal proxy (FireProx-class) keeps the door open.
3. **RBAC and Entra admin roles are different systems.** Azure RBAC roles (Owner/Contributor/Reader/User Access Administrator) govern ARM resources; Entra directory roles (Global Administrator, Application Administrator) govern the directory. A standard user can read directory data via cmdlets even when the portal is locked down — unless the org-wide `UsersPermissionToReadOtherUsersEnabled` switch was flipped.
4. **Managed Identity means never storing a key — until SSRF.** Every resource that has one can mint tokens from the inside: VMs via IMDS at `169.254.169.254` (header `Metadata: true`, `api-version` required), App Service/Functions via the `IDENTITY_ENDPOINT`/`X-IDENTITY-HEADER` environment pair. The `resource=` parameter picks the audience: management, Key Vault, Graph, or Storage.
5. **Storage namespace is guessable.** Account names sit at fixed DNS shapes (`<account>.blob.core.windows.net` plus file/table/queue variants), so name permutation and DNS wordlists find them externally — but anonymous access is prohibited by default at the account level, so public data means someone opted in at two levels; the `Container` level lets anyone list, the `Blob` level only lets them read known names.
6. **Global Administrator reaches the RBAC side through one documented call.** Entra roles grant no Azure resource access — except this bridge: `POST /providers/Microsoft.Authorization/elevateAccess` assigns the calling Global Admin User Access Administrator at root scope `/`, exposing every subscription and management group in the tenant. It is per-user, audit-logged (Sentinel ships a ready-made detection template), and the reason a directory-only compromise is never directory-only. Covered as a Phase 5 path in [[azure-bug-bounty]].

## Serverless: Azure Functions as a section

Function apps compress the whole unit into one resource: they carry app settings (connection strings and keys in `az functionapp config appsettings list`), function-level auth keys, an attached Managed Identity, and a Kudu deployment endpoint. The attack order: list function apps and settings, pull function keys (`az functionapp function keys list`), then fetch source through Kudu (`https://<app>.scm.azurewebsites.net/api/zip/site/wwwroot/` with deployment credentials). HTTP triggers declare an authorization level — `anonymous` (no key), `function` (per-function key as `?code=`), `admin` (master key) — so an unauthenticated `curl` against `/api/<name>` is the cheap test, and a recovered key is quiet programmatic access. From inside a compromised function, Managed Identity is reachable via the `IDENTITY_ENDPOINT` variables rather than the VM IMDS path — an SSRF primitive in the app becomes a `vault.azure.net` token, and the Key Vault behind it. Lambda's counterpart on the AWS side is documented in [[aws]]; Google's in [[gcp]]. The same Kudu channel also carries command execution on App Service hosts: deployment credentials are derivable from contributor rights (the publishing profile contains them, or the API accepts the Az context's Bearer token), and apps with integrated Entra ID auth keep encrypted tokens and client credentials in app settings that decrypt once commands run in the sandbox — Phase 8B in [[azure-bug-bounty]].

## Where to go next

The phased hunt methodology — spray, token theft, authenticated enumeration, Managed Identity abuse, escalation, persistence, hybrid Entra paths — is in [[azure-bug-bounty]].
