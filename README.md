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
