---
technology: "Microsoft Azure attack surface (ARM / Entra ID / Managed Identity)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.2
source: "six2dez/pentest-book (enumeration/cloud/azure.md + enumeration/cloud/serverless.md, no license — rewritten with attribution), accessed 2026-11-13; Supplement 2026-11-13: rewritten from dafthack/CloudPentestCheatsheets (cheatsheets/Azure.md), MIT, accessed 2026-11-13"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — endpoint shapes, headers, role semantics, and defaults verified against Microsoft Learn (IMDS overview, Linux/Windows Run Command, RBAC built-in roles, blob anonymous-read access configuration) during authoring, 2026-11-13; supplemented 2026-11-13 from NetSPI/MicroBurst source analysis (elevateAccess REST form per Microsoft Learn elevate-access doc, token-only ARM paths, automation RunAs pivots, Kudu command/token paths, Wire Server extension-settings decryption — function set verified against repo master); supplemented 2026-11-13 from hausec/PowerZure source analysis (VM disk SAS export, Custom Script Extension/UserData-channel execution, Intune script push — function set verified against repo master)"
---

Companion page: [[azure|Azure attack surface overview]] — ARM / Entra ID / Managed Identity in one model. Tool notes live in the tools vault; Appendix A names tools in prose.

# Azure bug bounty methodology (ARM / Entra ID / Managed Identity)

**Scope:** unauthenticated tenant recon, password attacks against Entra ID endpoints, token/credential loot on hosts, authenticated enumeration with `az` / Az PowerShell, Managed Identity and IMDS abuse, RBAC privilege escalation, persistence, and the hybrid Entra/on-prem bridges. **Out of scope:** generic web-app bugs on hosted sites (they belong to the web-stack units) — the metadata IP appears here only as an internal token source.

**Authorization:** everything past Phase 1 mutates or reads a live directory. Password spray within scope only, minimal object creation, and treat run-command invocation as production RCE: one proof command, then stop.

## Decision tree (one screen)

1. No credentials → Phase 1 (tenant recon + spray surface) and Phase 1C (public blob namespace).
2. Credentials or a hijacked session → Phase 2 (token/context file hunt if on a host), then Phase 3 enumeration.
3. Any compute foothold (VM shell, Function app, App Service web root) → Phase 4 (identity endpoint immediately).
4. Role assignment shows Owner/User Access Administrator/Contributor/VM Contributor → Phase 5 (escalate), then Phase 6 (persist).
5. On-prem AD access in a hybrid tenant → Phase 7 (sync account / seamless SSO bridges).
6. Only a raw bearer token, no login possible (SSRF exfil, Cloud Shell / Automation / App Service theft) → Phase 4B.

## Phase 1 — Unauthenticated recon

Tenant shape before any login attempt:

```
curl "https://login.microsoftonline.com/getuserrealm.srf?login=admin@example.onmicrosoft.com&xml=1"
# NameSpaceType "Managed" => Entra-managed domain; "Federated" => ADFS: aim password attacks at the on-prem portal
curl "https://login.microsoftonline.com/example.onmicrosoft.com/.well-known/openid-configuration"
# tenant_id, token endpoints, supported auth methods
```

Email validity is testable against the common OAuth token endpoint (existing vs non-existent user responses differ); dedicated user-enumeration scripts (UhOh365-class for O365) batch it. Subdomain and storage enumeration in 1C; AADInternals' `Get-AADIntTenantID` / `Get-AADIntTenantDomains` wrap the same endpoints.

## Phase 2 — Password attacks and token theft

### 2A Spray

The target is `POST /common/oauth2/token` on login.microsoftonline.com with a password grant. Spray tooling of the MSOLSpray class matters because it parses AADSTS return codes into per-account outcomes: valid credential, MFA required, tenant absent, user absent, locked, disabled, expired password. Smart Lockout is driven by source IP — a FireProx-class rotating AWS-API-Gateway front keeps each attempt from a fresh address. Federated domains: spray the ADFS portal instead (the cloud endpoint will not validate the password).

### 2B Session hijack

Evilginx2/Modlishka-class adversary-in-the-middle proxies ride the authenticated session and defeat push-MFA; sessions expire, so plan persistence (Phase 6) before the window closes.

### 2C Token and credential files on hosts

| Loot | Where | Use |
| --- | --- | --- |
| `TokenCache.dat`, `AzureRmContext.json` | `%USERPROFILE%\.azure\` | `Import-AzContext` replays the session on your box |
| `Save-AzContext` export | anywhere the operator put it | same import path |
| `.publishsettings` | Downloads, VS projects | base64 `ManagementCertificate` → passwordless PFX (classic-management API) |
| `.cspkg`, `web.config`, `app.config` | publish dirs, source | connection strings, embedded certs |

Manual context transplant when the profile is half-copied: base64 the token cache, paste it into the `CacheData` field of `AzureRmContext.json` (null placeholder by default), then `Import-AzContext -Profile <file>`. Confirm with `Get-AzContext`.

## Phase 3 — Authenticated enumeration

```
az login --use-device-code            # or: Connect-AzAccount -Credential $cred (sometimes slips past MFA prompts)
az account show; az account list --all        # subscriptions = blast radius; names leak "Prod"/"Dev"
az role assignment list --all --include-inherited       # THE map: who holds what on which scope
az ad app list --all; az ad sp list --all     # apps, service principals (credential holders)
az resource list; az group list               # resources/resource groups
```

PowerShell equivalents: `Get-AzSubscription`, `Get-AzRoleAssignment`, `Get-AzADApplication`, `Get-AzVM`, `Get-AzWebApp`, `Get-AzSqlServer` (+ firewall rules, Entra admin), `Get-AzStorageAccount`. Directory enumeration through the legacy MSOnline/AzureAD modules (`Get-MsolUser -All | fl`, `Get-MsolCompanyInformation` — watch `UsersPermissionToReadOtherUsersEnabled`, `DirSyncServiceAccount`, `PasswordSynchronizationEnabled`) still answers where the modules are installed; both are retired in favor of Microsoft Graph PowerShell (`Get-MgUser`), so match the module to what the environment still runs. Dynamic groups leak their membership rules — rules that stamp privileged roles onto users by attribute are escalation gifts.

Per-surface enumeration that pays immediately:

```
az automation account list ... / Get-AzAutomationAccount; Get-AzAutomationRunbook ...; Export-AzAutomationRunbook ...
Get-AzAutomationCredential/Variable ...        # runbooks scavenge well: embedded passwords, connection strings
az keyvault list; az keyvault secret list --vault-name <vault>; az keyvault secret show ...
az storage account list; az storage account keys list ...   # account keys = full data-plane control
az functionapp list; az functionapp config appsettings list ...   # Phase 8
```

Beyond vaults, storage and runbooks, sweep the services that hold credentials but get forgotten in review: container registries (admin-user credentials, retrievable with contributor rights), App Configuration (key-values behind HMAC-signed access keys — the REST calls authenticate with the key alone, no Microsoft identity involved), Cognitive Services and Batch account keys, and Azure ML workspaces, whose datastores hand out storage account keys and SQL usernames/passwords through the ML API to anyone with workspace-read (MicroBurst Get-AzMachineLearningCredentials). A Get-AzPasswords-class sweeper covers all of these in one pass — its current service list spans 18 sources including Function Apps, Container Apps, API Management, Service Bus, App Configuration and Cognitive Services, with per-service toggles to stay quiet.

### 3A — Automation accounts as pivot identities

An Automation account's RunAs connection is a service principal with its own role assignments — frequently broader than the operator's. Treat it as a credential, in escalating order of noise:

1. Scope first (read-only, nothing logged): resolve which subscriptions and Key Vaults each automation identity can actually reach — the targeting map for everything below (MicroBurst Get-AzAutomationConnectionScope).
2. If an identity reads a vault you cannot: deploy a temporary runbook that authenticates with the RunAs connection and exports that vault's keys, secrets and certificates (Get-AzKeyVaults-Automation; a ready-made KeyVaultRunBook ships with MicroBurst). Opsec: runbook jobs are job-logged, and runbook cleanup can fail — check what you left behind.
3. Durable takeover: the RunAs certificate itself is recoverable from the automation account with the right rights, or from Windows Hybrid Worker VMs (Invoke-AzHybridWorkerExtraction, with `-StorageAccount`/`-StorageKey` to exfiltrate to a blob you read) — afterwards authenticate directly as the service principal, independent of the original compromise. Custom-module listings (Get-AzAutomationCustomModules) flag automation estates running vulnerable module versions.

## Phase 4 — Managed Identity and IMDS

From any Azure VM shell (IMDS is reachable only inside the VM; clients must bypass host web proxies):

```
curl -s -H "Metadata: true" --noproxy "*" \
  "http://169.254.169.254/metadata/instance?api-version=2021-02-01"    # what am I on
curl -s -H "Metadata: true" \
  "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/"
```

Swap `resource=` for `https://vault.azure.net` (Key Vault), `https://graph.microsoft.com` (directory), `https://storage.azure.com/` (storage). `az login --identity` on the box does the same and yields a full CLI session. SSRF in a web app hosted on the VM aims the same URL at the same endpoint — the header requirement (`Metadata: true`) and the mandatory `api-version` parameter are the two details a naive SSRF payload gets wrong. App Service / Functions: the identity endpoint is NOT IMDS — read `IDENTITY_ENDPOINT` and `IDENTITY_HEADER` from the environment and send the value as `X-IDENTITY-HEADER`.

Identity tokens are also mintable from the control plane, with no host access at all, wherever a resource you can aim carries an identity: Arc-enabled servers (Get-AzArcCertificates), container registries (Invoke-AzACRTokenGenerator), and user-assigned identities exercised through a deploymentScripts resource you create yourself — the deployment script runs under the chosen identity and can echo its freshly-minted token into the script output (Invoke-AzUADeploymentScript).

### 4B — Token-only access: a raw bearer token, no login

When the credential is a bare management-scoped token — exfiltrated through SSRF, lifted from Cloud Shell, an Automation job, or an App Service environment — ARM REST answers directly. No `az login`, no PowerShell modules, any OS, nothing cached to clean up:

```
TOK=<management bearer token>
curl -s -H "Authorization: Bearer $TOK" \
  "https://management.azure.com/subscriptions?api-version=2019-11-01"   # blast radius first
curl -s -X POST -H "Authorization: Bearer $TOK" -H "Content-Type: application/json" \
  "https://management.azure.com/subscriptions/<sub>/resourceGroups/<rg>/providers/Microsoft.Compute/virtualMachines/<vm>/runCommand?api-version=2020-06-01" \
  -d '{"commandId":"RunPowerShellScript","script":["whoami"]}'          # guest RCE, Windows; RunShellScript for Linux
curl -s -X POST -H "Authorization: Bearer $TOK" \
  "https://management.azure.com/subscriptions/<sub>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<acct>/listKeys?api-version=2019-06-01"
```

MicroBurst's REST family wraps exactly this state — Invoke-AzVMCommandREST (multi-subscription Run Command against both Windows and Linux), Get-AzStorageKeysREST, Get-AzAutomationAccountCredsREST, Get-AzDomainInfoREST — for hosts where installing Az modules is not an option. Two constraints shape op planning: `api-version` is mandatory on every call, and ARM tokens expire (~1 hour), so keep the minting primitive (the SSRF, the job, the shell) alive. Key Vault data is NOT reachable with the management token — the vault plane wants a separately-scoped token (`resource=https://vault.azure.net`); hold both and Get-AzKeyVaultSecretsREST-class tooling dumps secrets with the pair (`-managementToken` + `-vaultToken`).

### 4C — VM extension protected settings, from inside the guest

The VM agent materializes every extension's configuration on the guest: `C:\Packages\Plugins\*\*\RuntimeSettings\*.settings` (Windows) carries publicSettings in cleartext and protectedSettings as certificate-encrypted blobs — VMAccount reset usernames/passwords, domain-join credentials, CustomScript `fileUris` and `commandToExecute`. From a guest shell, collect the files and decrypt wherever the thumbprinted certificate is present (MicroBurst Get-AzureVMExtensionSettings; it also parses the guest-log zips under `C:\WindowsAzure\CollectGuestLogsTemp`). No ARM rights and no management-plane visibility are required at all. The same extension configurations are served over HTTP by the host's Wire Server endpoint at 168.63.129.16 — request `/machine/?comp=goalstate`, follow the ExtensionsConfig link — with local administrator rights, from Windows or Linux guests alike (Get-AzureVMExtensionSettingsWireServer and its `.sh` sibling): the path to take when you have a foothold on the box but no read access to the subscription.

## Phase 5 — Privilege escalation

Verify role semantics against the built-in definitions (all upstream-confirmed): Owner = full access *including role assignment*; Contributor = full management *excluding* role assignment; User Access Administrator = manage access; Reader = view only.

- **Global Administrator (any Az context)** → `POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01` assigns the caller User Access Administrator at root scope `/` — the documented bridge between the directory role system and Azure RBAC, exposing every subscription and management group in the tenant (Microsoft ships it as the "Access management for Azure resources" toggle; MicroBurst Invoke-AzElevatedAccessToggle is the one-liner). The call is audit-logged, and it is per-user — then the UAA→Owner path above applies tenant-wide.
- **User Access Administrator** → `New-AzRoleAssignment -SignInName <you> -RoleDefinitionName Owner -Scope /subscriptions/<sub>` (or `az role assignment create`). Done.
- **Contributor** → cannot assign roles, but can manage everything: run-command on VMs, rotate storage keys, edit runbooks, export a VM's OS disk (time-limited SAS download link) for offline credential dumping — data-plane equivalent of ownership.
- **VM Contributor / any run-command right** → `az vm run-command invoke -g <rg> -n <vm> --command-id RunPowerShellScript --scripts "whoami"` (Windows) or `RunShellScript` (Linux). Runs through the VM agent, elevated by default, no SSH/RDP needed, output capped at the last 4 KiB.
- **VM write rights beyond run-command** → the same agent plane has more doors than Run Command: the Custom Script Extension pushes execution via extension settings, and the VM UserData channel executes through the agent (PowerZure's Invoke-AzureCustomScriptExtension / Invoke-AzureVMUserDataCommand pair) — paths worth switching to when Run Command invocations are alerted on.
- **Intune admin rights** → upload a script to Intune and it executes across every enrolled device in the tenant (PowerZure New-AzureIntuneScript) — device management as a fleet-execution surface.
- **Application Administrator / app-write rights** → add a credential to an existing high-privilege app (`New-AzADAppCredential`), then authenticate as it — no new object, no obvious change.
- **Group rights** → `Add-AzureADGroupMember` into a privileged group.
- **Logic App write rights in a resource group + an existing API connection** → deploy a throwaway Logic App whose definition uses that connection, trigger it, read the output, delete it: the actions execute under the connection's identity, no credentials touched (MicroBurst Invoke-APIConnectionHijack; the repo ships a ready-made Key Vault dump payload).

## Phase 6 — Persistence

Ranked by quietness: (1) credential injection on an existing service principal; (2) new service principal with a benign display name plus Contributor/Owner, secret retained; (3) Automation webhook (`New-AzAutomationWebhook`) bound to a runbook — a URL you can call for a year; (4) long-expiry SAS token on a storage container (`az storage container generate-sas --permissions rwdl --expiry <far date>`); (5) a fresh cloud-only user where User Administrator rights exist; (6) a consent-phishing OAuth app registration that harvests tokens from other tenants' users; (7) a Bastion shareable link — enabling the feature on a Bastion and minting a `(bsl=)` URL exposes the VM's RDP/SSH session through a public browser page, no network path to the host required (Invoke-AzRESTBastionShareableLink; list existing links with Get-AzRestBastionShareableLink); (8) a DSC configuration pushed through the VM extension with `ApplyAndAutoCorrect`, which re-creates removed artifacts on the VM's configuration schedule until the DSC agent itself is removed (Invoke-DscVmExtension). The automation-webhook item has an owner-mint variant: a runbook whose webhook trigger creates a cloud user with Owner rights on the subscription (MicroBurst's OwnerPersist pair) — unauthenticated POST, and the webhook URL is the only guard.

## Phase 7 — Hybrid: Entra ↔ on-prem bridges

- **Password Hash Sync:** the AD Connect sync account can dump every synced on-prem password hash (it is a directory user AND the sync engine). Compromise of the sync account ≈ compromise of the synced forest's credentials; it also bypasses conditional-access policies framed for "cloud" admins.
- **Seamless SSO:** the `AZUREADSSOACC$` computer account — anyone who edits its properties (or holds its Kerberos keys, decryptable through the AD Connect config: DPAPI → AES keys → AES-256-CBC blob) can mint Kerberos tickets impersonating any cloud user without MFA. The Kerberos mechanics live in [[kerberos]].
- **Federated (ADFS):** cloud endpoints defer to on-prem; the on-prem portal is the password target.

## Phase 8 — Serverless: Azure Functions

Enumerate: `az functionapp list`, `az functionapp config appsettings list` (secrets), `az functionapp function keys list`, connection strings, deployment source config. Source pull via Kudu: `https://<app>.scm.azurewebsites.net/api/zip/site/wwwroot/` authenticated with deployment credentials — the same console gives a file browser and debug shell. Test trigger auth levels in order `anonymous` → `function` (`curl "https://<app>.azurewebsites.net/api/<name>?code=<key>"`) → `admin`. Inside the function: app settings env vars, `IDENTITY_ENDPOINT` token mint (Phase 4), and any outbound HTTP the function makes (SSRF to internal endpoints). Deployment rights are code-execution rights: `az functionapp deployment source config-zip` pushes your package.

## Phase 8B — App Service hosts: Kudu execution and token decryption

Kudu is not just a source-pull path — it is command execution on the app host, and the credentials need not be stolen. With contributor rights on the app, the publishing profile (`Get-AzWebAppPublishingProfile`) contains the deployment username/password; or skip Basic auth entirely and call the Kudu API with a Bearer token from the current Az context, posting a command to `https://<app>.scm.azurewebsites.net/api/command` (MicroBurst Invoke-AzAppServicesCMD does both, fleet-wide; Invoke-AzAppServicesKuduDebug drives the interactive PowerShell/CMD debug console on Windows container apps).

Apps with integrated Entra ID authentication hold tokens and app-registration credentials in app settings, encrypted under the hood — but the decryption key lives in the environment. With command execution in the sandbox, the blobs decrypt to cached user tokens (per named user) and the app's own credential material (Get-AzWebAppTokens): treat any app-settings read primitive as deferred credential disclosure, and any Kudu console access as full app-tenant compromise.

## Fingerprint signatures

- `getuserrealm.srf` XML → `NameSpaceType` tells managed vs federated before you commit to an attack path.
- Storage: empty/absent public container answers HTTP 400 from `<account>.blob.core.windows.net`; authenticated listing errors return XML `AuthenticationFailed`; `Container`-level public access answers `?restype=container&comp=list` with a full listing (anonymous listing means opt-in at two levels — account and container).
- Login responses carry AADSTS error codes (`…/reference-aadsts-error-codes`) — treat them as the spray oracle.
- Dangling CNAMEs to `cloudapp.net`, `azurewebsites.net`, `*.<region>.cloudapp.azure.com` on unclaimed resources = takeover candidates (register the resource shape the record points to).

## Appendix A — tools (prose)

Tenant and directory recon: AADInternals, ROADtools (roadrecon gather/gui), MicroBurst (Invoke-EnumerateAzureSubDomains, Invoke-EnumerateAzureBlobs), o365creeper/UhOh365-class user checks, o365recon. Password attacks: MSOLSpray behind FireProx. Graph/attack-path mapping: AzureHound feeding BloodHound, Stormspotter (archived upstream — historical reference). Azure-native exploitation: PowerZure — the authenticated-phase workhorse: role-to-resource targeting (Get-AzureTarget), inverse role lookup by permission (Get-AzureRolePermission), vault export to PEM/PFX, VM disk SAS export (Phase 5), the execution variants of Phase 5 (Run Program, MSBuild payload, Custom Script Extension, UserData channel), Intune fleet push, the Global-Admin elevation (Set-AzureElevatedPrivileges — same bridge as MicroBurst's and AADInternals' one-liners), the service-principal Global Admin backdoor, the managed-identity token station (Invoke-AzureMIBackdoor), and raw-JWT login (Connect-AzureJWT) plus the standalone module-free New-AzureToken for minting an ARM token on a conditional-access-trusted host. Auditing from stolen credentials: ScoutSuite (`scout azure`), prowler. On-host loot: manual — the `.azure` directory and publish artifacts are plaintext-enough not to need a tool. MicroBurst additionally implements most of this methodology as functions and is the reference implementation to reach for past recon: subscription snapshot (Get-AzDomainInfo), the 18-source credential sweep (Get-AzPasswords), the token-only REST family of Phase 4B (Invoke-AzVMCommandREST, Get-AzStorageKeysREST, Get-AzKeyVaultSecretsREST, Invoke-AzElevatedAccessToggle), the automation pivots of Phase 3A (Get-AzAutomationConnectionScope, Get-AzKeyVaults-Automation, Invoke-AzHybridWorkerExtraction), the extension-settings decryption of Phase 4C (Get-AzureVMExtensionSettings, Get-AzureVMExtensionSettingsWireServer), App Service execution of Phase 8B (Invoke-AzAppServicesCMD, Get-AzWebAppTokens), the control-plane identity family of Phase 4 (Get-AzArcCertificates, Invoke-AzACRTokenGenerator, Invoke-AzUADeploymentScript), and the persistence pair of Phase 6 (OwnerPersist runbook, Invoke-DscVmExtension).

## Supplement (2026-11-13) — enumeration breadth and directory loot

Rewritten from dafthack/CloudPentestCheatsheets (`cheatsheets/Azure.md`), MIT, accessed 2026-11-13. Facts re-verified at write time: Resource Graph cmdlets and `az graph query` against Microsoft Learn (Az.ResourceGraph reference, resource-graph explore page), Automation job cmdlets against the Az.Automation reference, VM user-data semantics against the Virtual Machines user-data doc (base64, <=64 KiB, unencrypted, IMDS-retrievable, `$expand=userData` control-plane read), AzureHound's flows against the SpecterOps collector docs — the cheat's own readthedocs.io AzureHound link is dead (404). Most of the sheet was already covered above; only verified-new items land here.

**A1. Resource Graph is the fast inventory path.** One KQL query crosses every subscription the token can see — no per-resource-provider loops: `az graph query -q "Resources | project name, type, location, resourceGroup, subscriptionId"` (CLI resource-graph extension) or the PowerShell `Search-AzGraph -Query "Resources | summarize count() by type | sort by count_ desc"` (Az.ResourceGraph). Count-by-type first for estate shape, then project the interesting types; `-UseTenantScope` lifts the query past the default subscription scope.

**A2. Automation job OUTPUT streams, not just runbooks.** Phase 3A exports runbook code; what those runbooks PRINTED is separate loot — `Get-AzAutomationJob` per account, then `Get-AzAutomationJobOutput -AutomationAccountName <acct> -ResourceGroupName <rg> -JobId <id>` returns the output stream of past jobs. Connection strings, tokens, and temp passwords land there because writers assume job output is internal. Sweep every automation account in every subscription.

**A3. VM user data, read side.** Phase 5's UserData channel executes through the agent; the same field is loot on the read path. `UserData` (base64, <=64 KiB) rides on the VM object: control-plane read via `GET .../virtualMachines/<vm>?$expand=userData` (the PowerShell object exposes it as `$vm.UserData`), or from INSIDE the guest through IMDS (`.../metadata/instance/compute/userData`, usual `Metadata: true` header). Microsoft's doc warns it is unencrypted and readable by any process on the VM — boot scripts with embedded secrets are the norm. Loop it across subscriptions the way you loop VMs. EC2's counterpart (user data via `describe-instance-attribute`) is documented in [[aws]].

**A4. Hunt passwords in directory attributes.** A full directory dump — `Get-MgUser -All` in the current module generation, the retired MSOnline/AzureAD cmdlets where a legacy box still runs them — is greppable for password-like values: iterate every user's properties, match NAME or VALUE against `*password*` / `*pwd*` / `*pass*` and dump the hits. Shared passwords get parked in phone-number and extension fields more often than anyone admits.

**A5. Key Vault access-policy self-grant under Contributor.** Where the vault's authorization model is vault policy (not RBAC), a Contributor-tier identity writes its own policy and then reads: `az keyvault set-policy --name <vault> --upn <you> --secret-permissions get list` (same flag shape for key/storage/certificate permissions), then the Phase 3 secret walk. RBAC-authorized vaults ignore access policies — there the same goal needs a role assignment, i.e. Phase 5's UAA/Owner path.

**A6. Network inventory rounds out the map.** `az network public-ip list`, `nsg list`, `lb list`, `private-endpoint list` (PowerShell: `Get-AzPublicIpAddress`, `Get-AzVirtualNetwork`, `Get-AzExpressRouteCircuit`, `Get-AzVpnConnection`) — which addresses face out, which NSGs gate them, and the ExpressRoute/VPN links bridging on-prem. External-surface mapping from an authenticated seat, feeding the Phase 1 recon picture.

**A7. Directory-only tenants.** `az login --allow-no-subscriptions` (then `az account tenant list`) for identities whose tenants carry no Azure estate — pure Entra targets where subscription enumeration returns nothing and directory work is the whole game.

**A8. Graph without a Graph SDK.** An authenticated CLI session calls Microsoft Graph directly: `az rest --method GET --uri https://graph.microsoft.com/v1.0/me` (also `/organization`, `/servicePrincipals?$select=id,appId,displayName`). The same channel carries writes — the invitations POST below runs through it.

**A9. Device-code flow as the credential primitive.** The raw two-step flow — POST `/common/oauth2/devicecode`, then the `device_code` grant at `/common/oauth2/token` — with the well-known public client `1950a258-227b-4e31-a9cf-717495945fc2` (Azure PowerShell; its directory-read scopes are pre-consented in most tenants) returns a refresh token once someone approves the code at `microsoft.com/devicelogin`. As a collection path, that refresh token drives AzureHound headlessly (`azurehound -r "<refresh token>" list --tenant <tenant> -o out.json`, per the SpecterOps docs — it also answers for any other tenant the user can reach). As a phishing path, the victim approves in their OWN authenticated browser — their MFA and conditional access are satisfied by their session; the attacker keeps only the code's tokens. The credential-injection idea of Phase 5 has a service-principal variant here too: `az ad sp credential reset --id <app id>` replaces an SP's secret outright.

**A10. Guest invite as persistence.** With directory write rights, POST `https://graph.microsoft.com/v1.0/invitations` with `invitedUserEmailAddress` + `inviteRedirectUrl` mints a guest account plus a redeem URL; a follow-up POST to `/roleManagement/directory/roleAssignments` (`roleDefinitionId` `62e90394-69f5-4237-9190-012177145e10`, the Global Administrator role template ID, `directoryScopeId` `/`) binds a role to the new principal. Guest creation is routine mail-flow activity in most tenants — quieter than a fresh cloud-only user in some estates.
