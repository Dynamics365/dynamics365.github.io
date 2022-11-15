# Modify Microsoft Dynamics AX 2012 R3 SSRS configurations using PowerShell


`reportManagerWebConfig.ps1`

```powershell
#Modify the Report Server Web.config file. ie replace MSRS11.VAS with your folder name
Set-ExecutionPolicy Unrestricted
$webConfig = "C:Program FilesMicrosoft SQL ServerMSRS11.VASReporting ServicesReportManagerWeb.config"
$currentDate = (get-date).tostring("mm_dd_yyyy-hh_mm_s") # month_day_year - hours_mins_seconds
$backup = $webConfig + "_$currentDate"
$doc = new-object System.Xml.XmlDocument
$doc.Load($webConfig)
#save a backup copy
$doc.Save($backup)
Write-Host "Backup saved as " + $backup
$node = $doc.get_DocumentElement()."system.web".httpRuntime
$attribute = $doc.CreateAttribute("maxRequestLength")
$attribute.set_Value("100000")
$node.SetAttributeNode($attribute )
$doc.Save($webConfig)
Write-Host "1) Modified the Report Manager Web.config file"
```

` RsReportServer.ps1`

```powershell
#Modify the Report Server Web.config file. ie replace MSRS11.VAS with your folder name
Set-ExecutionPolicy Unrestricted

$version = "6.3.0.0"
$webConfig = "C:Program FilesMicrosoft SQL ServerMSRS11.VASReporting ServicesReportServerRsReportServer.config"
$currentDate = (get-date).tostring("mm_dd_yyyy-hh_mm_s") # month_day_year - hours_mins_seconds
$backup = $webConfig +  ".xml" #"_$currentDate"
$doc = new-object System.Xml.XmlDocument

$doc.Load($webConfig)

#save a backup copy
$doc.Save($backup)
Write
Write-Host  "Backup saved as " + $backup
#remove node "RSWindowsNegotiate" 
$node = $doc.documentElement.SelectSingleNode("//Configuration/Authentication/AuthenticationTypes/RSWindowsNegotiate")
if ($node) 
{
    
{
    $node.ParentNode.RemoveChild($node)
    Write
    Write-Host  "RSWindowsNegotiate Removed"
}
#Add node "IsRdceEnabled" 
$nodeService = $doc.documentElement.SelectSingleNode("//Configuration/Service")
$nodeIsRdceEnabled = $doc.documentElement.SelectSingleNode("//Configuration/Service/IsRdceEnabled")


#Set to true if already exists, otherwise create the node
if ($nodeIsRdceEnabled)
{
    
{
    $nodeIsRdceEnabled.InnerXml = "True"
}
else
{
    if ($nodeService)
    {
        
    {
        $subnodeService = $doc.createElement("IsRdceEnabled")
        
        $subnodeService.InnerXml = "True"
        $nodeService.appendChild($subnodeService)
        Write
        Write-Host  "IsRdceEnabled added"
    }
}
#Find Data
$nodeCodeAxQuery = $doc.documentElement.SelectSingleNode("//Configuration/Extensions/Data/Extension[@Name='AXQUERY']")

#Add Data Extension
if (-not($nodeCodeAxQuery))
{
    
{
    $nodeData = $doc.documentElement.SelectSingleNode("//Configuration/Extensions/Data")
    
    if ($nodeData) 
    {
        
    {
        $nodeData.InnerXml = $nodeData.InnerXml + 
        
        "<Extension Name='AXQUERY' Type='Microsoft.Dynamics.Framework.Reports.AxQueryConnection,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>
      <Extension Name='AXDATAMETHOD' Type='Microsoft.Dynamics.Framework.Reports.AxDataMethodConnection,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>
      <Extension Name='AXREPORTDATAPROVIDER' Type='Microsoft.Dynamics.Framework.Reports.AxReportProviderConnection,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>
      <Extension Name='AXADOMD' Type='Microsoft.Dynamics.Framework.Reports.AxAdomdConnection,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>
      <Extension Name='AXENUMDATAPROVIDER' Type='Microsoft.Dynamics.Framework.Reports.EnumProviderConnection,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>"
        Write-Host  "Data Extension added"
    }
}
#Find Extensions
$nodeExtensions = $doc.documentElement.SelectSingleNode("//Configuration/Extensions")
$nodeReportDefinitionCustomization = $doc.documentElement.SelectSingleNode("//Configuration/Extensions/ReportDefinitionCustomization")

if (-not($nodeReportDefinitionCustomization))
{
    
{
    #Add ReportDefinitionCustomization
    if ($nodeExtensions) 
    {
        
    {
        $subnodeExt = $doc.createElement("ReportDefinitionCustomization")
        
        $subnodeExt.InnerXml = "<Extension Name='AXRDCE' Type='Microsoft.Dynamics.Framework.Reports.AxRdce.CustomizationExtension,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>"
        $nodeExtensions.appendChild($subnodeExt)
        Write
        Write-Host  "ReportDefinitionCustomization added"
    }
}
else
{
    $nodeAXRDCE= $doc.documentElement.SelectSingleNode("//Configuration/Extensions/ReportDefinitionCustomization/Extension [@Name='AXRDCE']")
    
    if (-not($nodeAXRDCE))
    {
        
    {
        $nodeReportDefinitionCustomization.InnerXml = $nodeReportDefinitionCustomization.InnerXml +
            "<Extension Name='AXRDCE' Type='Microsoft.Dynamics.Framework.Reports.AxRdce.CustomizationExtension,Microsoft.Dynamics.Framework.ReportsExtensions, Version=" +
		$version +
		", Culture=neutral, PublicKeyToken=31bf3856ad364e35'/>"
    }
}
$doc.Save($webConfig)

Write
Write-Host  "2) Modified the Report Server Web.config file"
text some thing
```

`ReportServerRsSrvPolicyConfig.ps1`

```powershell
#Instructions: Modify the Report Server Web.config file. ie replace MSRS11.VAS with your folder name
# Modify version 6.3.0.0 for Dynamics AX 2012
Set-ExecutionPolicy Unrestricted
$version = "6.3.0.0"
$webConfig = "C:Program FilesMicrosoft SQL ServerMSRS11.VASReporting ServicesReportServerrssrvpolicy.config"
$currentDate = (get-date).tostring("mm_dd_yyyy-hh_mm_s") # month_day_year - hours_mins_seconds
$backup = $webConfig + ".xml" #"_$currentDate"
$doc = new-object System.Xml.XmlDocument
$doc.Load($webConfig)
#save a backup copy
$doc.Save($backup)
Write
Write-Host "Backup saved as " + $backup
#Add SecurityClass
$nodeAxSessionPermission = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/SecurityClasses/SecurityClass[@Name='AxSessionPermission']")
if (-not($nodeAxSessionPermission))
{
{
 $nodeSecurityClasses = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/SecurityClasses")
 if ($nodeSecurityClasses)
{
 {
 $nodeSecurityClass = $doc.createElement("SecurityClass")
 $nodeSecurityClass.InnerXml = "<SecurityClass Name='AxSessionPermission' Description='Microsoft.Dynamics.Framework.Reports.AxSessionPermission, Microsoft.Dynamics.Framework.Reports, Version=" +
 $version +
 ", Culture=neutral, PublicKeyToken=31bf3856ad364e35' />"
 $nodeSecurityClasses.appendChild($nodeSecurityClass.FirstChild)
Write
 Write-Host "SecurityClass added"
 }
}
#Add PermissionSet
$nodeAxSessionPermissionSet = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/NamedPermissionSets/PermissionSet[@class='NamedPermissionSet' and @version='1' and @Name='AxSessionPermissionSet']")
if (-not($nodeAxSessionPermissionSet))
{
{
 $nodeNamedPermissionSets = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/NamedPermissionSets")
 if ($nodeNamedPermissionSets)
{
 {
 $nodePermissionSet = $doc.createElement("PermissionSet")
 $nodePermissionSet.InnerXml = "<PermissionSet class='NamedPermissionSet' version='1' Name='AxSessionPermissionSet'>
 <IPermission class='AxSessionPermission' version='1' Unrestricted='true' />
 <IPermission class='SecurityPermission' version='1' Flags='Assertion' />
 </PermissionSet>"
 $nodeNamedPermissionSets.appendChild($nodePermissionSet.FirstChild)
Write
 Write-Host "PermissionSet added"
 }
}
#Add CodeGroup
$nodeAxStrongName = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/CodeGroup[@class='FirstMatchCodeGroup' and @version='1' and @PermissionSetName='Nothing']/CodeGroup[@class='UnionCodeGroup' and @version='1' and @PermissionSetName='FullTrust' and @Name='AX_Reports_Strong_Name']")
if (-not($nodeAxStrongName))
{
{
 $nodeCodeGroup = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/CodeGroup[@class='FirstMatchCodeGroup' and @version='1' and @PermissionSetName='Nothing']")
 if ($nodeCodeGroup)
{
 {
 $subnodeCodeGroup = $doc.createElement("CodeGroup")
 $subnodeCodeGroup.InnerXml = "<CodeGroup class='UnionCodeGroup' version='1' PermissionSetName='FullTrust' Name='AX_Reports_Strong_Name' Description='This code group grants Dynamics AX Reports code full trust. '>
 <IMembershipCondition class='StrongNameMembershipCondition' version='1' PublicKeyBlob='0024000004800000940000000602000000240000525341310004000001000100B5FC90E7027F67871E773A8FDE8938C81DD402BA65B9201D60593E96C492651E889CC13F1415EBB53FAC1131AE0BD333C5EE6021672D9718EA31A8AEBD0DA0072F25D87DBA6FC90FFD598ED4DA35E44C398C454307E8E33B8426143DAEC9F596836F97C8F74750E5975C64E2189F45DEF46B2A2B1247ADC3652BF5C308055DA9' />
 </CodeGroup>"
 $nodeCodeGroup.appendChild($subnodeCodeGroup.FirstChild)
Write
 Write-Host "CodeGroup added"
 }
}
#Add nodeReportExpressionPermission
#http://blogs.msdn.com/b/axsupport/archive/2012/02/02/microsoft-dynamics-ax-2012-reporting-extensions-error-system-security-permissions-environmentpermission-while-running-report.aspx
$nodeReportExpressionPermission = $doc.documentElement.SelectSingleNode("//configuration/mscorlib/security/policy/PolicyLevel/CodeGroup[@class='FirstMatchCodeGroup' and @version='1' and @PermissionSetName='Nothing']/CodeGroup[@class='UnionCodeGroup' and @version='1' and @PermissionSetName='Execution' and @Name='Report_Expressions_Default_Permissions']")
if ($nodeReportExpressionPermission)
{
{
 $attribute = $doc.CreateAttribute("PermissionSetName")
 $attribute.set_Value("FullTrust")
 $nodeReportExpressionPermission.SetAttributeNode($attribute )
}
}
$doc.Save($webConfig)
Write
Write-Host "3) Modified the Report Server rssrvpolicy.config file"
```
