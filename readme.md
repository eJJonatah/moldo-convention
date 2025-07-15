# Moldo (Object Molds)
###### \[Mold\]O\[bjects\]
```
 _____     _   _        | Data schema serialization/transmission contract
|     |___| |_| |___    
| | | | . | | . | . |   | Goiania - 2026
|_|_|_|___|_|___|___|   | @ejjonatah
                     
```

This is a convention file to deal with objects model design through a contract knowledge system. The objective of this 
system is enabling multi platform applications to communicate with each other using the same language. The type system here is 
not entirely dynamic but there might be new extra columns to deal with without recompiling the code

## Widely used intertwined integration types
> This are the commonly used types among different platforms for integrated applications and may be added as overloading for 
methods specialized on dealing with each data type possibly received from a transmission

```lua
/* based upon */ :u8 / /* C# */ (byte) / /* JS */ :Number /*
 *                                          type hits | suggestions
 * abstract type       value        /\/  .NET      SQL         TS        size */
/* financial  */ = | 01010000 | --   80   decimal   money       Number     16
/* rational   */ = | 00100001 | --   33   float     float(2)    Number     04
/* integral   */ = | 01000011 | --   67   int       int         Number     04
/* cardinal   */ = | 00000111 | --   07   long      bigint      Number     08
/* temporal   */ = | 00001111 | --   15   DateTime   DateTime   Date       08
/* relational */ = | 00011110 | --   30   Guid      identifier  String     16
/* textual    */ = | 01111101 | --   125  String    varchar*    String     ??
```
The above values represent different situations describing a specific value-type. Mos of these should have specific classes representing them in
each environment. 

---
## Const well known value Constraints
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