# A good tip of using Excel to analyse INSERT INTO statement captured in a Trace file


More than often, you will come across INSERT INTO statements when analysing D365FO trace files.

Below is a good trick to know which values that the system was trying to insert into the tables.

First, copy everything from the statement (click on the `Code` section > `Ctrl + A` > `Ctrl + C`).

![Copy the query](Pic3-1.PNG "copy the query")

Paste the script into Excel.

![Paste to Excel](Pic3-2.PNG "Paste to Excel")

Remove any empty rows.

![Remove the empty rows](Pic3-3.PNG "Remove the empty rows")

Find the `VALUES` keyword (usually after `RECID`). Insert a new row right below that row you found and cut the VALUES part to it.

*Before*

![Values part before](Pic3-4.PNG "Values part before")

*After*

![Values part after](Pic3-5.PNG "Values part after")

Copy from VALUES to the end of the script (Choose the VALUES cell > `Shift + Ctrl + Down Arrow`).

![Copy the part](Pic3-6.PNG "Copy the part")

Paste the copied values to any next columns, starting from the row where the very first column of the table appears.

![Move the part](Pic3-7.PNG "Move the part")

There, you can look for the columns with their inserted corresponding values.

For example:

- ExpandID = '0'

![ExpandID](Pic3-8.PNG "ExpandID")

- RecID = '5637233827'

![RecID](Pic3-9.PNG "RecID")

- ForecastEntryNumber = 'PAU-000063760'

![ForecastEntryNumber](Pic3-10.PNG "ForecastEntryNumber")
