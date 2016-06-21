## 準備
### スクリプト実行許可
```powershell
Set-ExecutionPolicy RemoteSigned 
```
### リモート操作を有効に
```powershell
Enable-PSRemoting
```

## 例（全部）
### hello world
```powershell
Configuration HelloPowerShellDSC
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node "localhost"
    {
        File HelloDSCFile
        {
            DestinationPath = "c:\HelloDSCFile.txt"
            Ensure = "Present"
            Type = "File"
            Contents = "Hello PowerShell DSC World!!" 
        }
    }
}

HelloPowerShellDSC -OutputPath .
Start-DscConfiguration .\HelloPowerShellDSC -Wait -Verbose
```

## 参考
* [PowerShell DSCで導入された新しい構文キーワード](http://www.buildinsider.net/enterprise/powershelldsc/01)
