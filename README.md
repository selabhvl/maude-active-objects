# Resource Contracts for Active Objects


This project contains ReAct, a Maude specification and executable semantics for a resource-aware active-object language.
It includes runnable examples and search/model-checking queries.

# Abstract
Workflows coordinate tasks across departments or organisations, 
where correct execution depends not only on control dependencies but also on the availability of shared resources. 
This paper presents ReAct, a resource-aware active object language for workflow modelling. 
In ReAct, method declarations serve as contracts: they specify alternative resource profiles in their signatures, 
giving methods multiple execution options when resources are limited. 
Methods can be invoked only once their dependency conditions are satisfied; at activation, 
a feasible resource profile is then selected and allocated. 
We encode the language in Maude and show how workflows can be executed, simulated, 
and verified against their declared dependencies and resource requirements.



# Maude Overview

Maude is a high-performance specification and programming language based on rewriting logic. 
Systems are modeled with equations and rewrite rules, organized into modules with sorts, operators, and axioms like ACU ([assoc comm id]) for multiset reasoning. 
Maude supports multiple execution modes—<kbd>reduce</kbd> (equational), <kbd>rew</kbd> (rule-based), 
and <kbd>search</kbd> (state-space exploration)—plus built-in LTL model checking via <kbd>modelCheck</kbd>, 
an object-based concurrency, user strategies, and meta-level reflection. 
This makes it well suited for executable specification, concurrency analysis, and proving properties in the same toolchain.


# Maude Installation
Grab the latest release for your platform (Linux x64, macOS Intel/ARM) from GitHub  [Releases](https://github.com/maude-lang/Maude/releases), then unzip it.
To run Maude on Windows, we recommend the [Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/install)

Official download & notes [Link](https://maude.cs.illinois.edu/wiki/Maude_download_and_installation).


### Quick verify (Linux / macOS / WSL)

1. Open your terminal (or **Ubuntu on WSL**), then start Maude:
```bash

maude
```


You should see a banner like this (version 3.5 or newer):


		     \||||||||||||||||||/
		   --- Welcome to Maude ---
		     /||||||||||||||||||\
	     Maude 3.5 built: Sep 25 2024 12:00:00
	     Copyright 1997-2024 SRI International
		   Tue Aug 19 13:49:45 2025

# Maude Implementation
The implementation of our ReAct in Maude (and its application to the exmple) is provided in two folders:

(1) task-dp/ the **Task Dependency** model and runnable examples (no resources).

(2) resource-aware/ **ReAct (Resource-Aware Active Objects)** Self-contained specification for the full resource-aware language (dependency and resource handling), along with the runnable example and the verification queries reported in the paper:


### Execution: Hospital workflow (rewrite)

 Run the executable semantics from the initial configuration:

```maude
rew in ACTIVE-OBJ-RESOURCE-TEST : init .
```
<details> <summary><strong>Click to expand the output:</strong></summary>

```
		     \||||||||||||||||||/
		   --- Welcome to Maude ---
		     /||||||||||||||||||\
	     Maude 3.5 built: Sep 25 2024 12:00:00
	     Copyright 1997-2024 SRI International
		   Tue Aug 19 13:49:45 2025

Maude> 
Maude> Maude> Maude> rewrite in ACTIVE-OBJ-RESOURCE-TEST : init .
rewrites: 484 in 0ms cpu (1ms real) (598269 rewrites/second)
result Configuration: < fregRecord : Future | value : someInt(1), state :
    resolved > < fcardioAssess : Future | value : someInt(111), state :
    resolved > < fimagingScan : Future | value : someInt(11), state : resolved
    > < finitiateTreatment : Future | value : someInt(1111), state : resolved >
    < fm : FUTMON | resolved : (fregRecord : fcardioAssess : fimagingScan :
    finitiateTreatment) > < resourcePool : RESOURCE-POOL | pool : (< r1 :
    RESOURCE | id : r1, type : "Intern", attrs : (years(2) ; shift("day")),
    state : available, ResCost : 10 > : < r2 : RESOURCE | id : r2, type :
    "Junior Nurse", attrs : (years(5) ; shift("day")), state : available,
    ResCost : 20 > : < r3 : RESOURCE | id : r3, type : "Junior Nurse", attrs :
    (years(5) ; shift("day")), state : available, ResCost : 20 > : < r4 :
    RESOURCE | id : r4, type : "Junior Resident", attrs : (years(5) ; shift(
    "day")), state : available, ResCost : 12 > : < r5 : RESOURCE | id : r5,
    type : "Senior Resident", attrs : (years(10) ; shift("day")), state :
    available, ResCost : 14 > : < r6 : RESOURCE | id : r6, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 > : < r7 : RESOURCE | id : r7, type : "Nurse", attrs : (years(
    5) ; shift("day")), state : available, ResCost : 30 > : < r8 : RESOURCE |
    id : r8, type : "Chief of Service", attrs : (years(15) ; shift("day")),
    state : available, ResCost : 16 > : < r9 : RESOURCE | id : r9, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 >), requests : nilReq > < counter : COUNTER | count : 5 > <
    Hospital : OBJECT | id : HOSPITAL, fields : 23, proc : idle, suspended : {
    fregRecord ; (fregRecord := fut(1)) ; (fcardioAssess := fut(2)) ; (
    fimagingScan := fut(3)) ; (finitiateTreatment := fut(4)) | eos} > <
    CardiologyUnit : OBJECT | id : CARDIOLOGYUNIT, fields : 102, proc : idle,
    suspended : emptyPool > < RadiologyUnit : OBJECT | id : RADIOLOGYUNIT,
    fields : 102, proc : idle, suspended : emptyPool > < fut(1) : Future |
    value : someInt(1), state : resolved > < fut(2) : Future | value : someInt(
    111), state : resolved > < fut(3) : Future | value : someInt(11), state :
    resolved > < fut(4) : Future | value : someInt(1111), state : resolved >

Maude> 
```
</details> 

### Reachability analysis: All futures resolved (search)
Run the search from the initial configuration to enumerate final states in which all four futures are resolved:


```maude
search in ACTIVE-OBJ-RESOURCE-TEST : init =>!
  < fregRecord         : Future | value : someInt(v1), state : resolved >
  < fcardioAssess      : Future | value : someInt(v2), state : resolved >
  < fimagingScan       : Future | value : someInt(v3), state : resolved >
  < finitiateTreatment : Future | value : someInt(v4), state : resolved >  C:Configuration .
```
<details> <summary><strong>Click to expand the output:</strong></summary>

```

Solution 1 (state 145)
states: 149  rewrites: 6579 in 7ms cpu (8ms real) (858877 rewrites/second)
C --> < fm : FUTMON | resolved : (fregRecord : fcardioAssess : fimagingScan :
    finitiateTreatment) > < resourcePool : RESOURCE-POOL | pool : (< r1 :
    RESOURCE | id : r1, type : "Intern", attrs : (years(2) ; shift("day")),
    state : available, ResCost : 10 > : < r2 : RESOURCE | id : r2, type :
    "Junior Nurse", attrs : (years(5) ; shift("day")), state : available,
    ResCost : 20 > : < r3 : RESOURCE | id : r3, type : "Junior Nurse", attrs :
    (years(5) ; shift("day")), state : available, ResCost : 20 > : < r4 :
    RESOURCE | id : r4, type : "Junior Resident", attrs : (years(5) ; shift(
    "day")), state : available, ResCost : 12 > : < r5 : RESOURCE | id : r5,
    type : "Senior Resident", attrs : (years(10) ; shift("day")), state :
    available, ResCost : 14 > : < r6 : RESOURCE | id : r6, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 > : < r7 : RESOURCE | id : r7, type : "Nurse", attrs : (years(
    5) ; shift("day")), state : available, ResCost : 30 > : < r8 : RESOURCE |
    id : r8, type : "Chief of Service", attrs : (years(15) ; shift("day")),
    state : available, ResCost : 16 > : < r9 : RESOURCE | id : r9, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 >), requests : nilReq > < counter : COUNTER | count : 5 > <
    Hospital : OBJECT | id : HOSPITAL, fields : 23, proc : idle, suspended : {
    fregRecord ; (fregRecord := fut(1)) ; (fcardioAssess := fut(2)) ; (
    fimagingScan := fut(3)) ; (finitiateTreatment := fut(4)) | eos} > <
    CardiologyUnit : OBJECT | id : CARDIOLOGYUNIT, fields : 102, proc : idle,
    suspended : emptyPool > < RadiologyUnit : OBJECT | id : RADIOLOGYUNIT,
    fields : 102, proc : idle, suspended : emptyPool > < fut(1) : Future |
    value : someInt(1), state : resolved > < fut(2) : Future | value : someInt(
    11), state : resolved > < fut(3) : Future | value : someInt(111), state :
    resolved > < fut(4) : Future | value : someInt(1111), state : resolved >
v1 --> 1
v2 --> 11
v3 --> 111
v4 --> 1111

Solution 2 (state 151)
states: 153  rewrites: 6705 in 8ms cpu (8ms real) (811056 rewrites/second)
C --> < fm : FUTMON | resolved : (fregRecord : fcardioAssess : fimagingScan :
    finitiateTreatment) > < resourcePool : RESOURCE-POOL | pool : (< r1 :
    RESOURCE | id : r1, type : "Intern", attrs : (years(2) ; shift("day")),
    state : available, ResCost : 10 > : < r2 : RESOURCE | id : r2, type :
    "Junior Nurse", attrs : (years(5) ; shift("day")), state : available,
    ResCost : 20 > : < r3 : RESOURCE | id : r3, type : "Junior Nurse", attrs :
    (years(5) ; shift("day")), state : available, ResCost : 20 > : < r4 :
    RESOURCE | id : r4, type : "Junior Resident", attrs : (years(5) ; shift(
    "day")), state : available, ResCost : 12 > : < r5 : RESOURCE | id : r5,
    type : "Senior Resident", attrs : (years(10) ; shift("day")), state :
    available, ResCost : 14 > : < r6 : RESOURCE | id : r6, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 > : < r7 : RESOURCE | id : r7, type : "Nurse", attrs : (years(
    5) ; shift("day")), state : available, ResCost : 30 > : < r8 : RESOURCE |
    id : r8, type : "Chief of Service", attrs : (years(15) ; shift("day")),
    state : available, ResCost : 16 > : < r9 : RESOURCE | id : r9, type :
    "Senior Nurses", attrs : (years(10) ; shift("day")), state : available,
    ResCost : 24 >), requests : nilReq > < counter : COUNTER | count : 5 > <
    Hospital : OBJECT | id : HOSPITAL, fields : 25, proc : idle, suspended : {
    fregRecord ; (fregRecord := fut(1)) ; (fcardioAssess := fut(2)) ; (
    fimagingScan := fut(3)) ; (finitiateTreatment := fut(4)) | eos} > <
    CardiologyUnit : OBJECT | id : CARDIOLOGYUNIT, fields : 102, proc : idle,
    suspended : emptyPool > < RadiologyUnit : OBJECT | id : RADIOLOGYUNIT,
    fields : 102, proc : idle, suspended : emptyPool > < fut(1) : Future |
    value : someInt(1), state : resolved > < fut(2) : Future | value : someInt(
    11), state : resolved > < fut(3) : Future | value : someInt(111), state :
    resolved > < fut(4) : Future | value : someInt(1111), state : resolved >
v1 --> 1
v2 --> 11
v3 --> 111
v4 --> 1111


...

```
</details> 

### Reachability analysis: Resource release (search)
Check resource release: verify that the Junior Resident (r4) is available in every final state.


```maude
search in ACTIVE-OBJ-RESOURCE-TEST : init =>!
  < resourcePool : RESOURCE-POOL
    | pool : ( < r4 : RESOURCE | id : r4, type  : "Junior Resident", attrs : A , state : available, ResCost : 12 > : _ ) >
  C:Configuration .```
<details> <summary><strong>Click to expand the output:</strong></summary>

```
...

Solution 9 (state 154)
states: 155  rewrites: 6711 in 8ms cpu (9ms real) (745832 rewrites/second)
C --> < fregRecord : Future | value : someInt(1), state : resolved > <
    fcardioAssess : Future | value : someInt(11), state : resolved > <
    fimagingScan : Future | value : someInt(111), state : resolved > <
    finitiateTreatment : Future | value : someInt(1111), state : resolved > <
    fm : FUTMON | resolved : (fregRecord : fcardioAssess : fimagingScan :
    finitiateTreatment) > < counter : COUNTER | count : 5 > < Hospital : OBJECT
    | id : HOSPITAL, fields : 27, proc : idle, suspended : {fregRecord ; (
    fregRecord := fut(1)) ; (fcardioAssess := fut(2)) ; (fimagingScan := fut(
    3)) ; (finitiateTreatment := fut(4)) | eos} > < CardiologyUnit : OBJECT |
    id : CARDIOLOGYUNIT, fields : 102, proc : idle, suspended : emptyPool > <
    RadiologyUnit : OBJECT | id : RADIOLOGYUNIT, fields : 102, proc : idle,
    suspended : emptyPool > < fut(1) : Future | value : someInt(1), state :
    resolved > < fut(2) : Future | value : someInt(11), state : resolved > <
    fut(3) : Future | value : someInt(111), state : resolved > < fut(4) :
    Future | value : someInt(1111), state : resolved >
P --> < r1 : RESOURCE | id : r1, type : "Intern", attrs : (years(2) ; shift(
    "day")), state : available, ResCost : 10 > : < r2 : RESOURCE | id : r2,
    type : "Junior Nurse", attrs : (years(5) ; shift("day")), state :
    available, ResCost : 20 > : < r3 : RESOURCE | id : r3, type :
    "Junior Nurse", attrs : (years(5) ; shift("day")), state : available,
    ResCost : 20 > : < r5 : RESOURCE | id : r5, type : "Senior Resident", attrs
    : (years(10) ; shift("day")), state : available, ResCost : 14 > : < r6 :
    RESOURCE | id : r6, type : "Senior Nurses", attrs : (years(10) ; shift(
    "day")), state : available, ResCost : 24 > : < r7 : RESOURCE | id : r7,
    type : "Nurse", attrs : (years(5) ; shift("day")), state : available,
    ResCost : 30 > : < r8 : RESOURCE | id : r8, type : "Chief of Service",
    attrs : (years(15) ; shift("day")), state : available, ResCost : 16 > : <
    r9 : RESOURCE | id : r9, type : "Senior Nurses", attrs : (years(10) ;
    shift("day")), state : available, ResCost : 24 >

No more solutions.
states: 155  rewrites: 6711 in 9ms cpu (9ms real) (733522 rewrites/second)

```
</details> 

