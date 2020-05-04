# FromHome



# PowerShell Install

```cmd
> brew cask install powershell
> pwsh
```
# PowerShellGet の設定

PowerShellで扱うモジュールをインストールする際、
PowerShellGet にインストールをやってもらうよう設定

```ps1
> Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

# AzTable モジュールをインストール

```ps1
> Install-Module AzTable
```

```ps1
> Add-AzAccount
```

# Get Storage Account Context
$resourceGroupName = 'WebPageFromHome'
$storageAccountName = 'fromhome'
$storageAccount = Get-AzStorageAccount -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName
$ctx = $storageAccount.Context

# New Table
$tableName = "menu"
New-AzStorageTable –Name $tableName –Context $ctx

$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable

# New Record To Table 

$key = "Drink"
$row1Name = "Type"
$row1Value = "Coffee&Tea"
$row2Name = "Price"

$rowKey = "Darjeeling"
$row2Value = 100

$partitionKey1 = "menu"
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $key `
    -rowKey ($rowKey) -property @{$row1Name=$row1Value;$row2Name=$row2Value}


# Get Table
$table = Get-AzTableRow -table $cloudTable

# Update Table
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("RowKey",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Coffee")
$menu = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

$menu.price = 100
$menu | Update-AzTableRow -table $cloudTable

# Delete Record
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("RowKey",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Coffee")
$menu = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

$menu  | Remove-AzTableRow -table $cloudTable

# Delete Table
Remove-AzStorageTable –Name $tableName –Context $ctx



