---
layout: default
title: Preferred Phone Updates
nav_order: 2
parent: NPSP Flows
has_children: false
---

# Preferred Phone Updates


### How to create the Phone Flow with Step by Step Instructions if you don’t want to install a package

## Configure Start Settings
- Record Triggered Flow
- Contact
- Created or Updated
- Formula Evaluates to True
- Every time
- Fast Field Updates

```
OR(
       ISCHANGED({!$Record.npe01__PreferredPhone__c}),
       ISCHANGED({!$Record.HomePhone}),
       ISCHANGED({!$Record.MobilePhone}),
       ISCHANGED({!$Record.npe01__WorkPhone__c}),
       ISCHANGED({!$Record.OtherPhone}),
       ISNEW(),
       ISCHANGED({!$Record.Phone})
)
```

<hr>


## Create Your Resources
- Formula 
- Name = formulaHomePhoneNumber
- Description = Returns the Home Phone number if it's populated. If not, checks and returns Mobile, Work, and Other if populated, in that order. If all are blank, defaults to the value of the standard Phone field.
- Data Type = Text

```
IF(NOT(ISBLANK({!$Record.HomePhone})), {!$Record.HomePhone},
IF(NOT(ISBLANK({!$Record.MobilePhone})), {!$Record.MobilePhone},
IF(NOT(ISBLANK({!$Record.npe01__WorkPhone__c})), {!$Record.npe01__WorkPhone__c},
IF(NOT(ISBLANK({!$Record.OtherPhone})), {!$Record.OtherPhone},
{!$Record.Phone}))))
```

- Formula 
- Name = formulaMobilePhoneNumber
- Description = Returns the Mobile Phone number if it's populated. If not, checks and returns Home, Work, and Other if populated, in that order. If all are blank, defaults to the value of the standard Phone field.
- Data Type = Text

```
IF(NOT(ISBLANK({!$Record.MobilePhone})), {!$Record.MobilePhone},
IF(NOT(ISBLANK({!$Record.HomePhone})), {!$Record.HomePhone},
IF(NOT(ISBLANK({!$Record.npe01__WorkPhone__c})), {!$Record.npe01__WorkPhone__c},
IF(NOT(ISBLANK({!$Record.OtherPhone})), {!$Record.OtherPhone},
{!$Record.Phone}))))
```
- Formula 
- Name = formulaOtherPhoneNumber
- Description = Returns the Other Phone number if it's populated. If not, checks and returns Mobile, Work, and Home if populated, in that order. If all are blank, defaults to the value of the standard Phone field.
- Data Type = Text

```
IF(NOT(ISBLANK({!$Record.OtherPhone})), {!$Record.OtherPhone},
IF(NOT(ISBLANK({!$Record.MobilePhone})), {!$Record.MobilePhone},
IF(NOT(ISBLANK({!$Record.npe01__WorkPhone__c})), {!$Record.npe01__WorkPhone__c},
IF(NOT(ISBLANK({!$Record.HomePhone})), {!$Record.HomePhone},
{!$Record.Phone}))))
```
- Formula 
- Name = formulaPreferredPhoneNumber
- Description = Returns the value of the Contact's preferred phone number. Defaults to Work Phone if no preferred number selected.
- Data Type = Text

```
CASE({!$Record.npe01__PreferredPhone__c},
'Home', {!formulaHomePhoneNumber},
'Mobile', {!formulaMobilePhoneNumber},
'Work', {!formulaWorkPhoneNumber},
'Other', {!formulaOtherPhoneNumber},
{!formulaWorkPhoneNumber}
)
```
- Formula 
- Name = formulaWorkPhoneNumber
- Description = Returns the Work Phone number if it's populated. If not, checks and returns Mobile, Home, and Other if populated, in that order. If all are blank, defaults to the value of the standard Phone field.
- Data Type = Text

```
IF(NOT(ISBLANK({!$Record.npe01__WorkPhone__c})), {!$Record.npe01__WorkPhone__c},
IF(NOT(ISBLANK({!$Record.MobilePhone})), {!$Record.MobilePhone},
IF(NOT(ISBLANK({!$Record.HomePhone})), {!$Record.HomePhone},
IF(NOT(ISBLANK({!$Record.OtherPhone})), {!$Record.OtherPhone},
{!$Record.Phone}))))
```
<hr>

## Add Decision
**Name = Email Update Needed**
Outcome 1 Standard Email is New or Changed
Custom Logic = (1 or 3) and 2 and 4
```
1. Record Prior Values Triggering Contact > Email is Blank = TRUE
2. Triggering Contact > Email Is Blank = TRUE
3. Triggering Contact > Email Is Changed = TRUE
4. Triggering Contact > Preferred Email is Blank = FALSE
```
Outcome 2 NPSP Email fields are changed
Any Conditions (OR)
```
- Triggering Contact > Alternate Email Is Changed is TRUE
- Triggering Contact > Work Email Is Changed is TRUE
- Triggering Contact > Personal Email Is Changed is TRUE
- Triggering Contact > Email Is Blank is TRUE
- Triggering Contact > Preferred Email Is Changed is TRUE
```
Outcome 3 No Preferred Email Set
All Conditions (AND)
```
- Triggering Contact > Alternate Email Is Blank is TRUE
- Triggering Contact > Work Email Is Blank is TRUE
- Triggering Contact > Personal Email Is Blank is TRUE
- Triggering Contact > Email Is Blank is TRUE
- Triggering Contact > Preferred Email Is Blank is TRUE
```
Default Outcome
<hr>

## Add Decision under Standard Email is New or Changed
**Name = Preferred Email**

Outcome 1 Alternate
```
Triggering Contact > Preferred Email = Alternate
```
Outcome 2 Personal
```
Triggering Contact > Preferred Email = Personal
```
Outcome 3 Work
```
Triggering Contact > Preferred Email = Work
```
Default Outcome	

## Set Assignment under Alternate
**Name = Set Alternate Email**
```
    - Triggering Contact > Alternate Email = Triggering Contact > Email
```
## Set Assignment under Personal
**Name = Set Personal Email**
```
    - Triggering Contact > Personal Email = Triggering Contact > Email
```
## Set Assignment under Work
**Name = Set Work Email** 
```
    - Triggering Contact > Work Email = Triggering Contact > Email
```
## Set Assignment under NPSP Email fields are changed
**Name = Assign new Email value**
```
    - Triggering Contact > Email = formulaPreferredEmailAddress 
```
## Set Assignment under No Preferred Email set
**Name = Set Preferred Email to Work**
```
    - Triggering Contact > Preferred Email = Work
    - Triggering Contact > Work Email = Triggering Contact > Email
```

# Test and Activate
