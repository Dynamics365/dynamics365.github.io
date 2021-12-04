# Expressions or Functions used in SSRS


Expressions are usually used for appearance of the data in a report, change properties of the fields, calculate some values and display them in a proper way, compares values between data of fields and then display them.
 
# Types of Expressions  

We have 3 types:  
- Globals  
- Operators - Arithmetic, Comparison,Concatenation, Logical  
- Common Functions - Text, Date &amp; Time, Math, Inspection, Program Flow, Aggregate, Financial, Conversion, Miscellaneous  

We can see each and every one very deataily
<!--more-->
## 1. Globals
Global expressions executes/works in Page Header and Footer parts only.   

**ExecutionTime**  Shows date and time at when report executes  
**PageNumber** shows page number of each and every page but allowed only in page header and footer  
**ReportName**  displays name of the active report what name we have assigned to the active report  
**UserId** shows current user name like **_company/userID_**  
**Language** displays language like US-English…

## 2. Operators  

### Arithmetic  

`^` power of  
`*`multiplication  
`/` divides two numbers and returns a floating point result  
 `:` divides two numbers and returns a integer result  
`Mod` divides two numbers and returns remainder only  
`adds` two numbers and concatenation for two strings  
`-` subtraction and indicates negative value for numeric values  

<!--more-->
 
### Comparison

Known operators : `< <= > >= <>`  
`Like` compares two strings and return true if matched or else returns False.  
*Ex:* `=Fields!Title.Value Like Fields!LoginID.Value`  

`Is` compare two object reference variables  
*Ex:* `=Fields!Title.Value Is Null`

### Concatenation

`+` and `&` symbols uses for concatenation

### Logical

Known: `And, Not, Or, Xor`  
```sql
SELECT * FROM users where firstname = 'Larry' XOR lastname = 'Smith'`
```
`AndAlso` First condition will check first and if it is true only, goes to next or else it won't need to check. Because our execution time is saving in a logical operation in which more conditions is combined using `AndAlso` function.  
`OrElse` same like above


## 3. Common Functions

### **Text**

`Asc, AscW` returns an integer value represents character code corresponding to a character.  
`Chr, chrw` returns the character associated with the specified character code  
`Filter` =Filter(Fields!Title.Value,"Pr",true,0)  
`Format`=Format(Fields!Price.Value, "#,##0.00"), Format(Fields!Date.Value, "yyyy-MM-dd")  
`FormatCurrency` =formatcurrency(Fields!SickLeaveHours.Value,3)  
`FormatDateTime` =FormatDateTime(Fields!BirthDate.Value,Integer)  
**Ex**:  
_0 returns 6/3/1977  
1 returns Friday, June 03, 1977  
2 returns 6/3/1977  
3 returns 12:00:00AM  
4 returns 00:00_

`FormatNumber` =FormatNumber(Fields!EmployeeID.Value,2), then result is 2.00  
`FormatPercent` ="Percentage : " & formatpercent(Fields!SickLeaveHours.Value)  
`GetChar` =GetChar(Fields!Title.Value,5)   
`InStr` =InStr(Fields!Title.Value,"a")  
`InStrRev` =Instrrev(Fields!Title.Value,"a")  
`LCase` =Lcase(Fields!Title.Value), Change strings into lower case   
`Left` =Left(Fields!Title.Value,4), Returns left side characters from a string  
`Len` =Len(Fields!Title.Value), Finds length of a string  
`LSet` =Lset(Fields!Title.Value,5), Returns some length of a string from left  
`LTrim` =Ltrim(" "&Fields!Title.Value), Trim left side of a string  
`Mid` =Mid(Fields!Title.Value,InSTrRev(Fields!Title.Value,"T")), Returns characters from the   mentioned starting position  
`Replace` =Replace(Fields!Title.Value,"a","A"), Replaces one string with another  
`Right` =Right(Fields!Title.Value,10), Returns right side characters from a string  
`RSet` =Rset(Fields!Title.Value,5),Returns some length of a string from left  
`RTrim` =Rtrim(Fields!Title.Value & " "), Trim left side of a string  
`Space` =Fields!Title.Value & Space(5) & Fields!Title.Value, Specifies some spaces within strings  
`StrComp` Returns a value indicating the result of a string comparison  
`vbBinaryCompare 0` Perform a binary comparison.  
`vbTextCompare 1` Perform a textual comparison.  
`string1` is less than `string2` -1   
`string1` is equal to `string2` 0  
`string1` is greater than `string2` 1  
`string1` or `string2` is Null Null  
`StrConv`  
=Strconv(Fields!Title.Value,vbProperCase)  
=Strconv(Fields!Title.Value,vbLowerCase)  
=Strconv(Fields!Title.Value,vbUpperCase)  
`StrDup` =StrDup(3,"M"), Returns a string or object consisting of the specified character repeated the specified number of times.  
`StrReverse` =StrReverse(Fields!Title.Value)  
`Trim` =Trim(" "& Fields!Title.Value & " ")  
`UCase` =Ucase(Fields!Title.Value)  

### **Date & Time**

`CDate` Converts a object into date format  
=Format(CDate(Fields!BirthDate.Value),"MMMM yyyy")  
`DateAdd` Returns a datetime that is the result of adding the specified number of time interval units to the original datetime.  
=dateadd("m",12,Fields!BirthDate.Value)  
`DateDiff` Find number of days, months and years between two dates  
=datediff("d",Fields!BirthDate.Value,Now)  
`DatePart` DatePart(DateInterval.Weekday, CDate("2009/11/13"), FirstDayOfWeek.Monday) returns 5 (Friday)  
`DateSerial` for first day of the month  
=DateSerial(Year(Now), Month(Now), 1)  
for the last day of the month  
=DateSerial(Year(Now), Month(Now)+1, 0)    
`DateString` Returns string value of system date  
=datestring()  
`DateValue` Returns current date  
`Day` Returns day value from date  
=day(Fields!BirthDate.Value)  
`FormatDateTime` =FormatDateTime(Fields!BirthDate.Value,Integer)
*Examples:*
0 returns 6/3/1977  
1 returns Friday, June 03, 1977  
2 returns 6/3/1977  
3 returns 12:00:00AM  
4 returns 00:00  
`Hour` =Hour(Fields!BirthDate.Value)  
`Minute` =Minute(Fields!BirthDate.Value)  
`Month` =Month(Fields!BirthDate.Value)  
`MonthName` =MonthName(Month(Fields!BirthDate.Value))  
`Now` Indicates current month  
=Now() or =Now  
`Second` =Second(Fields!BirthDate.Value)  
`TimeOfDay` =TimeOfDay()  
Returns a date value containing the current time of day according to your system  
`Timer` =Timer()    
Returns number of seconds elapsed since midnight  
`TimeSerial` =TimeSerial(24,60,60)  
Returns a date value representing a specified hour, minute and second  
`TimeString` =TimeString()  
Returns string value representing the current time of day according to your system  
`TimeValue` Returns a date value set to jan 1 of year 1  
=TimeValue(Fields!BirthDate.Value)  
`Today` Returns Current date  
`Weekday` Returns an integer value representing day of week  
=WeekDay(Fields!BirthDate.Value)  
`WeekdayName` =WeekdayName(Weekday(Fields!BirthDate.Value))  
Returns name of the day of week  
`Year` =year(Fields!BirthDate.Value)  
Returns year of specified date  

### Math

`Abs=Abs(-2.36)`

Returns the absolute value

`BigMul =BigMul(2,3)`

Returns multiplication value of two specified numbers

`Ceiling =Ceiling(2.67)`

Returns next highest value

`Cos=Cos(2.33)`

Returns cos value for specified number

`Cosh=Cosh(2.33)`
Returns hyperbolic cos value

`DivRem=DivRem(23,2,5)`

`Fix=Fix(23.89)`

Returns integer portion

`Floor=Floor(24.54)`

Returns largest integer

`Int=Int(24.78)`

Returns integer portion of a number

`Log=Log(24.78)`

Returns logarithm value

`Log10=Log10(24.78)`

Returns the base 10 logaritm value

`Max=Max(Fields!EmployeeID.Value)`

Returns larger value in the specified values

`Min=Min(Fields!EmployeeID.Value)`

Returns smaller value in the specified values

`Pow=Pow(Fields!EmployeeID.Value,2)`

Returns power of value for specified number

`Rnd=Rnd()`

Returns a random number

`Round=Round(43.16)`

Returns rounded value to the nearest integer

`Sign=Sign(-34534543)`

`Sin=Sin(Fields!EmployeeID.Value)`
Returns the sin value

`Sinh=Sinh(Fields!EmployeeID.Value)`
Returns the hyperbolic sin value


### Inspection

`IsArray
=IsArray(Fields!EmployeeID.Value)`

Returns a boolean value indicating whether the specified object is array or not

`IsDate
=IsDate(Fields!BirthDate.Value)`

Returns a boolean value indicating whether the specified object is Date or not

`IsNothing
=IsNothing(Fields!EmployeeID.Value)`

Returns a boolean value depends on specified object is Nothing or not

`IsNumeric
=IsNumeric(Fields!EmployeeID.Value)`

Returns a boolean value depends on specified object is Numeric value or not

### Program Flow

`Choose
=CHOOSE(3, "Red", "Yellow", "Green", "White")`

Returns a specific value using index in a list of arguments

`IIf
=IIF(Fields!EmployeeID.Value>10,"Yes","No")`

Returns any one value depends on condition

`Switch
=Switch(Fields!EmployeeID.Value<10,"Red",Fields!EmployeeID.Value>10,"Green")`

Evaluates list of expressions

### Aggregate

`Avg
=Avg(Fields!EmployeeID.Value)`

Returns average value for all specified values

`Count
=Count(Fields!EmployeeID.Value)`

Returns count of all specified values

`CountDistinct
=CountDistinct(Fields!EmployeeID.Value)`

Returns count of all distinct values

`CountRows
=CountRows()`

Returns count of rows

`First
=First(Fields!EmployeeID.Value)`

Returns first for all specified values

`Last
=Last(Fields!EmployeeID.Value)`

Returns last for all specified values

`Max
=Max(Fields!EmployeeID.Value)`

Returns max for all specified values

`Min
=Min(Fields!EmployeeID.Value)`

Returns min for all specified values

`StDev
=StDev(Fields!EmployeeID.Value)`

Returns standard deviation value

`StDevP
=StDevP(Fields!EmployeeID.Value)`

Returns Population standard deviation value

`Sum
=Sum(Fields!EmployeeID.Value)`

Returns sum of all values

`Var
=Var(Fields!EmployeeID.Value)`

Returns variance of all values

`VarP
=Var(Fields!EmployeeID.Value)`

Returns population variance of all values

`RunningValue
=RunningValue(Fields!EmployeeID.Value,sum,nothing)`

Returns running aggregate of the specified
expression

### Financial

`DDB DDB` (Double Declining Balance) method computes depreciation of an asset for a specified period.

Syntax: `DDB (Cost, Salvage, life, period, factor)`

`FV FV` (Future Value) of an investment based on periodic, constant payments and a constant interest rate.

Syntax: `FV (rate, nper, pmt, pv, type)`

`IPmt IPmt` (Interest Payment) for a given period for an investment based on periodic, constant payment and a constant interest rate

`IPMT (rate, per, nper, pv, fv, type)`

`IRR IRR` (Interest Rate of Return) for a series of cash flows represented by the numbers in values.

`IRR(values,guess)`

`MIRR MIRR` ( Modified internal rate of return ) for a series of periodic cash flows

`MIRR(values,finance_rate,reinvest_rate)`

`NPer` Returns the number of periods for an investment based on periodic, constant payments and a constant interest rate.

`NPER (rate, pmt, pv, fv, type)`

`NPV` Calculates the net present value of an investment by using a discount rate and a series of future payments (negative values) and income (positive values).

Syntax: `NPV(rate,value1,value2, ...)`

`Pmt` Calculates the payment for a loan based on constant payments and a constant interest rate.

`PMT(rate,nper,pv,fv,type)`

PPmt Returns the payment on the principal for a given period for an investment based on periodic, constant payments and a constant interest rate.

`PPMT(rate,per,nper,pv,fv,type)`

PV Returns the present value of an investment. The present value is the total amount that a series of future payments is worth now. For example, when you borrow money, the loan amount is the present value to the lender.

`PV(rate,nper,pmt,fv,type)`

Rate Returns the interest rate per period of an annuity. RATE is calculated by iteration and can have zero or more solutions.

`RATE(nper,pmt,pv,fv,type,guess)`

SLN Returns the straight-line depreciation of an asset for one period.

`SLN(cost,salvage,life)`

SYD Returns the sum-of-years' digits depreciation of an asset for a specified period.
`SYD(cost,salvage,life,per)`

### Conversion

`CBool` Convert to boolean

`CByte` Convert to byte

`CChar` Convert to char

`CDate` Convert to date

`CDbl` Convert to double

`CDec` Convert to decimal

`CInt` Convert to integer

`CLng` Convert to long

`CObj` Convert to object

`CShort` Convert to short

`CSng` Convert to single

`CStr` Convert to string

`Hex =Hex(Fields!EmployeeID.Value)`

Returns a hexadecimal value of a number

`Int =Int(43.44)`

Returns integer portion of a number

`Oct =Oct(Fields!EmployeeID.Value)`

Returns a octal value of a number

`Str =Str(Fields!EmployeeID.Value)`

Returns string value of a number

`Val =Val("32.43")`

Returns numeric value in string format


### Miscellaneous

`Previous =Previous(Fields!EmployeeID.Value)`

Returns the previous value
