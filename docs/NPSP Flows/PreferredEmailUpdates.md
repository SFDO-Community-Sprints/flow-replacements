---
layout: default
title: Preferred Email Updates
nav_order: 2
parent: NPSP Flows
has_children: false
---

# Preferred Email NPSP Updates


### How to create the Email Flow with Step by Step Instructions if you don’t want to install a package

## Configure Start Settings
- Record Triggered Flow
- Contact
- Created or Updated
- Formula Evaluates to True
- Every time
- Fast Field Updates

```
OR(
ISCHANGED({!$Record.Email}),
ISBLANK({!$Record.Email}),
ISCHANGED({!$Record.npe01__Preferred_Email__c}),
ISCHANGED({!$Record.npe01__AlternateEmail__c}),
ISCHANGED({!$Record.npe01__HomeEmail__c}),
ISCHANGED({!$Record.npe01__WorkEmail__c}),
AND(ISNULL({!$Record__Prior.Email}), NOT(ISNULL({!$Record.Email})))
)
```

<hr>


## Create Your Resources
- Formula 
- Name = formulaPreferredEmailAddress
- Description = Returns the value of whichever email address is set as Preferred. If no NPSP email addresses are populated returns the value of the standard Email field.
- Data Type = Text

```
IF({!formulaNPSPEmailPresent}, (CASE({!$Record.npe01__Preferred_Email__c}, 
    'Alternate', {!AlternateEmail}, 
    'Personal', {!PersonalEmail},
    'Work', {!WorkEmail},
    {!PersonalEmail})), 
   {!$Record.Email}
)
```

- Formula 
- Name = formulaNPSPEmailPresent
- Description = Returns true if at least one NPSP email field is populated.
- Data Type = Boolean

```
OR(
NOT(ISBLANK({!$Record.npe01__AlternateEmail__c})),
NOT(ISBLANK({!$Record.npe01__HomeEmail__c})),
NOT(ISBLANK({!$Record.npe01__WorkEmail__c}))
)
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
    - End
```
## Set Assignment under Personal
**Name = Set Personal Email**
```
    - Triggering Contact > Personal Email = Triggering Contact > Email
    - End
```
## Set Assignment under Work
**Name = Set Work Email** 
```
    - Triggering Contact > Work Email = Triggering Contact > Email
    - End
```
## Set Assignment under NPSP Email fields are changed
**Name = Assign new Email value**
```
    - Triggering Contact > Email = formulaPreferredEmailAddress 
    - End
```
## Set Assignment under No Preferred Email set
**Name = Set Preferred Email to Work**
```
    - Triggering Contact > Preferred Email = Work
    - Triggering Contact > Work Email = Triggering Contact > Email
    - End
```

# Test, and Activate
