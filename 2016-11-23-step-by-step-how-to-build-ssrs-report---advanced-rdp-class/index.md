# Step by Step to build SSRS Report - Advanced RDP Class


### Choosing RDP for a report

* Data to be rendered cannot be constructed as a query.
* Business logic to be processed depends on the parameters.
* Data can be rendered using existing business logic.
* More filters are to be added at runtime

### RDP Comlex

When a dataset does not provide the complexity needs of your report, you can use the default SrsReportRunController class. A controller class runs the report by creating the user interface (UI), calling SQL Server Reporting Services, and pre-processing the parameters for the report. The following list describes scenarios that require that you extend the SrsReportRunController class to create a custom controller for the report.

Report requires input data to be pre-processed.
Report requires input data to be manipulated or modified.
Multiple reports should be run from the same dialog box.
Report requires that it be run from a form.

### Report programming model

#### Model

A model for an SSRS report can be an AOT query/RDP/business logic.(RDPs class need to be extends SRSReportDataProvider)

#### Controller

1. Report data contract: Implemented by SRSReportDataContract
holds the different contracts used in a report

2. Report controller: Implemented by  SRSReportRunController
starting from parsing the report rdl, binding the contracts, UI builder classes to the report, rendering the UI, invoking the report viewer, and post processing actions after the report is rendered. 

3. Report UI builder:  Implemented by SRSReportDataContractUIBuilder
building the UI based on related contracts.

{{< youtube 6B5cqeeNvH4 >}}
