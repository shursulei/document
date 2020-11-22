# %UPCASE and %QUPCASE Functions

Convert values to uppercase.

| Type: | Macro function                                               |
| ----- | ------------------------------------------------------------ |
| See:  | [%LOWCASE and %QLOWCASE Autocall Macros](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=mcrolref&docsetTarget=p0z7ubhlepe3h7n1ivnyuawn6yze.htm&locale=en), [%NRBQUOTE Function](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=mcrolref&docsetTarget=p074ox0qkbckkan153eooocwkd0e.htm&locale=en), and [%QLOWCASE Autocall Macro](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=mcrolref&docsetTarget=n092d37ph0kexjn123vlgp1hy74l.htm&locale=en) |

Table of Contents

[Syntax](https://go.documentation.sas.com/#p0bd5dkyia0hrin1xi9gmqipuheb)

[Details](https://go.documentation.sas.com/#p0oxrw7e4txhben18madzg92t65t)

[Comparisons](https://go.documentation.sas.com/#n130jjssg61amzn1q3ch2wxf6zdp)

[Examples](https://go.documentation.sas.com/#p1quodbmg9qzzln1mp6bdbdcnn8w)

[Example 1: Capitalizing a Value to Be Compared](https://go.documentation.sas.com/#p12jaab7u2ew90n1sxu9jgggserq)

[Example 2: Comparing %UPCASE and %QUPCASE](https://go.documentation.sas.com/#n07ypg4dxc8pi3n1ly8mxvpat1yf)

## Syntax

%UPCASE(*character-string* | *text-expression*)

%QUPCASE(*character-string* | *text-expression*)

## Details

The %UPCASE and %QUPCASE functions convert lowercase characters in the argument to uppercase. %UPCASE does not mask special characters or mnemonic operators in its result, even when the argument was previously masked by a [macro](https://go.documentation.sas.com/) [quoting function](https://go.documentation.sas.com/).

If the argument contains a special character or mnemonic operator, listed below, use %QUPCASE. %QUPCASE masks the following special characters and mnemonic operators in its result:

```
& % ' " ( ) + − * / < > = ¬ ^ ~ ; , # blank
AND OR NOT EQ NE LE LT GE GT IN
```

%UPCASE and %QUPCASE are useful in the comparison of values because the [macro facility](https://go.documentation.sas.com/) does not automatically convert lowercase characters to uppercase before comparing values.

## Comparisons



- %QUPCASE masks the same characters as the %NRBQUOTE function.
- To convert characters to lowercase, use the %LOWCASE or %QLOWCASE [autocall macro](https://go.documentation.sas.com/).



## Examples

### Example 1: Capitalizing a Value to Be Compared

In this example, the macro RUNREPT compares a value input for the [macro variable](https://go.documentation.sas.com/) MONTH to the string DEC. If the uppercase value of the response is DEC, then PROC FSVIEW runs on the data set Reports.EndYear. Otherwise, PROC FSVIEW runs on the data set with the name of the month in the Reports data library.

```
%macro runrept(month);
   %if %upcase(&month)=DEC %then
       %str(proc fsview data=reports.endyear; run;);
   %else %str(proc fsview data=reports.&month; run;);
%mend runrept;
```

You can invoke the macro in any of these ways to satisfy the %IF condition:

```
%runrept(DEC)
%runrept(Dec)
%runrept(dec)
```

### Example 2: Comparing %UPCASE and %QUPCASE

These statements show the results produced by %UPCASE and %QUPCASE:

```
%let a=begin;
%let b=%nrstr(&a);
%put UPCASE produces: %upcase(&b);
%put QUPCASE produces: %qupcase(&b);
```

When these statements execute, the following is written to the SAS log:

```
UPCASE produces: begin
QUPCASE produces: &A
```