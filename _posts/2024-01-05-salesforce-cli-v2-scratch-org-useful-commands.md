---
layout: post
title: "Salesforce CLI sf (v2) handy commands to work with Scratch orgs"
date: 2024-01-05
author: ehsan
categories: [Salesforce, CLI, Scratch Org]
excerpt_separator: <!--more-->
---

Here are some handy **Salesforce CLI sf (v2)** commands for working with scratch orgs:

<br>

Remember to Uninstall the previous `sfdx` CLI before installing `sf`.

```bash
# installation
$ npm install sf

# create a DX project
$ sf project generate -n my-project

# authorize a default DevHub
$ sf org login web -d -a default-hub

# create a default scratch org
$ sf org create scratch -f config/project-scratch-def.json -a my-scratch-org -y 30 -d

# View the list of our orgs
$ sf org list

# generate a password for the default scratch org
$ sf org generate password

# push all local metadata changes to the scratch org
$ sf project deploy start

# assign permission set to the current user
$ sf org assign permset -n My_Permission_Set_Name

# run a script (for example to create sample data)
$ sf apex run -f ./scripts/setupMyObj.apex

# open the newly created default scratch org
$ sf org open

# org details including access token, username, password, alias, etc.
$ sf org display

# deploy or retrieve specific metadata
$ sf project deploy start -d force-app/main/default/lwc/myComponent
$ sf project retrieve start -m ApexClass:MyApexClass

# set or change org alias
$ sf alias set my-scratch-org=test-dev@example.com

# change default DevHub
$ sf config set target-org=me@myhub.org

# delete a scratch org
$ sf org delete scratch -o my-scratch-org
```

<!--more-->

---
### References

- [Uninstall Salesforce CLI or Plugins](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_uninstall.htm)