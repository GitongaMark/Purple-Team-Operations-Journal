Writing yara rules
source: yara.readthedocs.io


comments
/*
    This is a multi-line comment ...
*/

rule CommentExample   // ... and this is single-line comment
{
    condition:
        false  // just a dummy rule, don't do this
}

Strings
1. Hexadecimal strings used for defining raw sequences of bytes.
2. Text strings. they include:
                              case-insensitive strings
                              wide-character strings
                              xor strings
                              base64 strings
3. Regular expressions

private strings
unrefenced strings


CONDITIONS
are nothing more than boolean expressions as those that can be found in all programming languages.

counting strings
string offsets or virtual addresses
match length
file size
executable entry point
Accessing data at a given position
set of strings
USing anonymous strings with of and for..of
iterating over string occurrences
Iterators
Referencing other rules


MORE ABOUT RULES
Global rules
private rules
rule tags
metadata

USING MODULES
1. PE module
2. Cuckoo module

UNDEFINED VALUES
yara handles undefined values in a way that allows the rule to keep its meaningfulness.

EXTERNAL VARIABLES
Allow you to define rules that depend on values provided from the outside. They include: 
     contains
     startswith
     endswith
     icontains
     istartwith
     iendswith
     matches

INCLUDING FILES
uses the include directive