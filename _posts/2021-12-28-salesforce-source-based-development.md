---
layout: post
title: "Salesforce source-based development"
date: 2021-12-28
author: ehsan
categories: [Salesforce, SFDX]
excerpt_separator: <!--more-->
---

Salesforce DX allows developers to use Git and source repositories when doing any sorts of development on Salesforce platform.

<br>

## Setup

**Scratch orgs** are a central feature of Salesforce DX. The Scratch org is a source-driven and disposable deployment of Salesforce code and metadata.

**Dev Hub** allows you to create and manage scratch orgs.
It is also essential create and manage second-generation packages, and use Einstein features.

<br>

### Install SFDX CLI

Open this link: https://developer.salesforce.com/tools/sfdxcli download and install the CLI compatible with your OS.

To check if the CLI has been installed correctly, type this in command prompt:

```bash
$ sfdx --version

sfdx-cli/7.132.0 win32-x64 node-v16.13.1
```

And to check for installed salesforce dx plugins:

```
$ sfdx plugins --core

@oclif/plugin-autocomplete 0.3.0 (core)
@oclif/plugin-commands 1.3.0 (core)
...
```

<br>

#### Update SFDX CLI
Occasionally you might need to update your CLI in order to work with the latest improvements.

```
$ sfdx update
```

<br>

### Sign up for Dev Hub account
Navigate to https://developer.salesforce.com/promotions/orgs/dx-signup. Here you can sign-up for a 30 day trial Dev Hub.

In real world production, you will use a permanent Dev Hub.

This org is already configured to act as a Dev Hub. However, to convert a normal org to a Dev Hub you should follow these steps:

1. Setup
2. Development -> Dev Hub (or search Dev Hub)
3. Enable Dev Hub

<br>

### Connect Dev Hub and CLI
We need to authenticate with DevHub so the SFDX can track it in the future:

```bash
$ sfdx force:auth:web:login -d -a DefaultHub
```

`-d` flag is to define a DevHub and `-a` will set an alias for it.

After a successful authentication we can use this command to view the list of our orgs:

```bash
$ sfdx force:org:list

=== Orgs
     ALIAS       USERNAME                           ORG ID              CONNECTED STATUS
───  ──────────  ─────────────────────────────────  ──────────────────  ────────────────
(D)  DefaultHub  ***@***.com  00D5g000004FOzsEAG  Connected
```

The `--all` flag can be used to view the expired orgs as well.

**Note**: Developer Edition DevHub is only valid for 30 days and a new DevHub must be created afterwards.

|     | ALIAS | USERNAME | ORG ID | CONNECTED STATUS |
| --- | ----- | -------- | ------ | ---------------- |
| (D) | DefaultHub | abc@xyz.com | 00D5g000004jAhWEAU | RefreshTokenAuthError |

Logout in order to clean up the unwanted DevHub (non-Scratch) orgs:

```bash
$ sfdx force:auth:logout -u ***@***.com
```

**Note**: remember to Enable Dev Hub on the newly created Org.

<br>

### Salesforce DX project

To begin we need to create a DX project:

Create project
For this purpose we will use `create` within the `project` commands:

```bash
$ sfdx force:project:create -n my-project
```

- We can use --projectname instead -n flag too.

This will create a basic project folder structure and required files in the specified directory.

<br>

### Create Scratch Org

There are number of ways to do this.

One of the files that was created for us in the previous step was `config/project-scratch-def.json`. The easiest method is to use this definition to create our scratch org.

```bash
$ sfdx force:org:create -f config/project-scratch-def.json -a myScratchOrg --durationdays 30 --setdefaultusername
```
- `--setdefaultusername` can be simplified to `-s` and will make the new scratch org our default working org.
- `-f` specified the org definition
- `--durationdays` indicates when the scratch org expires (in 1-30 days). The default duration is 7 days.
- `-a` creates an alias

Alternatively, we can omit the project-scratch-def.json and specify values in the command:

```bash
$ sfdx force:org:create edition=Developer -a myScratchOrg -s -v devHub
```

After running the command we should get a success message:

```bash
Successfully created scratch org: 00DN0000000VT0XMAW, username: test-zoxah4mpz0t7@example.com
```

and the org list command should show us the new scratch org in the available orgs list:

```bash
$ sfdx force:org:list
```
```bash
=== Orgs
     ALIAS       USERNAME                           ORG ID              CONNECTED STATUS
───  ──────────  ─────────────────────────────────  ──────────────────  ────────────────
(D)  DefaultHub  ***@***.com  00D5g000004FOzsEAG  Connected

     ALIAS         USERNAME                       ORG ID              EXPIRATION DATE
───  ────────────  ─────────────────────────────  ──────────────────  ───────────────
(U)  myScratchOrg  test-zoxah4mpz0t7
```

<br>

### Open the Scratch Org
You can now open the org

```bash
$ sfdx force:org:open -u <username/alias>
```
If we omit the -u <username/alias> the CLI will open the default scratch org.

<br>

### VS Code Extensions
VS Code has several extensions that can assist us using SFDX. In fact, everything we did so far or we'll be doing in later chapters can be done through these extensions.

Salesforce has combined most of these extensions under a single pack called **Salesforce Extension Pack**, including:

- Salesforce CLI Integration
- Apex: Provides code-editing features for the Apex
- Apex Interactive Debugger
- Apex Replay Debugger: presents the logged information
- Lightning Web Components
- SLDS Validator: simplifies working with Salesforce Lightning Design System
- ...

The new **Salesforce Extension Pack (Expanded)** extension features some other packages such as:

- XML: XML Language Support by Red Hat
- ESLint: Integrates ESLint JavaScript into VS Code.
- Prettier: Code formatter
- Apex PMD: PMD static analysis for Salesforce Apex
- Apex Log Analyzer

After the installation is completed, your VS Code will have some integrated features to work with SFDX.

- Create new project: F1 (ctrl+shift+p) -> `SFDX: Crate Project`
- Create new Scratch Org: F1 -> `SFDX: Crate a Default Scratch Org`
- Open scratch org: click on the rectangular icon beside your scratch org name on the VS Code status bar.
- ...


<br>

#### VS Code Extension

Salesforce recommends using VS Code along with **Salesforce Extension Pack**. Most of the commands presented at this tutorial are available via VS Code commands after installation of this package.

<br>

---
### References

- https://developer.salesforce.com/docs/atlas.en-us.packagingGuide.meta/packagingGuide/dev_hub_intro.htm