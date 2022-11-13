# Only integrated security is supported for AX queries


I updated my dynamics Ax development Environment by restoring Database from **Production Database**. 

After the restoring the database, when I run any report form Dynamics Ax, there is an error `Only integrated security is supported for AX queries.`
Checking the SSRS is working fine.

The solution is to redeploy the reports, create a new report folder, open Microsoft Dynamics Ax 2012 Management Shell (make sure you run Powershell as Administrator).
`Publish-AXReport -ReportName *`

