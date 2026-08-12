---
layout: default
title: Preferred Email Updates
nav_order: 2
parent: NPSP Flows
has_children: false
---

# Preferred Email NPSP Updates


###How to create the Email Flow with Step by Step Instructions if you don’t want to install a package



##Configure Start
- Record Triggered Flow
- Contact
- Created or Updated
- Formula Evaluates to True

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



Every time
Fast Field Updates
Create Resources
Formula 
Name = formulaPreferredEmailAddress
Description = Returns the value of whichever email address is set as Preferred. If no NPSP email addresses are populated returns the value of the standard Email field.
Data Type = Text

```

IF({!formulaNPSPEmailPresent}, (CASE({!$Record.npe01__Preferred_Email__c}, 
    'Alternate', {!AlternateEmail}, 
    'Personal', {!PersonalEmail},
    'Work', {!WorkEmail},
    {!PersonalEmail})), 
   {!$Record.Email}
)
```


Formula 
Name = formulaNPSPEmailPresent
Description = Returns true if at least one NPSP email field is populated.
Data Type = Boolean

```
OR(
NOT(ISBLANK({!$Record.npe01__AlternateEmail__c})),
NOT(ISBLANK({!$Record.npe01__HomeEmail__c})),
NOT(ISBLANK({!$Record.npe01__WorkEmail__c}))
)
```







Add Decision
Name = Email Update Needed
Standard Email is New or Changed
Custom Logic = (1 or 3) and 2 and 4
Record Prior Values Triggering Contact > Email is Blank = TRUE
Triggering Contact > Email Is Blank = TRUE
Triggering Contact > Email Is Changed = TRUE
Triggering Contact > Preferred Email is Blank = FALSE
NPSP Email fields are changed
Any Conditions (OR)
Triggering Contact > Alternate Email Is Changed is TRUE
Triggering Contact > Work Email Is Changed is TRUE
Triggering Contact > Personal Email Is Changed is TRUE
Triggering Contact > Email Is Blank is TRUE
Triggering Contact > Preferred Email Is Changed is TRUE
No Preferred Email Set
All Conditions (AND)
Triggering Contact > Alternate Email Is Blank is TRUE
Triggering Contact > Work Email Is Blank is TRUE
Triggering Contact > Personal Email Is Blank is TRUE
Triggering Contact > Email Is Blank is TRUE
Triggering Contact > Preferred Email Is Blank is TRUE
Default
Add Decision under Standard Email is New or Changed
Name = Preferred Email
Alternate
Triggering Contact > Preferred Email = Alternate
Personal
Triggering Contact > Preferred Email = Personal
Work
Triggering Contact > Preferred Email = Work
Default	
Set Assignment under Alternate
Name = Set Alternate Email 
Triggering Contact > Alternate Email = Triggering Contact > Email
End
Set Assignment under Personal
Name = Set Personal Email 
Triggering Contact > Personal Email = Triggering Contact > Email
End
Set Assignment under Work
Name = SetWork Email 
Triggering Contact > Work Email = Triggering Contact > Email
End
Set Assignment under NPSP Email fields are changed
Name = Assign new Email value
Triggering Contact > Email = formulaPreferredEmailAddress 
