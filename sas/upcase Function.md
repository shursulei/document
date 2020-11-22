# UPCASE Function

Converts all letters in an argument to uppercase.

| Alias:              | UPPER                          |
| ------------------- | ------------------------------ |
| Returned data type: | CHAR, NCHAR, NVARCHAR, VARCHAR |

Table of Contents

[Syntax](https://go.documentation.sas.com/#p18dyaj6h361aen1l9vpaxbyaqkp)

[Arguments](https://go.documentation.sas.com/#p0n6j5n0ty4xy4n1bmz51whikzvu)

[Details](https://go.documentation.sas.com/#n0nq3puuurwmjon1o83c22squfxf)

[Comparisons](https://go.documentation.sas.com/#p1hm9r31cmrw3gn1cbzlshnpbrle)

[Example](https://go.documentation.sas.com/#n1dzsjo6kyil4rn1hcqj5e0ikjy8)

## Syntax

UPCASE(*expression*)

### Arguments

#### *expression*

specifies any valid expression that evaluates or can be coerced to a character string.

| Data type | CHAR, NCHAR |
| --------- | ----------- |
|           |             |

## Details

The UPCASE function copies a character expression, converts all lowercase letters to uppercase letters, and returns the altered value as a result.

## Comparisons

The LOWCASE function converts all letters in an argument to lowercase letters. The UPCASE function converts all letters in an argument to uppercase letters.

## Example

The following statement illustrates the UPCASE function:

| Statements                      | Results         |
| :------------------------------ | :-------------- |
| `name=upcase('John B. Smith');` | `JOHN B. SMITH` |