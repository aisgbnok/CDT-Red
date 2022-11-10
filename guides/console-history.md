# Delete Powershell History

```shell
Remove-Item (Get-PSReadlineOption).HistorySavePath
```

```shell
Set-PSReadlineOption -HistorySaveStyle SaveNothing
```
