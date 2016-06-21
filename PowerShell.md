## 準備
### スクリプト実行許可
```
Set-ExecutionPolicy RemoteSigned 
```
### リモート操作を有効に
```
Enable-PSRemoting
```

## 例
### hello world
```
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
