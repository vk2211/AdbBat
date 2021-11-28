# AdbBat

# adb_clear
@echo off
%~dp0\list_devices.bat %~dp0\adbs_clear.bat %1


# adb_install
@echo off
echo 正在安装 %1...
call %~dp0\list_devices.bat %~dp0\device_install %1
ping 127.0.0.1 -n 5 >nul


# adb_log
@echo off
%~dp0\list_devices.bat %~dp0\device_logcat %1
ping 127.0.0.1 -n 3 >nul


# adb_logc
@echo off
%~dp0\list_devices.bat %~dp0\device_logcat -c %1


# adbs_clear
adb -s %1 shell pm clear %2


# device_install
@echo %1 正在安装 %2...，请注意手机屏幕的安装提示
adb -s %1 install -r -d -t %2


# device_logcat
```
@echo %1 正在logcat %2...
set hour=%time:~,2%
set logfile=%1
if "%time:~,1%"==" " set hour=0%time:~1,1%
@set hhmmss=%date:~0,4%%date:~5,2%%date:~8,2%_%hour%%time:~3,2%%time:~6,2%
start cmd /c "adb -s %1 logcat > %hhmmss%-%logfile::=#%.log"
```


# device_logcat-c
```
@echo %1 清除手机端logcat...
adb -s %1 logcat -c
```


# list_devices
```
@echo off

setlocal EnableDelayedExpansion
set /a adbLines=0

rem 搜集设备号
rem adb的结果会有N+1行，N代表N个设备，1是结果的首行提示，例如：
rem C:\Users\80321424>adb devices
rem List of devices attached
rem 3b5d2605   device
rem 4dc91823   device
rem 所以要用“device”这个关键字来过滤结果，会得到N+1个条目
for /f %%c in ('adb devices ^| find /i "device"') do  (
	rem 首行会有“List”字样，当这行不是“List”就认为是一个个的设备，并存放到device数组中去
	if %%c neq List (
		set deviceSn[!adbLines!]=%%c
		set /A adbLines+=1
	)
)
set /a c=%adbLines%
rem echo TotalDevices:%c%

:0_device
if %c% equ 0 (
	echo 没有设备接入
	goto end
)

:1_device
	if %c% equ 1 (
		call %1 !deviceSn[0]! %2
		goto end
	)

:N_device

	set /a deviceNum=%adbLines%-1
rem ##########################################
	echo 共有%c%个设备接入，请选择设备号：
	echo    0. 全部设备（默认）
	rem for /L 表示从0开始，间隔1，到设备总数为止
	if not exist %~dp0\no_device_info.bat (
		for /L %%m in (0, 1, %deviceNum%) do (
			for /f %%r in ('adb -s !deviceSn[%%m]! shell getprop ro.product.brand') do (set deviceBrand[%%m]=%%r)
			for /f %%s in ('adb -s !deviceSn[%%m]! shell getprop ro.product.name') do (set deviceName[%%m]=%%s)
		)
	)
rem ##########################################
	for /L %%j in (0, 1, %deviceNum%) do (
		rem lineNo:行号
		set /a lineNo=%%j+1
		echo    !lineNo!. !deviceName[%%j]!    	【!deviceBrand[%%j]!】    		!deviceSn[%%j]!
	)
	
	set user_selection=0
	set /p user_selection=请选择设备（默认0）：
	if [%user_selection%]==[] (goto pre-end)
	if %user_selection% equ 0 (
rem		echo %user_selection% is 0
		for /L %%k in (0, 1, %deviceNum%) do (
			call %1 !deviceSn[%%k]! %2
		)
	) else (
		set /a s=%user_selection%-1
rem		echo sel=!s!
rem		echo 您选择了 %user_selection%. %%deviceSn[!s!]%%
		call %1 %%deviceSn[!s!]%% %2
	)
:pre-end
	endlocal

:end

```
