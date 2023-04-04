Сборник софта для Windows предназначенного для анализа малвари 
**Ссылка на Github: https://github.com/mandiant/flare-vm**
## Установка
Запускать от администратора

```powershell
(New-Object net.webclient).DownloadFile('https://raw.githubusercontent.com/mandiant/flare-vm/main/install.ps1',"$([Environment]::GetFolderPath("Desktop"))\install.ps1")
Unblock-File .\install.ps1
Set-ExecutionPolicy Unrestricted
.\install.ps1

```
