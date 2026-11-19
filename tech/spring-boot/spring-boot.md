---
technology: "Spring Boot"
kind: overview
audience: offensive-security
family: web-platforms
layer: self-contained Java application server (embedded Tomcat in a fat JAR; actuator management endpoints; Spring Cloud microservice stack around it)
language: Java (Kotlin sibling), Groovy/SpEL expressions
first-released: 2014 (Pivot/VMware; Spring Framework itself 2003)
license: Apache-2.0
homepage: "https://spring.io/projects/spring-boot"
github-repo: "spring-projects/spring-boot"
status: thin
provenance: "field-grounded (3 contributing writeups gated by command-context markers — /actuator paths in fuzz output, heapdump/OQL analysis, whitelabel error pages, spring.cloud.function routing-expression headers; exclusions: a Spring footer string on a page whose exploit is pure FreeMarker SSTI, a 'whitelabeling' config key that belongs to GitLab, and a java-rmi machine whose spring-boot lines are only pom.xml artifact IDs) + upstream-verified (spring.io advisory for CVE-2022-22963, Spring Cloud Function/Eureka documentation, JetBrains-independent actuator documentation). Thin unit, first-class treatment: the actuator surface and the CVE mechanics come upstream-verified, the chains come from the field. Enrichment pass 2, 2026-11-12."
---

# Spring Boot

> One fat JAR that is web app, admin console, and memory dump in a single process — and whose management endpoints, exposed by a single missing flag, will hand you the route table, the live sessions, and a 70-megabyte core dump of the JVM's secrets.

## What it is

Spring Boot (2014; Apache-2.0) packages a Spring application plus an embedded Tomcat into a single executable JAR — `java -jar app.jar` and it serves HTTP on 8080, no servlet container to install. Production deployments hide it behind nginx; microservice deployments wrap it in the Spring Cloud family — a gateway that routes by path to services resolved from a Eureka registry, each service carrying the same embedded-Tomcat core. Two defaults matter to an attacker: the **whitelabel error page** (`Whitelabel Error Page`, served on any unhandled exception) that fingerprints the stack instantly, and the **actuator module** — management endpoints (`/actuator/*`) that in a misconfigured deployment are mounted on the main port with no authentication.

The bug profile is a catalog, not a craft: exposed actuator endpoints leaking sessions, environment, and heap; the Spring Cloud Function SpEL injection (CVE-2022-22963) that is a single header away from RCE; service-registry trust in Eureka deployments that lets you register yourself as the login service; and, once you have a shell, a fat JAR whose `BOOT-INF/classes/application.properties` holds the database credentials in cleartext.

## The anatomy that matters on an engagement

    client → nginx (80) → embedded Tomcat (8080) → @Controller routes
                                          ↘ /actuator/* management endpoints (sessions, env, heapdump)
    microservices: gateway → lb://SERVICE-NAME → Eureka registry (8761) → instance list
    fat JAR on disk: app.jar → BOOT-INF/classes/application.properties (credentials)

Three structural properties drive most findings:

- **Actuator is one flag from catastrophic.** `management.endpoints.web.exposure.include=*` plus no auth mounts route maps, live sessions, environment variables, and the heapdump on the public port.
- **The gateway trusts the registry.** With `lb://` URIs, the gateway asks Eureka where `USER-MANAGEMENT-SERVICE` lives — and Eureka's registration API is happy to answer "your host."
- **Everything needed to exploit a version is in the build metadata.** A leaked `pom.xml` prints dependency versions; the SpEL injection is version-gated and reads straight out of it.

## Where you'll meet it

- **Java shops' external-facing services** — booking, hosting, and product sites where a directory scan plus the spring-boot wordlist finds `/actuator` mounted by accident.
- **Microservice estates** — an edge service (nginx → gateway) with a Eureka registry on 8761 behind weak or heapdump-derived credentials; the estate's internal trust (registry, gateway load balancing) is the target, not any single app's logic.
- **Internal/admin tooling** — anything with a `/login` and a stack-trace habit; the whitelabel page plus a verbose error is often the only recon you need.
- **Build-and-ship pipelines** — the same fat JARs on disk at `/app` or `/var/www`, one `unzip` away from `application.properties` and Maven's `~/.m2/settings.xml` with the next credential in the chain.

## Reading the framework's mind

- **The whitelabel page is a confession.** Any 404 or 500 rendering it means Spring Boot; re-run content discovery with a Spring Boot wordlist immediately.
- **`/actuator/mappings` is the route table.** It lists every handler — including admin paths the UI never links; `/actuator/sessions` lists live session IDs you can set as your own cookie.
- **`/actuator/heapdump` is a credential archive.** The HPROF file contains every `java.lang.String` ever alive — query it with OQL for `password` before you do anything else.
- **SpEL headers are code.** `spring.cloud.function.routing-expression` in Spring Cloud Function ≤ 3.2.2 is evaluated as an expression — a header-named RCE on any `/functionRouter`-style route.
- **The JAR is the config store.** `BOOT-INF/classes/application.properties`, `application.yml`, and the Maven tree in `META-INF` beat an hour of source review.

```http
POST /functionRouter HTTP/1.1
Host: example.com
spring.cloud.function.routing-expression: T(java.lang.Runtime).getRuntime().exec("id")
Content-Length: 0
# ^ a single header; versions 3.1.6/3.2.2 and older evaluate it as SpEL (CVE-2022-22963)
```

## Where to go next

The actuator enumeration order, heapdump mining, the SpEL injection, Eureka instance injection, and fat-JAR loot are in [[spring-boot-bug-bounty]].
