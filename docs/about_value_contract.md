# The value contract system

Each contract represents an entity instances clas and should be transmitted as

```lua
-- the contract doens't have a name or description by itself, it might be provided by the context
-- b-len type       meaning 
    1    :u8      -- The Type Signal
    4    :i32     -- The rule-set
    16?  :u8.guid -- Maybe the type id or ascii characters
```

### (0-1) The Type Signal
Either 0, 1 or anything else. 
- Zero means that the contract ended and more irrelevant trimming data might be added
- One means that the type is composed/custom and the type GUID will be added
- Else cast to ***Integration Types***

### (2-4) The ConstraintSet
Addresses one or more set of Constraint associated with the given value
* seealso ***Const well known value Constraint***

### (5-21) The TypeId?
May exists and may not based on the signal's value. It might be either random bytes for a unique identifier
or ascii characters. Which one makes sense first

## Contract based value transmission

There are two possibilities, either the listener knows what type should it receive or its a generic one. For the 
known one, a instance sequence is provided abstract bytes in sequence, the actual meaning of those will be interpreted by 
the listener with the entity contract in hands. If its a generic one than the contract ID should be the 
first 16 bytes of the message's content