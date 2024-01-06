---
layout: post
title: "Map records without Id to their related records"
date: 2024-01-06
author: ehsan
categories: [Salesforce, Apex, Tips & Tricks]
excerpt_separator: <!--more-->
---

I recently encountered a task involving a multi-layered data structure. To outline the requirements concisely:
<br>

- **Trigger**: The process is initiated by an Insert trigger, which supplies a list of newly added Leads.
- **Account Lookup**: Each Lead within this list contains a reference to a corresponding Account.
- **Contact Mapping**: The objective is to retrieve the primary Contact associated with each Account and establish a direct link between that Contact and its respective Lead.

### Assumption

- Leads are new and therefore no *Id* exist.
- To simplify, we ignore every *null value* in lookups.
- Also we will ignore all the conditions.

### Solution

We know that we need to avoid Query in a loop, therefore, the crucial step involves constructing a Map that correlates Leads with their corresponding orders.

```java
Integer counter = 0;
Map<Integer, Lead> newLeadMap = new Map<Integer, Lead>();
for (Lead l : newLeads) {
     newLeadMap.put(counter++, l);
}
```

Secondly, we will get all the Account Ids associated with Leads.

```java
Set<Id> accountIdSet = new Set<Id>();
for (Integer key : leadMap.keySet()) {
     accountIdSet.add(leadMap.get(key).account__c);
}
```

And we query all Contacts that has a lookup to those Accounts.

```java
List<Contact> contacts = [SELECT Id, AccountId
     FROM Contact
     WHERE AccountId IN :AccountIds];
```

Next, let's get a map of Accounts with their corresponding Contacts.

```java
Map<Id, List<Contact>> accountIdToContactListMap = new Map<Id, List<Contact>>();
for (Contact con : contacts) {
     List<Contact> contactList = accountIdToContactListMap.get(con.AccountId);
     if (contactList != null) {
          contactList.add(con);
          accountIdToContactListMap.put(con.AccountId, contactList);
     } else {
          accountIdToContactListMap.put(con.AccountId, new List<Contact>{ con });
     }
}
```

Now it's easy to find the primary Contact.

```java
Map<Id, Contact> accountIdToContactMap = new Map<Id, Contact>();
for (Id acctId : accountIdToContactListMap.keySet()) {
     Boolean primaryContactFound = false;
     for (Contact con : accountIdToContactListMap.get(acctId)) {
          if (con.Is_Primary__c == true) {
               accountIdToContactMap.put(con.AccountId, con);
               break;
          }
     }
}
```

Cool! Last thing to do is to Map primary Contacts with the Leads.

```java
Map<Integer, Contact> result = new Map<Integer,Contact>();
for (Integer key : leadMap.keySet()) {
     Contact con = accountIdToContactMap.get(leadMap.get(key).account__c);

     result.put(key, con);
}
```

Remember that every **Key** value was a replacement for the Lead Id.
