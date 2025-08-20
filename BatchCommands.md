## Example Batch file to close all browser instances
----------------------------------------------------------

@echo off
REM Close Google Chrome
taskkill /F /IM chrome.exe

REM Close Mozilla Firefox
taskkill /F /IM firefox.exe

REM Close Microsoft Edge
taskkill /F /IM msedge.exe

REM Close Internet Explorer (if used)
taskkill /F /IM iexplore.exe

REM Close Opera (optional)
taskkill /F /IM opera.exe

echo All browser processes terminated.
pause
