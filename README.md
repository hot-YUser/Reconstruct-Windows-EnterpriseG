# Windows 10/11 Enterprise G Rebuild Method
[繁體中文](https://github.com/hot-YUser/Reconstruct-Windows-EnterpriseG/blob/main/README%E4%B8%AD%E6%96%87.md) [简体中文](https://www.xrgzs.top/posts/convert-entg)
## What is Enterprise Edition G
There are many versions of Windows Enterprise, such as N version (EU version), S version (LTSB/LTSC), and G version

What is this Enterprise Edition G? In fact, it refers to the "Government Version" Government

Among them, the "Microsoft China Internet Information Government Edition" (also called the Chinese Government Edition) is customized based on this edition.

Reference: Windows 10 China Netcom Government Edition official website: https://document.cmgos.com/release_notes/release_notes
```
Windows 10 China Netcom Government Edition (hereinafter referred to as CMGE) V2022-L is an operating system version customized and developed based on Windows 10 21H2, in accordance with relevant Chinese laws, regulations, and standards, and in response to the needs of China's professional fields.
1) Product activation
The startup of CMGE products is developed based on Windows 10, and the startup server is located at the China Netcom Data Center in China.
2) System patches and upgrades
System patches and upgrade servers for CMGE products are located in China and provided by China Netcom Technology Co., Ltd.
3) Digital certificate
CMGE has a built-in root certificate of the digital certificate authority designated by the Chinese government.
4) GB/T 30278-2013
CMGE's system default security settings comply with the requirements of GB/T 30278-2013 "Information Security Technology Government Computer Terminal Core Settings Specification".
5) System simplification
Removed/disabled the office categories, personal assistant categories, entertainment and lifestyle applications and cloud-based services that come with Windows:
a. Removed the following applications/services:
Weather, Get Help, Tips, 3D Viewer, Office Hub, Microsoft Solitaire Collection, Mixed Reality Portal, OneNote, People, Store Purchase App, Wallet, Alarms & Clocks, Calendar, Mail, Feedback Center, Maps, Microsoft Store , Xbox, Xbox Game Bar, Groove Music, Movies & TV, Edge, Quick Assist, and more.
b. The following applications/services have been deactivated:
Cortana, Windows Defender, Administrative Shares, Fax, Windows Media Player, Windows Hello, Remote Desktop, Application Management, Connected User Experiences and Telemetry, Microsoft Account Sign-in Assistant, Netlogon, Performance Logs & Alerts, Problem Reports and Solutions Control Panel Support, Remote Desktop Services UserMode Port Redirector, Windows Error Reporting Service, Windows Security Center Service, Windows License Manager Service, etc.
```
## Correct conversion method
If you directly use the `YYVX9-NTFWV-6MDM3-9PT4T-4M68B` or `44RPN-FTY23-9VTTB-MP9BX-T84FV` key for version conversion, it will cause the system to fail to update and various strange problems (such as the inability to use DISM Correctly read system version `0x800f0805`)

The above situation is due to the lack of relevant component packages in the system and the lack of version identification component packages, that is, the component packages related to EnterpriseG.

Therefore, we need to pack the component package before changing the key. It is best to pack the component package before the system image is installed, otherwise it will be very troublesome.
### Principle introduction
This tutorial refers to the following three articles:

https://www.xrgzs.top/posts/convert-entg

https://bbs.pcbeta.com/viewthread-1786621-1-1.html

https://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1905800&page=1&authorid=2495899

For Windows 10/11, the following component packages are required

```
Microsoft-Windows-Branding-EnterpriseG-Package
Microsoft-Windows-Editions-EnterpriseG-Package
Microsoft-Windows-Security-SPP-Component-SKU-EnterpriseG-Package
Microsoft-Windows-Editions-EnterpriseG-Package-amd64H2 zh-CN (required starting from 24H2)
Microsoft-Windows-EnterpriseGEdition
Microsoft-Windows-EnterpriseGEdition-Wrapper
```
In addition, obtaining the installation component package is also very complicated, which involves the system update mode of Windows 10 - UUP. I will omit the explanation here. If you are interested, you can read the [original author's article](https://www.xrgzs.top/posts/convert-entg).
### Where to find component packages?
All components can be found in UUP. After all, EnterpriseG itself also supports upgrades.

The first three component packages can be found in the ESD file named `Microsoft-Windows-EditionSpecific-EnterpriseG-Package`
```
Microsoft-Windows-Branding-EnterpriseG-Package
Microsoft-Windows-Editions-EnterpriseG-Package
Microsoft-Windows-Security-SPP-Component-SKU-EnterpriseG-Package
```
In the ESD file of `Microsoft-Windows-Client-LanguagePack-Package-amd64-zh-CN`, you can find the fourth component package
```
Microsoft-Windows-Editions-EnterpriseG-Package-amd64-zh-CN
```
The above two ESD files can be found in the file list corresponding to Build on the UUP dump website. All builds are acceptable, including Insider versions.

Then the remaining two component packages are more troublesome, but they are also the most important ones, the package for version identification.
```
Microsoft-Windows-EnterpriseGEdition
Microsoft-Windows-EnterpriseGEdition-Wrapper
```
Without these two packages, DISM will still be unable to identify the version and report an error `0x800f0805`

These two packages can be found in any "cumulative update package", so if you have not released an Insider version of the cumulative update, you cannot get these two packages.
## Practical operation
### Precautions
Because EnterpriseG only has two languages: Chinese and English, the following operations will use Simplified Chinese as the main demonstration language.

At the time of writing this article, the latest official version of Windows is 24H2, so 24H2 will be used for demonstration.

Before starting, it is recommended that you prepare to search for the artifact Everything, which will be very helpful for later operations.
If you don’t know how to use it, you can refer to this video `https://www.bilibili.com/video/BV1Hw411W7vK/`

Next, of course, you have to prepare the iso image. UUP Dump will not be introduced here. It should be noted that please do not check the ESD compression option, and the language must be Simplified Chinese or English.

When operating later, you need to have a simple working folder. I will operate in `E:\`. If you don't have multiple hard disk partitions, you can create a new folder `"C:\EntG"`, and then it will be in it. operate
### Prepare file package
#### Download the build package
Then continue to search for and download the files we need in UUP Dump: `Microsoft-Windows-EditionSpecific-EnterpriseG-Package.ESD`, `Microsoft-Windows-Client-LanguagePack-Package-amd64-zh-CN.esd` and any one `The cumulative update package for this component` (called `Windows1x.0-KB50xxxxx-x64.msu`, this example is `Windows11.0-KB5040529-x64.msu`).

#### Unzip the build package
Before starting, add four new folders (names customized, here Just a demonstration)

First, unzip the .msu cumulative update package you just downloaded into the `KB` folder you just created.

There will be a `Windows1x.0-KB50xxxxx-x64.wim` inside. After unzipping it, use Everything to search for the keyword `Microsoft-Windows-EnterpriseGEdition`

It was found that it contains two .mum files and two corresponding .cat files, a total of four files.
```
Microsoft-Windows-EnterpriseGEdition-Wrapper~31bf3856ad364e35~amd64~~10.0.26100.1301.cat
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.26100.1301.cat
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.26100.1301.mum
Microsoft-Windows-EnterpriseGEdition-Wrapper~31bf3856ad364e35~amd64~~10.0.26100.1301.mum
```
Place these four files in the `entg3` folder to prevent them from being found later.

(Then you can delete the folder where you extracted the cumulative update package earlier to keep the working environment simple)

It should be noted that we need to remember the version number of this cumulative update package in this step. For example, in my step it is `26100.1301`, which will be used later.

Then unzip the `microsoft-windows-editionspecific-enterpriseg-package.esd` just downloaded to the `entg1` folder

Unzip `Microsoft-Windows-Client-LanguagePack-Package_zh-cn.esd` to `entg2`

At this point all required component packages are ready.
### Officially begins
#### Mount image
##### First step
Of course, you need to unzip the ISO image, extract the `\sources\install.wim` in the ISO file, and put it under the work folder. Here I put it in `E:\`

**Please note that you cannot directly mount the ISO and operate it in the virtual CD-ROM drive**.

After the extraction is completed, you need to mount the system image wim file. If your system image is in esd format, you must convert it to wim before operating.

##### The second step
Create mount path

Here, create a `MountTemp` folder under the work folder (the name is customized, this is just a demonstration). It should be noted that we cannot directly deal with the Online system, it must be a mounted system.

After that, the `"MountTemp"` in the command must be replaced with your own path.

##### The third step
Execute the following command to mount the image
```
DISM.exe /Mount-Image / ImageFile:"install.wim" /Index: 1 /MountDir:"MountTemp"
```
My actual location here is
```
DISM.exe /Mount-Image / ImageFile:"E:\install.wim" /Index: 1 /MountDir:"E:\MountTemp"
```
Of course, this step can also use DISM++ to operate.
##### Step 4
Install the EntG component, and then execute the following command to install the component:
```
FOR %a IN (entg1\*.mum) DO DISM.exe /Image:"MountTemp" /Add-Package /PackagePath:"%a"
```

The meaning of this command is to first detect all .mum files under entg1, and then use DISM to install them into the mounted system.

Of course, DISM++ also has corresponding functions

Installing everything in this way will be more complete than installing only a few necessary components. An `update.mum` file will prompt an error in the middle, so don't worry about it.
##### Step 5
Then repeat the same steps as step 4 to install `entg2` into the mounted system.
```
FOR %a IN (entg2\*.mum) DO DISM.exe /Image:"MountTemp" /Add-Package /PackagePath:"%a"
```
##### Step 6
Then we have to process the 4 files under `entg3`

Because these 4 files cannot be installed directly through DISM and an error will be reported, we need to perform a manual installation operation and use the command to place the relevant files of these files under `MountTemp\Windows\servicing\Packages`
```
bf3856ad364e35~amd64~~ 10 . 0 .*.*" "Mount_Dir\Windows\servicing\Packages"
```
But again, this command cannot be run successfully directly. You need to use privilege escalation tools such as `NSudo` or `NanaRun` to escalate the privileges before proceeding.

If you don’t know how to use the above two tools, of course we can also use `Dism++`, [Common Tools] on the left => [Toolbox] => [File Browser], this file browser has the highest permissions, so In the future, you can directly manually copy (Ctrl + C, Ctrl + V) the four files to `MountTemp\Windows\servicing\Packages` (please note that the `"MountTemp"` in the command must be replaced by your own path)

##### Step 7
Next we need to mount the registry and manually restore the installation information of this component package

Execute the following command to mount the `MountTemp\Windows\System32\config\SOFTWARE` registry (this login code corresponds to the HKLM\SOFTWARE of the mounted system) to `HKLM\EntGSOFTWARE` (the name is customized, this is just a demonstration):
```REG.exe LOAD HKLM\EntGSOFTWARE "MountTemp\Windows\System32\config\SOFTWARE"```(remember to replace HKLM\EntGSOFTWARE with your own name)
Of course, you can also manually use the login editor program (regedit) to operate

###### Import the registry
**_Here comes the key step! ! ! _The following commands cannot be missed! ! ! **

Do you still remember that when we decompressed the build package, we had to remember the version of the build package?

The version number to be used here is the version number of the build package, not the ISO version number! ! !

Since I often get the version number set by the above command wrong, I give the following command to automatically read it without any brain:
```
for /f "tokens= 6 , 7 delims=~." %a in (' dir /b edition') do set EntGEditionVersion= %a.% b
```
Next, we need to read some key values ​​​​(% InstallTimeHigh%, % InstallTimeLow%, % InstallUser%) in the registry to make subsequent modifications to the registry.
```
::OfflineReg reading (original way)
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions -EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallTimeHigh') DO SET InstallTimeHigh=%i
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions -EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallTimeLow') DO SET InstallTimeLow=%i
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions -EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallUser') DO SET InstallUser=%i
CLS
echo %InstallTimeHigh%
echo %InstallTimeLow%
echo %InstallUser%
echo %InstallLocation%
```
```
::Rough read
FOR /F "tokens=*" %i IN ('REG QUERY "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages"^|find /i "Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~ "') DO (
 FOR /F "tokens=3* skip=2" %j IN ('REG QUERY "%i" /v InstallTimeHigh') DO SET "InstallTimeHigh=%j"
 FOR /F "tokens=3* skip=2" %k IN ('REG QUERY "%i" /v InstallTimeLow') DO SET "InstallTimeLow=%k"
 FOR /F "tokens=3* skip=2" %l IN ('REG QUERY "%i" /v InstallUser') DO SET "InstallUser=%l"
 FOR /F "tokens=3* skip=2" %m IN ('REG QUERY "%i" /v InstallLocation') DO SET "InstallLocation=%m"
)
CLS
echo %InstallTimeHigh%
echo %InstallTimeLow%
echo %InstallUser%
echo %InstallLocation%
```
```
::Accurate reading (AMD64)
FOR /F "tokens=*" %i IN ('REG QUERY "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages"^|find /i "Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~ amd64~~10.0.%EntGEditionVersion%"') DO (
 FOR /F "tokens=3* skip=2" %j IN ('REG QUERY "%i" /v InstallTimeHigh') DO SET "InstallTimeHigh=%j"
 FOR /F "tokens=3* skip=2" %k IN ('REG QUERY "%i" /v InstallTimeLow') DO SET "InstallTimeLow=%k"
 FOR /F "tokens=3* skip=2" %l IN ('REG QUERY "%i" /v InstallUser') DO SET "InstallUser=%l"
 FOR /F "tokens=3* skip=2" %m IN ('REG QUERY "%i" /v InstallLocation') DO SET "InstallLocation=%m"
)
CLS
echo %InstallTimeHigh%
echo %InstallTimeLow%
echo %InstallUser%
echo %InstallLocation%
```
One of the above three reading methods should be able to successfully read. It is recommended to use precise reading. If precise reading cannot be achieved, use rough reading.

Note: Please copy and paste all the above CMD instructions directly. If you need to use them in scripts, please manually add the two %% of the FOR instruction.

If there are several items (such as InstallTimeHigh, InstallTimeLow) that cannot be read out, or cannot be imported into the registry later, you can set them as you like.
```
::It needs to be set to decimal. If it is hexadecimal, it needs to be written in the form of 0x.
set InstallTimeHigh=31066064
set InstallTimeHigh=417343264
```
Modify the login code Microsoft-Windows-EnterpriseGEdition related information so that the system version can be read correctly
```
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "CurrentState" /t REG_DWORD /d 112
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallClient" /t REG_SZ /d "DISM Package Manager Provider"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallLocation" /t REG_SZ /d "\ \?%WorkDisk%\%LCUName%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallName" /t REG_SZ /d "Microsoft -Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%.mum"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallTimeHigh" /t REG_DWORD /d "% InstallTimeHigh%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallTimeLow" /t REG_DWORD /d "% InstallTimeLow%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallUser" /t REG_SZ /d "% InstallUser%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "SelfUpdate" /t REG_DWORD /d 0
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "Visibility" /t REG_DWORD /d 1
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%\Owners"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%\Owners" /v "Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35 ~amd64~~10.0.%EntGEditionVersion%" /t REG_DWORD /d 131184
```
###### The following steps generally do not need to be performed
If the registry cannot be written correctly, you can manually "copy" the relevant key values ​​of the registry. Related registry:
```
First use the registry editor to access:
HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages
Found again:
Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~System Architecture~~10.0. Cumulative update package version number
Copy down InstallTimeHigh, InstallTimeLow, InstallUser, and InstallLocation respectively.
Under HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages, create a new item:
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~System Architecture~~10.0. Cumulative update package version number
Create InstallTimeHigh, InstallTimeLow, InstallUser, InstallLocation, etc. respectively (it is recommended to use commands to modify this step)
```
```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~System architecture~~10.0. Cumulative update package version number]
"InstallClient"="DISM Package Manager Provider"
"InstallName"="Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~System architecture~~10.0. Cumulative update package version number"
"InstallLocation"="[You need to copy this yourself]"
"CurrentState"=dword:00000070
"SelfUpdate"=dword:00000000
"Visibility"=dword:00000001
"InstallTimeHigh"=dword: [You need to copy this yourself, 8-digit hexadecimal]
"InstallTimeLow"=dword: [You need to copy this yourself, 8-digit hexadecimal]
"InstallUser"="[You need to copy this yourself, S-1-5-21-followed by a long string]"

[HKEY_LOCAL_MACHINE\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGNEdition~31bf3856ad364e35~System architecture~~10.0.Cumulative update package version number\Owners]
"Microsoft-Windows-EnterpriseGNEdition~31bf3856ad364e35~System architecture~~10.0. Cumulative update package version number"=dword:00020070
```
###### Uninstall the registry (HKLM\EntGSOFTWARE needs to be replaced with your own name)
```
REG.exe UNLOAD HKLM\EntGSOFTWARE
```
###### Install product key
```
DISM.exe /Image:"MountTemp" /Set-ProductKey:FV469-WGNG4-YQP66-2B2HY-KD8YX
```
###### Verify system version
So far, this method has been used to convert versions, and the verification of this step has been successful.
```
DISM.exe /Image:"MountTemp" /Get-CurrentEdition
```
###### Copy License Agreement
```
MKDIR "MountTemp\Windows\System32\Licenses\neutral\_Default\EnterpriseG"
COPY /Y "MountTemp\Windows\System32\Licenses\neutral\Volume\Professional\*.*" "MountTemp\Windows\System32\Licenses\neutral\_Default\EnterpriseG"
```
###### Uninstall image
```
DISM.exe /Unmount-Wim /MountDir:"Mount_Dir" /commit
```
At this point, the system image has been successfully changed to Enterprise Edition G!

## Postscript
Please refer to the [original author's article](https://www.xrgzs.top/posts/convert-entg) :)

# Disclaimer
The copyrights of this Windows system and software belong to their respective owners. They are only used for personal packaging technology research and exchange, and may not be used for commercial purposes. The manufacturer of this system is not responsible for any technical and copyright issues. Please delete it within 24 hours after trial.
If you have any comments and suggestions about this system, feedback is welcome, and please purchase genuine Windows operating system software!
