---
date: 2017-03-23T13:19:38Z
title: Tyk Sync
menu: 
  main:
    parent: Tyk Stack
weight: 4
aliases:
  - /advanced-configuration/manage-multiple-environments/tyk-sync/
---

Tyk Sync is a command-line tool and Go library for synchronising API definitions and Security Policies from a Git repository or file system into Tyk. It allows for the versioning of Tyk configurations to Git or files, as well as one-way sync from Git or files to Tyk. 

Tyk Sync works with Git and any CI/CD tool to ensure that only versioned changes are loaded into your Tyk Environment.  For example, a developer can configure and test the APIs locally, and then use *tyk-sync dump* to convert the APIs to transportable format. Following Git standard practises, to load this change to an environment, he/she should create a Pull Request (PR) on Git for a peer review and merge the approved API configurations in Git. Once the Pull Request is approved and merged, the deployment pipeline could be triggered to run *tyk-sync sync*, *tyk-sync publish* or *tyk-sync update* to sync configurations from Git to the target Tyk installation. See the [Dump](#dump-command) command for how to extract the current Tyk configurations from an installation. See  [Sync](#sync-command), [Update](#update-command), or [Publish](#publish-command) commands for they can help to sync configurations from Git to target Tyk installation.

Tyk Sync does not work with keys. Please consult [move keys between environments]({{< ref "advanced-configuration/manage-multiple-environments/move-keys-between-environments" >}}) for further details.

## Features

Tyk Sync works with the Open Source *Tyk Gateway* and *Tyk Dashboard* installation. The table below shows features available for each installation type.

| Tyk Sync Feature                                                           | Tyk Gateway (OSS) | Tyk Dashboard (Licensed) | Example |
| ---------------------------------------------------------------------------|---------|---------------|---------|
| Dump APIs and Policies in a transportable format from Tyk to a directory   | ❌     | ✅            | [Example: Dump a specific API from one Tyk Dashboard](#example-dump-a-specific-api-from-one-tyk-dashboard) |
| Update APIs                                                                | ✅     | ✅            |         |
| Update Policies                                                            | ❌     | ✅            |         |
| Publish APIs                                                               | ✅     | ✅            |         |
| Publish Policies                                                           | ❌     | ✅            |         |
| Synchronise APIs with those stored in a VCS (one-way: from VCS to Tyk)     | ✅     | ✅            | [Example: Transfer APIs from one Tyk Dashboard to another](#example-transfer-from-one-tyk-dashboard-to-another) |
| Synchronise Policies with those stored in a VCS (one-way: from VCS to Tyk) | ❌     | ✅            |         |
| Support for importing, converting and publishing Swagger/OpenAPI JSON files as Tyk Classic APIs (OpenAPI 2.0 and 3.0 are supported) | ✅     | ✅            |         |
| Support for Tyk Classic and Tyk OAS APIs (see note below)                  | ✅     | ✅            |         |
| Specialised support for Git                                                | ✅     | ✅            |         |
| Show and import [Tyk examples](https://github.com/TykTechnologies/tyk-examples) | ✅     | ✅            | [Example: Import Tyk Example into Dashboard](#example-import-tyk-example-into-dashboard) |


{{< note success >}}
**Note**  

Tyk Sync supports both [Tyk OAS APIs]({{< ref "getting-started/key-concepts/high-level-concepts" >}}) and [Tyk Classic APIs]({{< ref "getting-started/key-concepts/what-is-an-api-definition/#api-definition-types" >}}) when working with Tyk Dashboard, however at this time you must set the [allow-unsafe-oas]({{< ref "tyk-dashboard/configuration#allow_unsafe_oas" >}}) configuration in Dashboard, and the flag `--allow-unsafe-oas` when invoking Tyk Sync if you want to use Tyk Sync to migrate Tyk OAS APIs.

API Category is not currently supported in Tyk Sync for Tyk OAS APIs.

`--allow-unsafe-oas` is a flag that is required for Tyk Sync to work with Tyk OAS APIs. This is a temporary measure provided for early adopters and will be deprecated later when Tyk Sync will be updated in a future release to bring you the full Tyk OAS API experience. 
{{< /note >}}

### Sync

Tyk Sync tries to be clever about what APIs and Policies to update and which to create, it will actually base all
ID matching on the API ID and the masked Policy ID, so it can identify the same object across installations. Tyk has
a tendency to generate fresh IDs for all new Objects, so Tyk Sync gets around this by using portable IDs and ensuring
the necessary portable IDs are set when using the `dump` command.

This means that Tyk Sync can be used to back-up your most important API Gateway configurations as code, and to deploy
those configurations to any target and ensure that API IDs and Policy IDs will remain consistent, ensuring that any
dependent tokens continue to have access to your services.

### Prerequisites:

- In order for policy ID matching to work correctly, your Dashboard must have `allow_explicit_policy_id: true` and `enable_duplicate_slugs: true` and your Gateway must have `policies.allow_explicit_policy_id: true`.
- It is assumed you have a Tyk CE or Tyk Pro installation.

## Installation

Currently the application is available via [Docker](https://hub.docker.com/r/tykio/tyk-sync) and [Packagecloud](https://packagecloud.io/tyk/tyk-sync).

### Docker:

To install a particular version of `tyk-sync` via docker image please run the command bellow with the appropriate version you want to use. All available versions could be found on the Tyk Sync Docker Hub page here: https://hub.docker.com/r/tykio/tyk-sync/tags
```{.copyWrapper}
docker pull tykio/tyk-sync:{version_id}
```
To run `tyk-sync` as a one-off command and display usage options please do:
```{.copyWrapper}
docker run -it --rm tykio/tyk-sync:{version_id} help
```
Then the docker image `tyk-sync` can be used in the following way:
```{.copyWrapper}
docker run -it --rm tykio/tyk-sync:{version_id} [flags]
docker run -it --rm tykio/tyk-sync:{version_id} [command]
```
As per the examples below `tyk-sync` will need access to the host file sytem to read and write files.  You can use docker bind mounts to map files in the container to files on your host machine.

## Usage

```
Usage:
  tyk-sync [flags]
  tyk-sync [command]

Available Commands:
  dump        Dump will extract policies and APIs from a target (Tyk Dashboard)
  examples    Shows a list of all available tyk examples
  help        Help about any command
  publish     publish API definitions from a Git repo or file system to a Tyk Gateway or Dashboard
  sync        Synchronise a github repo or file system with a Tyk Gateway
  update      Update a Tyk Dashboard or Gateway with APIs and policies
  version     This command will show the current Tyk-Sync version

Flags:
  -h, --help   help for tyk-sync

Use "tyk-sync [command] --help" for more information about a command.
```

### Dump Command

Dump will extract policies and APIs from a target (your Dashboard) and place them in a directory of your choosing. It will also generate a spec file that can be used for syncing.

```

Usage:
  tyk-sync dump [flags]
Flags:
  -b, --branch string      Branch to use (defaults to refs/heads/master) (default "refs/heads/master")
  -d, --dashboard string   Fully qualified Tyk Dashboard target URL
  -h, --help               help for dump
  -k, --key string         Key file location for auth (optional)
  -s, --secret string      Your API secret
  -t, --target string      Target directory for files
      --policies           Specific policies ID selection (optional)
      --apis               Specific api_id's selection (optional)
```

API secret refers to secret use to access your Gateway API or Dashboard API. For dashboard users, you can get it from "User" page under “Tyk Dashboard API Access key”.

### Publish Command

Publish API definitions from a Git repo to a Tyk Gateway or Dashboard. This will not update any existing APIs, and if it detects a collision, the command will stop.

```
Usage:
  tyk-sync publish [flags]
Flags:
  -b, --branch string      Branch to use (defaults to refs/heads/master) (default "refs/heads/master")
  -d, --dashboard string   Fully qualified Tyk Dashboard target URL
  -g, --gateway string     Fully qualified Tyk Gateway target URL
  -h, --help               help for publish
  -k, --key string         Key file location for auth (optional)
  -p, --path string        Source directory for definition files (optional)
  -s, --secret string      Your API secret
      --test               Use test publisher, output results to stdio
      --policies           Specific policies ID selection (optional)
      --apis               Specific api_id's selection (optional)
      --allow-unsafe-oas   Use Tyk Classic endpoints in Tyk Dashboard API for Tyk OAS APIs (optional)
```

API secret refers to secret use to access your Gateway API or Dashboard API. For dashboard users, you can get it from "User" page under “Tyk Dashboard API Access key”.

### Sync Command

Sync will synchronise an API Gateway with the contents of a Github repository. The sync is one way - from the repo to the Gateway, the command will not write back to the repo. Sync will delete any objects in the Dashboard or Gateway that it cannot find in the github repo, and update those that it can find and create those that are missing.

```
Usage:
tyk-sync sync [flags]
Flags:
-b, --branch string      Branch to use (defaults to refs/heads/master) (default "refs/heads/master")
-d, --dashboard string   Fully qualified Tyk Dashboard target URL
-g, --gateway string     Fully qualified Tyk Gateway target URL
-h, --help               help for sync
-k, --key string         Key file location for auth (optional)
-o, --org string         org ID override
-p, --path string        Source directory for definition files (optional)
-s, --secret string      Your API secret
    --test               Use test publisher, output results to stdio
    --policies           Specific policies ID selection (optional)
    --apis               Specific api_id's selection (optional)
    --allow-unsafe-oas   Use Tyk Classic endpoints in Tyk Dashboard API for Tyk OAS APIs (optional)
```

API secret refers to secret use to access your Gateway API or Dashboard API. For dashboard users, you can get it from "User" page under “Tyk Dashboard API Access key”.

### Update Command

Update will attempt to identify matching APIs or Policies in the target, and update those APIs. It does not create new ones. Use `tyk-sync publish` or `tyk-git sync` for new content.

```
Usage:
tyk-sync update [flags]
Flags:
-b, --branch string      Branch to use (defaults to refs/heads/master) (default "refs/heads/master")
-d, --dashboard string   Fully qualified Tyk Dashboard target URL
-g, --gateway string     Fully qualified Tyk Gateway target URL
-h, --help               help for update
-k, --key string         Key file location for auth (optional)
-p, --path string        Source directory for definition files (optional)
-s, --secret string      Your API secret
    --test               Use test publisher, output results to stdio
    --policies           Specific policies ID selection (optional)
    --apis               Specific api_id's selection (optional)
    --allow-unsafe-oas   Use Tyk Classic endpoints in Tyk Dashboard API for Tyk OAS APIs (optional)
```

API secret refers to secret use to access your Gateway API or Dashboard API. For dashboard users, you can get it from "User" page under “Tyk Dashboard API Access key”.

### Examples Command

The examples command lists all examples from our official [Tyk examples](https://github.com/TykTechnologies/tyk-examples) repository. [See output in example usage]({{< relref "#example-import-tyk-example-into-dashboard" >}})
```{.copyWrapper}
Usage:
  tyk-sync examples [flags]
  tyk-sync examples [command]

Available Commands:
  publish     Publish a specific example to a gateway or dashboard by using its location
  show        Shows details of a specific example by using its location

Flags:
  -h, --help   help for examples
```

### Examples Show Command
Shows more details about a specific example by using its location. [See output in example usage]({{< relref "#example-import-tyk-example-into-dashboard" >}})
```{.copyWrapper}
Usage:
  tyk-sync examples show [flags]

Flags:
  -h, --help              help for show
  -l, --location string   Location to example
```

### Examples Publish Command
Publishs an example by using its location.
```{.copyWrapper}
Usage:
  tyk-sync examples publish [flags]

Flags:
  -b, --branch string      Branch to use (defaults to refs/heads/main) (default "refs/heads/main")
  -d, --dashboard string   Fully qualified dashboard target URL
  -g, --gateway string     Fully qualified gateway target URL
  -h, --help               help for publish
  -k, --key string         Key file location for auth (optional)
  -l, --location string    Location to example
  -s, --secret string      Your API secret
      --test               Use test publisher, output results to stdio
```

API secret refers to secret use to access your Gateway API or Dashboard API. For dashboard users, you can get it from "User" page under “Tyk Dashboard API Access key”.

## Example: Transfer from one Tyk Dashboard to another

First, you need to extract the data from our Tyk Dashboard. Here you `dump` into ./tmp. Let's assume this is a git-enabled
directory

```{.copyWrapper}
tyk-sync dump -d="http://localhost:3000" -s="b2d420ca5302442b6f20100f76de7d83" -t="./tmp"
Extracting APIs and Policies from http://localhost:3000
> Fetching policies
--> Identified 1 policies
--> Fetching and cleaning policy objects
> Fetching APIs
--> Fetched 3 APIs
> Creating spec file in: tmp/.tyk.json
Done.
```

If running `tyk-sync` in docker the command above would read

```{.copyWrapper}
docker run --rm --mount type=bind,source="$(pwd)",target=/opt/tyk-sync/tmp \
 tykio/tyk-sync:v1.2 \
 dump \
 -d="http://host.docker.internal:3000" \
 -s="b2d420ca5302442b6f20100f76de7d83" \
 -t="./tmp"
```

Next, let's push those changes back to the Git repo on the branch `my-test-branch`:

```{.copyWrapper}
cd tmp
git add .
git commit -m "My dashboard dump"
git push -u origin my-test-branch
```

Now to restore this data directly from GitHub:

```{.copyWrapper}
tyk-sync sync -d="http://localhost:3010" -s="b2d420ca5302442b6f20100f76de7d83" -b="refs/heads/my-test-branch" https://github.com/myname/my-test.git
Using publisher: Dashboard Publisher
Fetched 3 definitions
Fetched 1 policies
Processing APIs...
Deleting: 0
Updating: 3
Creating: 0
SYNC Updating: 598ec94f9695f201730d835b
SYNC Updating: 598ec9589695f201730d835c
SYNC Updating: 5990cfee9695f201730d836e
Processing Policies...
Deleting policies: 0
Updating policies: 1
Creating policies: 0
SYNC Updating Policy: Test policy 1
--> Found policy using explicit ID, substituting remote ID for update
```

If running `tyk-sync` in docker the command above would read

```{.copyWrapper}
docker run --rm \
  --mount type=bind,source="$(pwd)",target=/opt/tyk-sync/tmp \
 tykio/tyk-sync:v1.2 \
  sync \
  -d="http://localhost:3010" \
  -s="b2d420ca5302442b6f20100f76de7d83" \
  -b="refs/heads/my-test-branch" https://github.com/myname/my-test.git
```

The command provides output to identify which actions have been taken. If using a Tyk Gateway, the Gateway will be
automatically hot-reloaded.

## Example: Dump a specific API from one Tyk Dashboard  

First, we need to identify the `api_id` that we want to dump, in this case `ac35df594b574c9c7a3806286611d211`.
When we have that, we are going to execute the dump command specifying the `api_id` in the tags.
```
tyk-sync dump -d="http://localhost:3000" -s="b2d420ca5302442b6f20100f76de7d83" -t="./tmp" --apis="ac35df594b574c9c7a3806286611d211"
Extracting APIs and Policies from http://localhost:3000
> Fetching policies
--> Identified 0 policies
--> Fetching and cleaning policy objects
> Fetching APIs
--> Fetched 1 APIs
> Creating spec file in: tmp/.tyk.json
Done.
```

If you want to specify more than one API, the values need to be comma-separated.
For example `--apis="ac35df594b574c9c7a3806286611d211,30e7b4001ea94fb970c324bad1a171c3"`.

The same behaviour applies to policies.

## Example: Check the currently installed version of Tyk Sync

To check the current Tyk Sync version, we need to run the version command:


```
tyk-sync version
v1.2
```

## Example: Import Tyk example into Dashboard

To list all available examples you need to run this command:
```{.copyWrapper}
tyk-sync examples
LOCATION           NAME                               DESCRIPTION
udg/vat-checker    VAT number checker UDG             Simple REST API wrapped in GQL using Universal Data Graph that allows user to check validity of a VAT number and display some details about it.
udg/geo-info       Geo information about the World    Countries GQL API extended with information from Restcountries
```

It's also possible to show more details about an example by using its location. For example, based on the output from `tyk-sync examples` above, we can use the location of the example "VAT number checker UDG" to get more information:
```{.copyWrapper}
tyk-sync examples show --location="udg/vat-checker"
LOCATION
udg/vat-checker

NAME
VAT number checker UDG

DESCRIPTION
Simple REST API wrapped in GQL using Universal Data Graph that allows user to check validity of a VAT number and display some details about it.

FEATURES
- REST Datasource

MIN TYK VERSION
5.0
```

To publish it into the Dashboard you will need to use this command:
```{.copyWrapper}
tyk-sync examples publish -d="http://localhost:3000" -s="b2d420ca5302442b6f20100f76de7d83" -l="udg/vat-checker"
Fetched 1 definitions
Fetched 0 policies
Using publisher: Dashboard Publisher
org override detected, setting.
Creating API 0: vat-validation
--> Status: OK, ID:726e705e6afc432742867e1bd898cb26
Updating API 0: vat-validation
--> Status: OK, ID:726e705e6afc432742867e1bd898cb26
org override detected, setting.
Done
```
