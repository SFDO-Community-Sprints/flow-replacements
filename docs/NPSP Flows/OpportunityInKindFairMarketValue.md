---
layout: default
title: Opportunity In-Kind Fair Market Value
nav_order: 4
parent: NPSP Flows
has_children: false
---

# Opportunity In-Kind Fair Market Value

### How to create the Fair Market Value Flow with Step by Step Instructions if you don’t want to install a package

## Configure Start Settings
- Record Triggered Flow
- Opportunity
- Created or Updated
- Formula Evaluates to True
- Every time
- Fast Field Updates

```
({!$Record.Amount} = 0 || ISBLANK({!$Record.Amount})) &&
{!$Record.RecordType.DeveloperName} = 'InKindGift' &&
NOT(ISBLANK({!$Record.npsp__Fair_Market_Value__c}))
```

<hr>


## Add Update Records Element
- Name = Opportunity Copy FMV to Amount
- Description = Copy the Opportunities Fair Market Value field to the Amount field
- Use the oppt record that triggered the flow
- Conditions - None
- Set Field Values
```
Amount <--- Triggering Opportunity > Fair Market Value

#Test and Activate
