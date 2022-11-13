# Date time function Dynamics 365 Finance Operations




```C#
-- string to date and time
str dateStrYMD = '2022-04-12T13:34:30';
UtcDateTime datetime = DateTimeUtil::anyToDateTime(dateStrYMD);

-- Year difference
int timeDiff = Global::yearDiff(DateTimeUtil::getSystemDateTime(), DateTimeUtil::date(_myTable.myUTCDateTime));

-- datetime compare
DateTimeUtil::newDateTime(today()-3, 0, DateTimeUtil::getCompanyTimeZone()) > datetime

```
