## [Proposal] The xRotating List

### Introduction

Goal: **helping small pools to mint their first blocks and attract enough natural delegators by temporarily inflating their stake**

Volunteers from the xSPO Alliance could be delegators for their own pools by concentrating their respective delegating power into a "rotating stake". 

The main idea would be to massively delegate staked wallets to one of us during a predefined number of epochs and then do it again to the benefit of another one of and again and again until each one of us would have benefited temporarily from this opportunity: more stake means a higher probability of minting block and of bootstraping a natural delegation momentum. The core of this never-ending process would be an ordererd list ("the xRotating List") and a set of rules detailing how to enter the List and how to be excluded from the List.

#### Basic representation of the xRotating List:

|     |             |               |
|---  |---          |---            |
| N   | Slot #1     |  ] "Hot seat" |
|     | Slot #2     |  ] "Hot seat" |
|     | ...         |               |
|     | Slot #P-1   |               |
|     | Slot #P     |               |

With P participants, the List is of size P. N is the number of Ouroboros epochs the pool in Slot #1 will benefit from the Common Stake (CS).


### Rules

#### Basics

- To enter the List, each wannabe participant declare the address of a wallet of his own with X₳ in it as a request. And this request will have to be validated. 

- The initial Delegation Commitment (initial DC) corresponds to the declaration of the participant in this first request.

- A request to enter the list is valida if the following conditions are met:
  - the wallet at the provided address ("Delegated Wallet") contains at least 500₳
  - the "Delegated Wallet" contains at least the initial DC
  - the requesting stake pool has less than 3% in margin
  - the requesting stake pool has less than 500₳ in fees
  - the requesting stake pool has minted less than 50 lifetime blocks

- These "Conditions to be in the xRotating List" will be checked at least one time per Ouroboros epoch ("Verifications")

- Each time a new stake pool join the List, a new slot is added to the end of the List and the position of the joining pool is set to Slot #P. 

- A Phase is a sequence of Ouroboros epochs during which N > 0.

- At the beginning of a Phase, all xDelegators' wallets ("Delegated Wallets") are delegated to the pool in Slot #1 and a new value of N is computed with respected to the Parameters.

- When a pool reaches Slot #1, it can stay there for N Ouroboros epochs. This N is computed according to the "Table of Phase stages" which relies on how many ₳ are in the DC.

- Notifications are sent by the xSupervisors of the List to indicate to each xDelegators that they need to change their delegation for the Delegated Wallet during the current Ouroboros epoch.

- At the end of a Phase, the List is shifted, N is reset and a new Phase begins.

- After being in Slot #1 for N epochs, a pool has to stay in the List for some epochs in order to let the other pools benefit from what it has benefited. The "Table of Epochs of Commitment" shows when a former Slot #1 pool wishing to leave the group can be leaving the List and remove its stake from the Common Stake without being considered in fault.

- The initial DC can be increased and then be called "updated DC" but the next time the pool will be in Slot #1, N can only increment by 1 (if a treshold from the "Table of Phases stages" is crossed by the new amount in DC). The correct value for N according to the "Table of Phases stages" will be accessible the next time the pool is in Slot #1 after this one.

- A pool which has been excluded from the list can re-enter after its exclusion time if it meets the "Conditions to be in the xRotating List". A pool which has been permanently excluded cannot enter the List again.

#### Details of Phase mecanisms

```
Ouroboros   [   Epoch e   ][  Epoch e+1  ][  Epoch e+2  ][  Epoch e+3  ][  Epoch e+5  ][  Epoch e+6  ]
Verif.       X              X              X              X              X              X      
The List    [             ][      N      ][      N-1    ][      N-2    ][             ][             ]
N           [      0      ][      2      ][      1      ][      0      ][      1      ][      0      ]
Phase       [      1      ][                     2                     ][              3             ]
Notif.                                         *X X*                         *X X*
```

At the beginning of a new Ouroboros epoch e:
- if N = 0, each pool goes up by one Slot: a pool previously in Slot #S now takes place in Slot #S-1. The pool in Slot #1 is moved to Slot #P. And a new Phase begins that will set a new value for N.
- if N > 0, N is decremented, i.e. N(e) = N(e-1) - 1
  - and if N = 1, xDelegators receive notifications from the xSupervisors that invite them to delegate to the pool that will be in Slot #1 in the next Phase.

#### Rules breaking:

If a pool breaks a rule it receives penalties which depend on the severity of the fault. 

Penalties take effect immediately and are described in the "Table of Penalties".

N is set to 1 and all xDelegators are immediately notified to modify the pool they are delegated their Delegated Wallet:
- if a pool currently in Slot #1 or #2 breaks some of the rules can be excluded from the List
- if a pool in Slot #1 mints mB blocks while its associated N > 0


### Parameters

**Conditions to be in the xRotating List** :

- the wallet at the provided address ("Delegated Wallet") contains at least **500**₳
- the "Delegated Wallet" contains at least the initial DC
- the requesting stake pool has less than **3**% in margin
- the requesting stake pool has less than **500**₳ in fees
- the requesting stake pool has minted less than **50** lifetime blocks


**Table of Phases stages (N)**

|₳ in initial DC   |  N  |  #epochs|
|---|---|---|
|0.5k   to 1.0k    |  1  |  2|
|1.0k   to 2.5k    |  2  |  3|
|2.5k   to 10.0k   |  3  |  4 |
|10.0k  to 50.0k   |  4  |  5 |
|50.0k  and more   |  5  | 6 |


**Table of maximum blocks per Phase (mB)**

| ₳ in initial DC   |  mB|
|---|---|
|0.5k   to 1.0k    |  infinity|
|1.0k   to 2.5k    |  infinity|
|2.5k   to 10.0k   |  1|
|10.0k  to 25.0k   |  2|
|10.0k  to 50.0k   |  3|
|50.0k  to 100.0k  |  4|
|100.0k and more   |  5|


**Table of Epochs of Commitment**

|₳ in updated DC    |  #epochs before leaving the List|
|---|---|
|0.5k   to 1.0k    |  if P < 5 then P else 5|
|1.0k   to 2.5k    |  if P < 5 then P else 5|
|2.5k   to 10.0k   |  if P < 4 then P else 4|
|10.0k  to 25.0k   |  if P < 4 then P else 4|
|10.0k  to 50.0k   |  if P < 3 then P else 3|
|50.0k  to 100.0k  |  if P < 3 then P else 3|
|100.0k and more   |  if P < 2 then P else 2|


**Table of Penalties**
|Code|Description|Penalty|
|---|---|---|
|F1| delegating to the wrong pool in the List (exception : the pool in Slot #1 can delegate to any other pool in the xRotating List during its Phase)|1 Slot down|
|F2| delegating to a pool that is not in the List|2 Slots down|
|F3| decreasing the amount of ₳ in DC|3 Slots down|
|F4| not respecting the "Conditions to be in the xRotating List"| 4 Slots down|
|F5| not respecting the "Table of Epochs of Commitment"| permanent exclusion| 
|R1| 3 successive epochs with F1        | exclusion for 5 epochs |
|R2| 2 non-sequential epochs with F2                            | exclusion for 10 epochs |
|R3| 2 non-sequential epochs with F3                  | exclusion for 10 epochs |
|R3| 2 non-sequential epochs with F4                           |exclusion for 10 epochs|
|X1| F2/F3/F4 while in Slot #1                                 | exclusion for 30 epochs |
|X2| F1/F2/F3/F4 while in Slot #2                               |exclusion for 30 epochs |
|X3| F1/F2/F3/F4/F5 while in Slot #P after being in Slot #1                               | permanent exclusion |

**Hyper-parameters**
- max participants : 50
