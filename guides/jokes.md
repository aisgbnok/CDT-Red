# Joke Service

## Description

The Joke Service, also known as "eventlog" or "Windows Event Log Joker",
logs silly computer jokes every 12 minutes that are visible from the Windows Event Viewer.
It is actually harmless, other than consuming a few megabytes of memory.
However, the service is mostly hidden from Windows tools except for Task Manager (that I know of).
The Joke Service was build using .NET 7.0, targets Windows 10 19041, and is supported on Windows 7 and up.

## Install

1. [Download the `eventlogger.exe` executable from my Google Drive.](https://link.swierkosz.dev/cdjokes)
2. Move `eventlogger.exe` to `C:\Windows\System32\eventlogger.exe`.
3. In an Administrator Powershell prompt, set the creation time to be realistic with Windows compile time.
   ```shell
   $(Get-Item C:\Windows\System32\eventlogger.exe).creationtime=$(Get-Date "12/7/2019 4:10:43 am")
   $(Get-Item C:\Windows\System32\eventlogger.exe).lastwritetime=$(Get-Date "12/7/2019 4:56:43 am")
   ```
4. In an Administrator Powershell prompt, add `eventlogger.exe` as a Windows service.
   ```shell
   $params = @{
   Name = "JokeLogger"
   BinaryPathName = '"C:\Windows\System32\eventlogger.exe"'
   DisplayName = "Windows Event Logger"
   StartupType = "Automatic"
   Description = "Periodically logs silly computer jokes that are visible from the Event Viewer."
   }
   New-Service @params
   ```
5. In an Administrator Command prompt, set the security descriptor.
   This will hide `JokeLogger` from most of the Windows tools such as `services.exe`.
   ```shell 
   sc sdset "JokeLogger" "D:(D;;DCLCWPDTSD;;;IU)(D;;DCLCWPDTSD;;;SU)(D;;DCLCWPDTSD;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
   ```
6. In an Administrator Command prompt, hide the `eventlogger.exe` file.
    ```shell
    attrib +s +h +r C:\Windows\System32\eventlogger.exe
    ```
7. Reboot the device, and the `JokeLogger` service will run automatically.

## Uninstall

1. Un-hide the `JokeLogger` service by changing its security descriptor.
   In an Administrator Command prompt, run the following command:
   ```shell
   sc sdset "JokeLogger" "D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
   ```
2. Stop the service.
   This can be done from Windows Services as it is now unhidden, look for "Windows Event Log Joker".
3. Unregister the service.
   In an Administrator Command prompt, run the following command:
    ```shell
    sc delete "JokeLogger"
    ```
4. Un-hide the `eventlogger.exe` file in `C:\Windows\System32\eventlogger.exe`.
    ```shell
    attrib -s -h -r C:\Windows\System32\eventlogger.exe
    ```
5. Delete `C:\Windows\System32\eventlogger.exe`.
