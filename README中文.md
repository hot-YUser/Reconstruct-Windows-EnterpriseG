# Windows 10/11 企業版 G 重建方法
注意：本文章從https://www.xrgzs.top/posts/convert-entg 翻譯改寫搬運，目的是為了讓更多人看到。
本篇省略了很多細節，請到原作者出處參考。
## 什麼是企業版 G
Windows 企業版有許多版本，像是 N 版（歐盟版），S 版（LTSB/LTSC），還有 G 版

這個企業版 G 是什麼呢？其實就是指「政府版」Government

其中「微軟神州網信政府版」(也叫中國政府版)，就是基於此版訂製的

參考：Windows 10 神州網信政府版官網：https://document.cmgos.com/release_notes/release_notes
```
Windows 10 神州網信政府版（以下簡稱 CMGE）V2022-L 是在 Windows 10 21H2 的基礎上，根據中國有關法律、法規、標準的規定，針對中國專業領域的需求，定制開發的一個操作系統版本。
1） 產品激活
CMGE 產品的啟動是基於 Windows 10 開發，啟動伺服器位於中國境內的神州網信資料中心。
2） 系統補丁和升級
CMGE 產品的系統修補程式和升級伺服器位於中國境內且由神州網信技術有限公司提供。
3） 數位證書
CMGE 內建中國政府指定數位憑證機關的根證書。
4） GB/T 30278-2013
CMGE 的系統預設安全設定符合 GB/T 30278-2013《資訊安全技術政府電腦終端核心設定規範》的要求。
5） 系統精簡
移除 / 停用了 Windows 自帶的辦公室類別、個人助理類別、娛樂生活類應用程式及基於雲端的服務：
a. 移除了下列應用程式 / 服務：
天氣、取得協助、使用技巧、3D 檢視器、Office Hub、Microsoft Solitaire Collection、混合實境入口網站、OneNote、人脈、Store Purchase App、錢包、鬧鐘和時鐘、日曆、郵件、回饋中心、地圖、Microsoft Store、Xbox 、Xbox Game Bar、Groove 音樂、電影與電視、Edge、Quick Assist 等。
b. 停用了下列應用程式 / 服務：
Cortana、Windows Defender、Administrative Shares、Fax、Windows Media Player、Windows Hello、遠端桌面、Application Management、Connected User Experiences and Telemetry、Microsoft Account Sign-in Assistant、Netlogon、Performance Logs & Alerts、Problem Reports and Solutions Control Panel Support 、Remote Desktop Services UserMode Port Redirector、Windows Error Reporting Service、Windows 安全性中心服務、Windows 許可證管理器服務等。
```
## 正確的轉制方式
若直接使用`YYVX9-NTFWV-6MDM3-9PT4T-4M68B`或`44RPN-FTY23-9VTTB-MP9BX-T84FV`金鑰進行版本轉換，會導致系統無法更新以及各種奇奇怪怪的問題(例如無法使用 DISM 正確讀取系統版本`0x800f0805`)

上面的這種情況是因為系統內缺乏相關的元件包，缺乏版本識別元件包，即與 EnterpriseG 相關的元件包

因此，我們需要在更改密鑰前就把組件包打好，最好是在系統鏡像被安裝前就把組件包打好，否則會非常麻煩
### 原理介紹
本教學參考以下三篇文章：

https://www.xrgzs.top/posts/convert-entg

https://bbs.pcbeta.com/viewthread-1786621-1-1.html

https://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1905800&page=1&authorid=2495899

對於 Windows 10/11 而言，需要以下幾個元件包

```
Microsoft-Windows-Branding-EnterpriseG-Package 
Microsoft-Windows-Editions-EnterpriseG-Package 
Microsoft-Windows-Security-SPP-Component-SKU-EnterpriseG-Package 
Microsoft-Windows-Editions-EnterpriseG-Package-amd64H2 zh-CN （24H2開始必須）
Microsoft-Windows-EnterpriseGEdition 
Microsoft-Windows-EnterpriseGEdition-Wrapper
```
另外，安裝元件包的取得也是非常複雜，這牽涉到 Windows 10 的系統更新模式－UUP，這裡就省略不做解釋，有興趣的可以去看原作者文章
### 元件包去哪裡找
所有的組件都是可以在 UUP 中找到的，畢竟EnterpriseG本身也是支援升級的

在名為`Microsoft-Windows-EditionSpecific-EnterpriseG-Package`的ESD文件中，能夠找到前三個元件包
```
Microsoft-Windows-Branding-EnterpriseG-Package 
Microsoft-Windows-Editions-EnterpriseG-Package 
Microsoft-Windows-Security-SPP-Component-SKU-EnterpriseG-Package
```
在`Microsoft-Windows-Client-LanguagePack-Package-amd64-zh-CN`的ESD文件中，則能找到第四個元件包
```
Microsoft-Windows-Editions-EnterpriseG-Package-amd64-zh-CN
```
以上這兩個 ESD 檔案均可在 UUP dump 網站對應 Build 的檔案清單中找到，所有組建都行，包括Insider版本

那接下來來剩下兩個元件包就比較麻煩，但也是最重要的，用於版本識別的套件
```
Microsoft-Windows-EnterpriseGEdition 
Microsoft-Windows-EnterpriseGEdition-Wrapper
```
如果沒有這兩個包的話，DISM一樣會無法識別版本而報錯`0x800f0805`

這兩個包能在任何「累積更新包」中找到，所以沒有發布過累積更新的Insider版本，就無法獲得這兩個包
## 實際操作
### 事前說明 
因為EnterpriseG只有中英兩種語言，以下操作會以簡體中文為主要示範語言。

目前本文撰寫時，最新的Windows正式版是24H2，所以會以24H2來示範。

再開始之前，建議大家可以準備好搜尋神器Everything，對稍後的操作會很有幫助。
不會使用的話可以參考這個視頻`https://www.bilibili.com/video/BV1Hw411W7vK/`

接著當然是得準備iso的映象，UUP Dump這裡就不多介紹了。需要注意的是，請不要勾選ESD壓縮的選項，且語言要為簡中或英文。

稍後在操作時，需要有一個單純的工作資料夾，我會在`E:\`中操作，沒有多硬碟分區的可以新建一個資料夾`"C:\EntG"`，之後就在裡面操作
### 準備文件包
#### 下載組建包
接著繼續在UUP Dump中尋找下載我們所需的檔案:`Microsoft-Windows-EditionSpecific-EnterpriseG-Package.ESD`、`Microsoft-Windows-Client-LanguagePack-Package-amd64-zh-CN.esd`以及任意一個`該組建的累積更新包`(叫做`Windows1x.0-KB50xxxxx-x64.msu`，本範例示範是`Windows11.0-KB5040529-x64.msu`)。

#### 解壓組建包
開始前先在工作資料夾`E:\`或`"C:\EntG"`分別新增`KB`、`entg1`、`entg2`、`entg3`四個新資料夾 (名稱自訂，這裡只是示範)

首先將剛剛下載的.msu累積更新包解壓至剛剛創建的`KB`資料夾中

裡面會有個`Windows1x.0-KB50xxxxx-x64.wim`，將其解壓後，使用Everything搜尋關鍵字`Microsoft-Windows-EnterpriseGEdition`

發現其內含有兩個.mum 檔案及兩個對應的.cat 檔案共四個文件
```
Microsoft-Windows-EnterpriseGEdition-Wrapper~31bf3856ad364e35~amd64~~10.0.26100.1301.cat
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.26100.1301.cat
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.26100.1301.mum
Microsoft-Windows-EnterpriseGEdition-Wrapper~31bf3856ad364e35~amd64~~10.0.26100.1301.mum
```
將這四個檔案放至`entg3`資料夾中存放，以免之後找不到

(接著可將前面解壓累積更新包的資料夾刪除，以維持工作環境單純)

需要注意的是，我們這步得記下這個累積更新包的版本號，如我這一步就是`26100.1301`，後面要用到

接著將剛剛下載的`microsoft-windows-editionspecific-enterpriseg-package.esd`解壓縮`entg1`資料夾下

`Microsoft-Windows-Client-LanguagePack-Package_zh-cn.esd`解壓縮到`entg2`

至此所有需要的元件包都以準備完畢。
### 正式開始
#### 掛載鏡像
##### 第一步
當然是要將ISO鏡像解壓了，將ISO文件中的`\sources\install.wim`提取出來，放在工作資料夾底下，這裡我放在`E:\`

**要注意_不能_直接掛載ISO在虛擬光碟機裡面操作**。

提取完成後，需要掛載系統鏡像wim文件，如果你的系統鏡像是esd格式的，得轉換成 wim 之後再操作

##### 第二步
創建掛載路徑

這裡在工作資料夾底下創建一個`MountTemp`資料夾(名稱自訂，這裡只是示範)，需要注意，我們不能直接處理Online的系統，必須是掛載的系統

之後命令中的`"MountTemp"`，都要自行替換成自己的路徑

##### 第三步
執行以下命令掛載鏡像
```
DISM.exe /Mount-Image / ImageFile:"install.wim" /Index: 1 /MountDir:"MountTemp"
```
這裡我的實際位置是
```
DISM.exe /Mount-Image / ImageFile:"E:\install.wim" /Index: 1 /MountDir:"E:\MountTemp"
```
當然，此步驟也可以使用DISM++來操作。
##### 第四步
安裝EntG元件，然後執行以下命令安裝元件：
```
FOR %a IN (entg1\*.mum) DO DISM.exe /Image:"MountTemp" /Add-Package /PackagePath:"%a"
```

這命令的意思是，先偵測entg1底下的所有.mum文件，然後用DISM處理安裝到掛載的系統中

當然，DISM++ 也有對應的功能

這樣全部安裝，會比只安裝必要的幾個元件來的更完整中途有個的`update.mum`文件會提示錯誤，不用管它
##### 第五步
接著重複與第四步一樣的步驟，將`entg2`安裝到掛載的系統中
```
FOR %a IN (entg2\*.mum) DO DISM.exe /Image:"MountTemp" /Add-Package /PackagePath:"%a"
```
##### 第六步
接著我們要處理`entg3`底下的4個文件

因為這4個檔案無法直接透過DISM直接進行安裝會報錯，因此我們需要進行手動安裝操作，用指令將這幾個檔案相關檔案放置到`MountTemp\Windows\servicing\Packages`下方
```
bf3856ad364e35~amd64~~ 10 . 0 .*.*" "Mount_Dir\Windows\servicing\Packages"
```
但一樣，這指令無法直接運行成功，需要透過`NSudo`或`NanaRun`等提權工具，提權後再進行操作

若不會使用上面那兩個工具，當然我們一樣也可以透過 `Dism++`，左側【常用工具】=>【工具箱】=> 【檔案瀏覽器】，這個檔案瀏覽器擁有最高權限，這樣以後就可以直接手動複製（Ctrl + C、Ctrl + V）那四個檔案到`MountTemp\Windows\servicing\Packages`（請注意，所以命令中的`"MountTemp"`，都要自行替換成自己的路徑）

##### 第七步
接下來我們要掛載註冊表，手動還原這個元件包的安裝信息

執行以下命令，掛載`MountTemp\Windows\System32\config\SOFTWARE`註冊表（這個登錄機碼對應所掛載系統的HKLM\SOFTWARE）到`HKLM\EntGSOFTWARE`(名稱自訂，這裡只是示範)：
```REG.exe LOAD HKLM\EntGSOFTWARE "MountTemp\Windows\System32\config\SOFTWARE"```(HKLM\EntGSOFTWARE記得替換成自己的名稱)
當然，也可以手動用登錄編輯程式（regedit）操作

###### 匯入註冊表
**_關鍵步驟來了！ ！ ！_以下的命令不能漏！ ！ ！**

還記得我們在解壓組建包的時候，要把組建包的版本記起來嗎？

這裡要使用的版本號，是組建包的版本號，而不是ISO的版本號！！！

由於上述指令設定的版本號碼我自己也常搞錯，特給以下指令無腦自動讀取：
```
for /f "tokens= 6 , 7 delims=~." %a in (' dir /b edition') do  set EntGEditionVersion= %a.% b
```
接著，我們需要讀取註冊表部分鍵值（% InstallTimeHigh%、% InstallTimeLow%、% InstallUser%）以進行後續修改註冊表
```
::OfflineReg讀取(原始方式)
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallTimeHigh') DO SET InstallTimeHigh=%i
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallTimeLow') DO SET InstallTimeLow=%i
FOR /F "tokens=2* delims=:" %%i IN ('OfflineReg.exe "Mount_Dir\Windows\System32\config\SOFTWARE" "Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGPackagesVersion%" getvalue InstallUser') DO SET InstallUser=%i
CLS
echo %InstallTimeHigh%
echo %InstallTimeLow%
echo %InstallUser%
echo %InstallLocation%
```
```
::粗略讀取
FOR /F "tokens=*" %i IN ('REG QUERY "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages"^|find /i "Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~"') DO (
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
::精準讀取(AMD64)
FOR /F "tokens=*" %i IN ('REG QUERY "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages"^|find /i "Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%"') DO (
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
上面這三種讀取方法，應該會有一種能夠成功讀取到，建議使用精準讀取，如精準讀取都不到再用粗略讀取。

注意：以上的所有 CMD 指令請直接複製貼上，如需在腳本中使用請手動補上 FOR 指令的兩個 %%

如果有幾個項目（如 InstallTimeHigh、InstallTimeLow），實在讀不出來，或後面無法匯入到註冊表，可以隨意設定
```
::需要設定為10進制，如果是16進制需要寫成0x多少的形式
set InstallTimeHigh=31066064
set InstallTimeHigh=417343264
```
修改登錄機碼 Microsoft-Windows-EnterpriseGEdition 相關資訊以讓系統版本可以正確讀取
```
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "CurrentState" /t REG_DWORD /d 112
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallClient" /t REG_SZ /d "DISM Package Manager Provider"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallLocation" /t REG_SZ /d "\\?%WorkDisk%\%LCUName%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallName" /t REG_SZ /d "Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%.mum"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallTimeHigh" /t REG_DWORD /d "%InstallTimeHigh%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallTimeLow" /t REG_DWORD /d "%InstallTimeLow%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "InstallUser" /t REG_SZ /d "%InstallUser%"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "SelfUpdate" /t REG_DWORD /d 0
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /v "Visibility" /t REG_DWORD /d 1
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%\Owners"
REG.exe ADD "HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%\Owners" /v "Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~amd64~~10.0.%EntGEditionVersion%" /t REG_DWORD /d 131184
```
###### 以下步驟一般不需執行
如果無法正確寫入註冊表，可以手動「抄寫」 註冊表的相關鍵值，相關註冊表：
```
先用註冊表編輯器訪問：
HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages
再找到：
Microsoft-Windows-Editions-EnterpriseG-Package~31bf3856ad364e35~系統架構~~10.0.累積更新包的版本號
分别抄下InstallTimeHigh、InstallTimeLow、InstallUser、InstallLocation
HKLM\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages下，新建項：
Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~系统架构~~10.0.累积更新包的版本号
分别創建InstallTimeHigh、InstallTimeLow、InstallUser、InstallLocation等（此步建議使用命令修改）
```
```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~系统架构~~10.0.累积更新包的版本号]
"InstallClient"="DISM Package Manager Provider"
"InstallName"="Microsoft-Windows-EnterpriseGEdition~31bf3856ad364e35~系统架構~~10.0.累積更新包的版本號"
"InstallLocation"="【這個需要自己抄】"
"CurrentState"=dword:00000070
"SelfUpdate"=dword:00000000
"Visibility"=dword:00000001
"InstallTimeHigh"=dword:【這個需要自己抄，8位16進制】
"InstallTimeLow"=dword:【這個需要自己抄，8位16進制】
"InstallUser"="【這個需要自己抄，S-1-5-21-後面一大串】"

[HKEY_LOCAL_MACHINE\EntGSOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages\Microsoft-Windows-EnterpriseGNEdition~31bf3856ad364e35~系统架構~~10.0.累積更新包的版本號\Owners]
"Microsoft-Windows-EnterpriseGNEdition~31bf3856ad364e35~系统架構~~10.0.累積更新包的版本號"=dword:00020070
```
###### 卸載註冊表 (HKLM\EntGSOFTWARE需換成自己的名稱)
```
REG.exe UNLOAD HKLM\EntGSOFTWARE
```
###### 安裝產品金鑰
```
DISM.exe /Image:"MountTemp" /Set-ProductKey:FV469-WGNG4-YQP66-2B2HY-KD8YX
```
###### 驗證系統版本
目前為止此方式來轉換版本，這一步驟驗證都是成功的
```
DISM.exe /Image:"MountTemp" /Get-CurrentEdition
```
###### 複製許可協議
```
MKDIR "MountTemp\Windows\System32\Licenses\neutral\_Default\EnterpriseG"
COPY /Y "MountTemp\Windows\System32\Licenses\neutral\Volume\Professional\*.*" "MountTemp\Windows\System32\Licenses\neutral\_Default\EnterpriseG"
```
###### 卸載映像
```
DISM.exe /Unmount-Wim /MountDir:"Mount_Dir" /commit
```
到此，這個系統鏡像就成功改為企業版 G 了！

## 後記
請參考原作者文章：)

# 免責聲明
本 Windows 系統及軟體版權屬各自產權人所有，僅用於個人封裝技術研究交流使用，不得用於商業用途，且本系統製作者不承擔任何技術及版權問題，請於試用後 24 小時內刪除。
如果您對本系統有任何意見和建議，歡迎回饋，請購買正版 Windows 作業系統軟體！
