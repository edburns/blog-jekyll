---
layout: post
title:  "Using GitHub Copilot to stand up Shaarli on Azure Container Apps and patch the PHP2Shell vuln"
date:   2026-10-22 14:00-0800
comments: true
---

## Executive summary

I used GitHub Copilot to help deploy my own [Shaarli](https://github.com/shaarli/Shaarli) bookmark manager on [Azure Container Apps](https://learn.microsoft.com/en-us/azure/container-apps/overview). Several weeks later, the PHP runtime in that container needed an urgent security update. This post tells both parts of the story: getting something useful running, and taking responsibility for keeping it running safely.

The code is in [edburns/F3-2666-shaarli](https://github.com/edburns/F3-2666-shaarli). The mitigation is captured in [commit a095c1e](https://github.com/edburns/F3-2666-shaarli/commit/a095c1eb8a992ea5c0a011e752266bc0ca37fdcb).

As with my [JavaLand 2026 picks post]({{ site.url }}/blog/2026/02/13/javaland-2026-picks.html), I also used AI to help draft this post from the conversation logs. The interesting part is not that AI can write Bash. It is what happened when we ran the Bash, what we had to correct, and what evidence we collected afterward.

## Problem statement: the Shaarli lifestyle

I had 4,611 bookmarks to move out of Diigo. I wanted to preserve tags and have a convenient way to save the page I was reading. There was a substantial detour through Instapaper import formats and APIs, but that is another story. Here I want to concentrate on the self-hosted option.

On August 29, 2026, I gave Copilot this assignment:

> In this session I want to create durable scripts in this current branch of this current repository to bootstrap myself into what I will call "The Shaarli lifestyle".

The requirements were deliberately modest:

* A private bookmark manager, reachable over the public Internet, for one user.
* Azure Container Apps, without a Kubernetes cluster for me to administer.
* No custom DNS. An opaque `azurecontainerapps.io` hostname was fine.
* Persistent bookmarks and configuration.
* Scale-to-zero. I did not want to pay for a constantly running replica.
* Scripts to create, clone, and delete an instance.

The opaque hostname was a convenience, not an access-control mechanism. Authentication still had to do the actual work.

## Standing it up

### The architecture

Copilot produced four Bash files: `create-shaarli.sh`, `clone-shaarli.sh`, `delete-shaarli.sh`, and the shared `lib-shaarli.sh`.

Each logical instance gets its own resource group, Container Apps environment, Container App, Azure Storage account, Azure Files share, and Microsoft Entra app registration. The logical name `personal` maps to the resource group `rg-shaarli-personal`; resource-group tags tell the scripts which randomly named resources belong to it.

The request path looks like this:

```text
Browser
  -> Container Apps HTTPS ingress
  -> Microsoft Entra authentication, restricted to one principal
  -> Shaarli's web server and PHP-FPM
  -> Azure Files mounted at /var/www/shaarli/data
```

Container Apps' [built-in authentication](https://learn.microsoft.com/en-us/azure/container-apps/authentication), often called Easy Auth, provided the outer gate. Shaarli retained its own login as a second layer. The provisioning script created the app with internal ingress, configured authentication and the principal allow-list, and only then enabled public HTTPS ingress.

The replica bounds were zero to one, with single-revision mode. The persistent share was the important part: configuration and the bookmark datastore must outlive a replica. Cache and temporary files could remain ephemeral.

The user-facing commands were pleasantly small:

```bash
./scripts/create-shaarli.sh personal
./scripts/clone-shaarli.sh personal personal-copy
./scripts/delete-shaarli.sh personal
```

Those are three separate lifecycle operations, not a sequence to copy and run! The last one deletes the instance and its data.

### The first deployment did not work

The initial Container App name was too long. Azure had already created the resource group, storage, and environment when it rejected the app name.

Static Bash checks had passed. Azure still said no.

We shortened the random suffix to 11 bytes, giving a 30-character app name, removed the incomplete estate, and reran creation. We also made restarting the private revision after installing the authentication secret part of provisioning.

I mention this because "Copilot wrote the scripts" and "the scripts successfully deployed the system" are different milestones. We reached the second one by looking at the failure and correcting the implementation.

### Getting the bookmarks in

A Pocket-style HTML file that another importer accepted was rejected by Shaarli. Copilot converted it to Netscape bookmark HTML, preserving titles, timestamps, and tags, and marking every bookmark private.

The useful evidence was the importer result:

```text
4611 bookmarks imported, 0 bookmarks overwritten, 0 bookmarks skipped.
```

The generated import file contained personal and internal URLs, so it was locally excluded from Git. Application data did not belong in the infrastructure repository.

### Getting the bookmarklets right

In September I tried the built-in bookmarklet and observed a URL beginning with `https://admin/shaare`. That ain't right.

Behind the Container Apps ingress and authentication layer, Shaarli was not deriving the external root URL correctly. We added `set-root-url.sh` to repair `general.root_url` in the persisted `config.json.php`. For future instances, the library computes the public URL from the app name and the environment's default domain, then seeds the configuration before first boot.

```bash
./scripts/set-root-url.sh personal --show
./scripts/set-root-url.sh personal
```

The argument is the logical instance name, not the full hostname. I managed to get that wrong too.

After repairing the root URL, I deleted and re-created the bookmarklets. Dragging them from the Tools page avoided another problem: copying the link address had percent-encoded the JavaScript quotes. On September 6 I confirmed that both bookmarklets worked.

### Keeping scale-to-zero

I was explicit about the trade-off:

> I do want to keep scale-to-0. I do not want to pay for a constantly running replica. I don't mind a bit of cold-start time. But I'd like to minimize it.

We raised the resource allocation from 0.25 vCPU and 0.5 GiB to 0.5 vCPU and 1 GiB, and selected `none` for thumbnails. We left the replica bounds alone. This was not a claim that the whole installation was free; storage and other Azure charges still apply.

Some proposed performance improvements did not survive scrutiny. In particular, moving compiled templates to Azure Files would put more reads on network storage. We left them local rather than treating every cache as something that must be persisted.

There was also a useful measurement lesson: an unauthenticated `curl` returning 401 might only be measuring Easy Auth. To measure a Shaarli cold start, I needed an authenticated request that actually reached the application.

## Then came PHP2Shell

On October 22, 2026, I came back with a different problem: this system uses PHP, and the newly disclosed PHP2Shell vulnerabilities might apply to it.

The two PHP advisories, published October 21, describe critical remote-code-execution vulnerabilities:

| Vulnerability | Advisory | Fixed version for our PHP branch |
|---------------|----------|----------------------------------|
| CVE-2026-109618 | [RCE via out-of-memory corruption](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w) | PHP 8.4.27 |
| CVE-2026-109619 | [RCE via stale global state](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp) | PHP 8.4.27 |

The stale-state advisory includes a validated PHP-FPM path, although exploitation depends on suitable application behavior and subsequent requests reaching the same worker. The out-of-memory advisory describes FPM as susceptible based on source analysis, but does not claim a demonstrated complete FPM exploit. Those distinctions matter. "Uses PHP" is a reason to investigate and patch, not proof that somebody has exploited this particular app.

The [Shaarli maintainer discussion in shaarli/Shaarli#2297](https://github.com/shaarli/Shaarli/issues/2297) and the [v0.16.10 release notes](https://github.com/shaarli/Shaarli/releases/tag/v0.16.10) identified the rebuilt container containing the fixes.

### An authentication gate is not a runtime patch

Our public endpoint still returned HTTP 401, and the configuration still required Entra authentication with one allowed principal. That was good news: an arbitrary anonymous request was not getting through to PHP.

But it did not establish that PHP was patched.

Nor was Shaarli's own login an adequate reason to ignore a runtime vulnerability. The PHP advisories concern request processing and worker state, not just application operations after login. We kept Easy Auth and patched the container.

### A mutable tag is not evidence

The original deployment used:

```text
ghcr.io/shaarli/shaarli:release
```

By the time we investigated, that tag pointed to a rebuilt image. That still did not tell us which bytes an existing replica had started with.

We changed to an explicit version and immutable image digest:

```text
ghcr.io/shaarli/shaarli:v0.16.10@sha256:48a1a8962c58c74c5c36d4927a7c2dcb5bf191a93b83dc480d9827f383ada903
```

Earlier in the performance discussion, the assistant had backed away from digest pinning because its cold-start benefit was speculative. Here the reason was different: I needed to identify exactly what we deployed. That was worth the obligation to deliberately update the pin next time.

## Patching the running instance

The live mitigation changed the container image, not the storage account, file share, mount path, authentication, or replica bounds. The resulting revision ended in `--0000002`; it became healthy and latest-ready, and the two older revisions were inactive and stopped.

The repository now has two additional scripts:

* [`update-shaarli-image.sh`](https://github.com/edburns/F3-2666-shaarli/blob/a095c1eb8a992ea5c0a011e752266bc0ca37fdcb/scripts/update-shaarli-image.sh), which requires an immutable digest, checks authentication, snapshots the data share before an image change, and waits for the rollout.
* [`attest-shaarli-security.sh`](https://github.com/edburns/F3-2666-shaarli/blob/a095c1eb8a992ea5c0a011e752266bc0ca37fdcb/scripts/attest-shaarli-security.sh), which reports the deployment and authentication state and can check the PHP runtime and internal HTTP endpoint.

The intended workflow for subsequent updates is:

```bash
./scripts/update-shaarli-image.sh personal
```

Then sign in through the browser to wake the application if it has scaled to zero, and run this from an interactive terminal:

```bash
./scripts/attest-shaarli-security.sh personal --runtime
```

The interactive-terminal requirement was another lived-experience correction. `az containerapp exec` failed in non-TTY execution. During this session we used macOS's `script` command to provide a pseudo-terminal:

```bash
script -q /dev/null az containerapp exec \
  --name "$APP" \
  --resource-group "$RG" \
  --command 'php84 --version'
```

The remote container reported:

```text
PHP 8.4.27 (cli) (built: Oct 21 2026 16:38:20) (NTS)
```

We also caught a bug in our rollout-wait logic: it incorrectly read Azure CLI's multi-value TSV output. Querying the latest and latest-ready revision names separately fixed it. Again, a successful update and a working verification script are different things.

## What about my data?

During the mitigation I added an explicit requirement:

> Make sure the data in the current Shaarli instance is not damaged in any way. The system must remain usable without any loss of data after the mitigation is complete.

We compared the old and new revision definitions. Both mounted the same Azure Files volume at `/var/www/shaarli/data`.

We then took a safety snapshot, downloaded the snapshot and live share independently, and compared every file by relative path, size, and SHA-256 digest. We repeated the comparison after the internal HTTP check.

```text
files=7
bytes=1677883
byteForByteMatch=true
```

Azure Files metadata showed that the only file with a modification timestamp at or after rollout was `lastupdatecheck.txt`, a seven-byte update-check file. The other persistent files retained their pre-rollout modification timestamps.

**An important qualification:** the recorded snapshot was taken at 18:34:01 UTC, after the new revision was created at 18:19:30 UTC. It was not a pre-upgrade backup, and a comparison against it cannot prove the entire pre-upgrade state was identical. The unchanged mounts and file metadata are additional evidence, not a substitute for a before-image.

We corrected the durable upgrade script to take its snapshot *before* future image changes. For an active application, I would also coordinate a quiet period and check restoreability rather than assume that any snapshot proves an application-consistent backup.

We did not create or delete test bookmarks during the incident validation. The application check was deliberately read-only.

## How can I convince a skeptic?

That was my next question to Copilot. I wanted evidence a person other than the assistant could reproduce.

From the repository directory, these commands identify the instance without embedding my subscription or tenant identifiers:

```bash
RG=rg-shaarli-personal
APP=$(az group show -n "$RG" --query tags.shaarliAppName -o tsv)

az account show --query '{name:name,id:id,tenantId:tenantId}' -o json

az containerapp show -g "$RG" -n "$APP" \
  --query '{image:properties.template.containers[0].image,latest:properties.latestRevisionName,ready:properties.latestReadyRevisionName}' \
  -o json

az containerapp revision list -g "$RG" -n "$APP" --all \
  --query '[].{name:name,active:properties.active,health:properties.healthState,running:properties.runningState,image:properties.template.containers[0].image}' \
  -o table
```

Checking the Azure context first is not busywork. Early in our investigation, the CLI was authenticated to a different tenant and could not see the instance.

The registry side can be checked without starting a local Shaarli container:

```bash
docker buildx imagetools inspect ghcr.io/shaarli/shaarli:v0.16.10
```

The chain of evidence is:

1. The upstream PHP advisories identify the fixed versions.
2. Shaarli's release notes identify the rebuilt release.
3. The registry digest identifies the reviewed image.
4. Azure's revision configuration identifies that same digest, with the older revisions inactive.
5. An exec session into the named Azure revision reports the patched PHP CLI version.
6. The public authentication gate still rejects anonymous traffic.
7. A request inside the remote container receives a successful HTTP response.

There are limits to this evidence. `php84 --version` checks the CLI binary, not independently the PHP-FPM worker. For a stronger attestation, inspect `php84-fpm`'s installed package version and the FPM executable/process as well. An internal HTTP response is not a complete authenticated browser workflow or a write/restore test. And patching these two vulnerabilities does not establish that the system was never previously compromised.

Those are reasons to describe the evidence precisely, not reasons to prefer a dangerous exploit test against the live bookmark store. A failed exploit attempt could simply mean its prerequisites were not met.

The [committed live attestation](https://github.com/edburns/F3-2666-shaarli/blob/a095c1eb8a992ea5c0a011e752266bc0ca37fdcb/security/2026-10-22-php2shell/live-post-change-attestation.md) records what we observed. The [assessment and implementation plan](https://github.com/edburns/F3-2666-shaarli/tree/a095c1eb8a992ea5c0a011e752266bc0ca37fdcb/security/2026-10-22-php2shell) capture the broader work.

## What I learned

Copilot helped me cross several boundaries I do not work at every day: PHP packaging, Container Apps authentication, Azure Files, and remote runtime inspection. It also made mistakes at those boundaries. I had to keep asking whether a command checked the thing we actually cared about.

The biggest improvement was not a clever prompt. It was turning the work into artifacts: scripts, an immutable deployment identity, a data snapshot, and a written record with explicit limits.

I now have a useful bookmark manager and a more repeatable way to update it. I also have one more reminder that operating software does not end when the first deployment succeeds.

### Appendix: prompts worth keeping

These are the short prompts I would reuse, with the application and vulnerability names changed as appropriate:

> Research the vuln, the extent to which my Shaarli instance is vulnerable to it, and make a plan for patching it so that the instance is no longer vulnerable.

> Make sure the data in the current Shaarli instance is not damaged in any way.

> How can I convince a skeptic that the PHP2Shell vulns have actually been successfully mitigated?

Keep the raw operational logs private until they have been reviewed and redacted. They can contain credentials and application data. A blog post needs the relevant commands and evidence, not an unfiltered transcript.
