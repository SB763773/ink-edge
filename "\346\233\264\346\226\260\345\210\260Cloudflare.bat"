@echo off
setlocal EnableExtensions
cd /d "%~dp0"

set "CUSTOM_DOMAIN=aitrs.aicreatorhome.cn"
set "HEALTH_URL=https://aitrs.aicreatorhome.cn/health"
set "DRY_RUN_DIR=.wrangler-dry-run"

where npx.cmd >nul 2>&1
if errorlevel 1 (
  echo npx.cmd not found. Please install Node.js/npm first.
  pause
  exit /b 1
)

where curl.exe >nul 2>&1
if errorlevel 1 (
  echo curl.exe not found.
  pause
  exit /b 1
)

echo.
echo Running Cloudflare dry-run...
call npx.cmd wrangler deploy --dry-run --outdir "%DRY_RUN_DIR%"
if errorlevel 1 (
  echo Cloudflare dry-run failed.
  pause
  exit /b 1
)

if exist "%DRY_RUN_DIR%\README.md" del "%DRY_RUN_DIR%\README.md" >nul 2>&1
if exist "%DRY_RUN_DIR%\worker.js" del "%DRY_RUN_DIR%\worker.js" >nul 2>&1
if exist "%DRY_RUN_DIR%\worker.js.map" del "%DRY_RUN_DIR%\worker.js.map" >nul 2>&1
if exist "%DRY_RUN_DIR%" rmdir "%DRY_RUN_DIR%" >nul 2>&1

echo.
echo Deploying to Cloudflare Workers custom domain: %CUSTOM_DOMAIN%
call npx.cmd wrangler deploy --domain "%CUSTOM_DOMAIN%"
if errorlevel 1 (
  echo Cloudflare deploy failed.
  pause
  exit /b 1
)

echo.
echo Running health check: %HEALTH_URL%
curl.exe -f -s "%HEALTH_URL%"
if errorlevel 1 (
  echo.
  echo Health check failed. The deployment may still be propagating, or DNS may not be refreshed locally.
  pause
  exit /b 1
)

echo.
echo Updated Cloudflare site successfully.
pause
