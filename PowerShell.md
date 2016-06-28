## 準備
### スクリプト実行許可
```powershell
Set-ExecutionPolicy RemoteSigned 
```
### リモート操作を有効に
```powershell
Enable-PSRemoting
```

### モジュールの検証
インストールせずに、ローカルに落として使えるようにする
```powershell
Save-Module -Name <module> -Path <path> 
```

### モジュールのインストール
```powershell
Install-Module -Name <module>
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

## 例（部分）
### ファイアウォール

Ping受信ルール

```powershell
Import-DSCResource -ModuleName xNetworking

Node localhost
{ 
    xFirewall Firewall
    { 
        Name = "MyFirewallRule" 
        DisplayName = "ファイルとプリンターの共有 (エコー要求 - ICMPv4 受信)" 
        Group = "ファイルとプリンターの共有"
        Ensure = "Present"
        Action = "Allow"
        Enabled = "True"
        Direction = "Inbound"
        Profile = "Domain"
        Protocol = "TCP"
    }
} 
```

## 参考
* [PowerShell DSCで導入された新しい構文キーワード](http://www.buildinsider.net/enterprise/powershelldsc/01)
