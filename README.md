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
