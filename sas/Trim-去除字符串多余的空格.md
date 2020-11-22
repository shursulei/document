# TRIM Function

Removes trailing blanks from a character expression, and returns a string with a length of zero if the expression is missing.

| Alias:              | TRIMN                          |
| ------------------- | ------------------------------ |
| Returned data type: | CHAR, NCHAR, NVARCHAR, VARCHAR |

Table of Contents

[Syntax](https://go.documentation.sas.com/#n0eufu63n7w80fn1a1573xvriu1b)

[Arguments](https://go.documentation.sas.com/#p0yz3u36hhbxltn17dot1l17ixj3)

[Details](https://go.documentation.sas.com/#n1tz6lsx5f1jf2n1ow3lf5cm4xon)

[Example](https://go.documentation.sas.com/#p0idt4fttcam9pn1jpgvfdrynvh2)

## Syntax

TRIM('*expression*')

### Arguments

#### *expression*

specifies any valid expression that evaluates or can be coerced to a character string.

| Data type | CHAR, NCHAR, NVARCHAR, VARCHAR |
| --------- | ------------------------------ |
|           |                                |

## Details

The TRIM function copies a character argument, removes trailing blanks, and returns the trimmed argument as a result. If the argument is blank, TRIM returns a string with a length of zero. TRIM is useful after concatenating because concatenation does not remove trailing blanks.

Note: The TRIM function removes both blanks and whitespace characters as defined by the Unicode standard. Consequently, the TRIM function also handle DBCS blanks and shift out/shift in (SO/SI) escape codes. For more information about the Unicode whitespace character standard, see [Wikipedia: Unicode character property](https://en.wikipedia.org/wiki/Unicode_character_property#Whitespace).

## Example

The following statements illustrate the TRIM function:

| Statements                                                   | Results                    |
| :----------------------------------------------------------- | :------------------------- |
| `proc cas;   string1='Testscore   ';   string2='File.xls';   result=trim(string1)||(string2);   print "result=" result; run;` | `result=TestscoreFile.xls` |