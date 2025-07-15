## Const well known value Constraint
> The data about the transmitted should follow a rule contract before sending. This contract should be centralized in a particular
provider, applications may store this locally but it must be provided externally.

```lua
/* based upon */ :int32 / /*C#*/<int> / /*JS*/:Number*

-- TG        Name          length or id u16     group flags         meaning
/* #N~ */ NonNegative  = | ******** ******** ******** *******1 | --  Number not negative
/* #N+ */ NonZeroed    = | ******** ******** ******** ******1* | --  Value not zero or default
/* #AA */ NotEmpty     = | ******** ******** ******** *****1** | --  trimmed text not empty or values not default
-- --------------------------------spaces management------------------------------------------------
/* #NN */ PoliName     = | ******** ******** *******1 ****1*** | --  Requires multi part name
/* #NS */ NoSpaces     = | ******** ******** ******** ****1*** | --  Continuous text without spaces
-- ------------------------------characters management----------------------------------------------
/* #AB */ LettersOnly  = | ******** ******** ****111* ***1**** | --  Text only letters
/* #Nº */ NumberOnly   = | ******** ******** *****11* ***1**** | --  Text only ordinal numbers
/* #AP */ Alphanumeric = | ******** ******** ******1* ***1**** | --  Alphanumeric text
-- ----------------------------------time control---------------------------------------------------
/* #T- */ FutureTime   = | ******** ******** ***1**** **1***** | --  Date/Time of the future
/* #T+ */ PastTime     = | ******** ******** ******** **1***** | --  Date/Time from past
-- -----------------------------------relational----------------------------------------------------
/* #!= */ Unique       = | ******** ******** 111***** *1****** | --  Values cannot repeat within collection
/* #PK */ PrimaryKey   = | ******** ******** *11***** *1****** | --  Values cannot repeat within repository
/* #FK */ ForeignKey   = | ******** ******** **1***** *1****** | --  External key inforced

-- ----------------------------------value ranges---------------------------------------------------
/* #>> */ MaxValue     = | XXXXXXXX XXXXXXXX *******1 1******* | --  Max Value indicator
/* #<< */ MinValue     = | XXXXXXXX XXXXXXXX ******** 1******* | --  Min Value indicator
/* #<> */ LineLength   = | XXXXXXXX XXXXXXXX ******** ******** | --  Max length indicator (x)
/* #?? */ Customized   = | XXXXXXXX XXXXXXXX 00000000 00000000 | --  CUSTOM Constraint    (y)
```

---

### The first region
1. Upon Text 
> _NotEmpty_ requires a text to be null when there are no characters. If "NonZeroed" is also specified than 
the text cannot be null at all.
2. Upon Numbers
> They do as said, but _NotEmpty_ acts as a **not null** specifier
3. Upon Dates
> _NonNegative_ indicates that its actually a duration type (subtract default and you'll have a period) _NotEmpty_ becomes a **not null** 
specifier, _NonZeroed_ informs that it cannot be the **default** value

---

### Spaces management
1. Upon Text
> Expected behavior, they conflict between each other and the greater one should be check first
2. Upon Numbersflo
> _PoliName_ demonstrates a decimal-part containing number (non-integer) and _NoSpaces_ is the opposite. (none remains decimal number)
3. Upon Dates
> _PoliName_ demonstrates a DateTime value and _NoSpaces_ a DateOnly kind of value. (none remains DateTime)

---

### Characters management
1. Upon Text
> This is the only section where this region is relevant. They are not combinable. They do allow spaces _NoSpaces_ is 
not specified. Alphanumeric allows ONLY letters and numbers. None of em allow the whole ansi character set

---

### Time control
1. Upon Dates
> This is the only section where this region is relevant. They are not combinable. _FutureTime_ allows only value after the 
current time of the server and _PastTime_ allows only values before it 

---

### Relational
1. \* (Any)
> These are special cases for collections, which are either duplicates within the repository or within a collection itself

---

### Value Ranges
1. \* (Any)
> _MaxValue_, _MinValue_ uses the left side of the **i32** to declare a boundary values for an number if applied on 
text will be interpreted as fixed term required length (as in char(n) from sql). _LineLength_ serves the same purpose but 
works for both text numbers as dynamic size (as varchar), in case of text represents string length and other types 
is instance count. **Customized** carries a part of a on the left side of the **i32** that identifies a custom rule-set

int32 value Constraint bit break down flag indicators:
```
byte 1
    b00       NonNegative 
    b01       NonZeroed   
    b02       NotEmpty
    b03       group S present indicator
    -
    b04       group T present indicator
    b05       group U present indicator
    b06       group V present indicator
    b07       group W/L switcher indicator (ON/ValueRange OFF/MaxLength)

byte 2
    b08       group S and W switcher (ON/PoliName OFF/NoSpaces) (ON/MaxValue OFF/MinValue)
    b09       group T discriminator T'
    b10       group T discriminator T''
    b11       group T discriminator T''' (sum T(s) 1-Alphanumeric 2-NumerOnly 3-LettersOnly)
    -
    b12       group U switcher (ON/FutureTime OFF/PastTime)
    b13       group V indicator V'   Unique
    b14       group V indicator V''  PrimaryKey
    b15       group V indicator V''' ForeignKey
    
[   bRR 16 more bits ] used to indicate a numeric value to indicate either 

- Max/Min Value (when w group is ON)
- Maximum array Length (when w group is OFF)
- Custom rule-set Id when all other groups, switches, indicadators and discriminators are set to 0

```
