

# Delete custom SharePoint site designs

## Summary

Site designs and especially site scripts can be something that ends up just hanging around in your tenant for a long time even though you no longer need them for anything. Use the scripts below to get rid of them. You might also find some site scripts that are not linked to any site design and hence never get executed!
 
[!INCLUDE [Delete Warning](../../docfx/includes/DELETE-WARN.md)]

# [SPO Management Shell](#tab/spoms-ps)
```powershell
Connect-SPOService "https://contoso-admin.sharepoint.com"

$keepThese = "Register the new site", "Corporate Basic Site", "Corporate Internal Site"
$siteDesigns = Get-SPOSiteDesign
$siteDesigns = $siteDesigns | Where-Object { -not ($keepThese -contains $_.Title)}

if ($siteDesigns.Count -eq 0) { break }

$siteDesigns | Format-Table Title, SiteScriptIds, Description
Read-Host -Prompt "Press Enter to start deleting (CTRL + C to exit)"
$progress = 0
$total = $siteDesigns.Count

foreach ($siteDesign in $siteDesigns)
{
  $progress++
  Write-Host $progress / $total":" $siteDesign.Title
  Remove-SPOSiteDesign $siteDesign.Id
}
```
[!INCLUDE [More about SPO Management Shell](../../docfx/includes/MORE-SPOMS.md)]

# [PnP PowerShell](#tab/pnpps)

```powershell

$tenantAdminUrl = "https://contoso-admin.sharepoint.com"

Connect-PnPOnline -Url $tenantAdminUrl -Interactive

$SiteDesignTitlesToKeep = "Cat Lovers United", "Multicolored theme"
$sitedesigns = Get-PnPSiteDesign
$sitedesigns = $sitedesigns | where {-not ($sparksjoy -contains $_.Title)}
$sitedesigns | Format-Table Title, SiteScriptIds, Description
if ($sitedesigns.Count -eq 0) { break }
Read-Host -Prompt "Press Enter to start deleting (CTRL + C to exit)"
$progress = 0
$total = $sitedesigns.Count
foreach ($sitedesign in $sitedesigns)
{
  $progress++
  write-host $progress / $total":" $sitedesign.Title
  Remove-PnPSiteDesign -Identity $sitedesign.Id -Force:$true
}

```
[!INCLUDE [More about PnP PowerShell](../../docfx/includes/MORE-PNPPS.md)]

# [CLI for Microsoft 365](#tab/cli-m365-ps)

```powershell

# Get Credentials to connect
$m365Status = m365 status
if ($m365Status -match "Logged Out") {
   m365 login
}

$keepThese = "Register the new site", "Corporate Basic Site", "Corporate Internal Site"

# Get all site designs from the current tenant
$siteDesigns = m365 spo sitedesign list | ConvertFrom-Json

$siteDesigns = $siteDesigns | Where-Object { -not ($keepThese -contains $_.Title)}

if ($siteDesigns.Count -eq 0) { break }

$siteDesigns | Format-Table Title, SiteScriptIds, Description

Read-Host -Prompt "Press Enter to start deleting (CTRL + C to exit)"
$progress = 0
$total = $siteDesigns.Count

foreach ($siteDesign in $siteDesigns)
{
  $progress++
  Write-Host $progress / $total":" $siteDesign.Title
  m365 spo sitedesign remove --id $siteDesign.Id
}

```
[!INCLUDE [More about CLI for Microsoft 365](../../docfx/includes/MORE-CLIM365.md)]

***

## Contributors

| Author(s) |
|-----------|
| Paul Bullock |
| [Leon Armston](https://github.com/LeonArmston)|
| [Ganesh Sanap](https://ganeshsanapblogs.wordpress.com/about) |

[!INCLUDE [DISCLAIMER](../../docfx/includes/DISCLAIMER.md)]
<img src="https://m365-visitor-stats.azurewebsites.net/script-samples/scripts/spo-remove-site-designs" aria-hidden="true" />
