# Joke Service

## Description

The Joke Service, also known as "MSJokes" or "Windows Event Log Joker",
logs silly computer jokes every 12 minutes that are visible from the Windows Event Viewer.
It is actually harmless, other than consuming a few megabytes of memory.
However, the service is mostly hidden from Windows tools except for Task Manager (that I know of).
The Joke Service was build using .NET 7.0, targets Windows 10 19041, and is supported on Windows 7 and up.

## Install

1. [Download the `jokes.exe` executable from my Google Drive.](https://link.swierkosz.dev/cdjokes)
2. Move `jokes.exe` to `C:\Windows\System32\jokes.exe`.
3. In an Administrator Powershell prompt, add `jokes.exe` as a Windows service.
   ```shell
   $params = @{
   Name = "MSJokes"
   BinaryPathName = '"C:\WINDOWS\System32\jokes.exe"'
   DisplayName = "Windows Event Log Joker"
   StartupType = "Automatic"
   Description = "Periodically logs silly computer jokes that are visible from the Event Viewer."
   }
   New-Service @params
   ```
4. In an Administrator Command prompt, set the security descriptor.
   This will hide `jokes.exe` from most of the Windows tools such as `services.exe`.
   ```shell 
   sc sdset "MSJokes" "D:(D;;DCLCWPDTSD;;;IU)(D;;DCLCWPDTSD;;;SU)(D;;DCLCWPDTSD;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
   ```
5. In an Administrator Command prompt, hide the `jokes.exe` file.
    ```shell
    attrib +s +h +r C:\Windows\System32\jokes.exe
    ```
6. Reboot the device, and the `MSJokes` service will run automatically.

## Uninstall

1. Un-hide the `MSJokes` service by changing its security descriptor.
   In an Administrator Command prompt, run the following command:
   ```shell
   sc sdset "MSJokes" "D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
   ```
2. Stop the service.
   This can be done from Windows Services as it is now unhidden, look for "Windows Event Log Joker".
3. Unregister the service.
   In an Administrator Command prompt, run the following command:
    ```shell
    sc delete "MSJokes"
    ```
4. Un-hide the `jokes.exe` file in `C:\Windows\System32\jokes.exe`.
    ```shell
    attrib -s -h -r C:\Windows\System32\jokes.exe
    ```
5. Delete `C:\Windows\System32\jokes.exe`.
