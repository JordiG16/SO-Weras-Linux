@echo off
setlocal enabledelayedexpansion

rem Inicializa variables
set "direccion="
set "opc2="
set "contador=0"

:Menu
cls
echo 1. Ejecutar proceso o procesos
echo 2. Cerrar proceso abierto
echo 3. Salir
set /p opcion="Elige tu opcion: "

if "%opcion%"=="1" (
    call :Ejecutar
    goto :Menu
) else if "%opcion%"=="2" (
    call :CerrarProceso
    goto :Menu
) else if "%opcion%"=="3" (
    echo Saliendo...
    exit /b
) else (
    echo Solo puede escoger la opcion 1, 2 o 3
    timeout /t 2 /nobreak >nul
    goto :Menu
)

:Ejecutar
    cls
    echo Ingrese la direccion del proceso que deseas ejecutar:
    set /p direccion="Ruta del proceso: "

    rem Validar si el archivo existe
    if not exist "%direccion%" (
        echo No existe la direccion que brindaste
        timeout /t 2 /nobreak >nul
        goto :Menu
    )

    rem Obtener el nombre del archivo (sin ruta)
    for %%F in ("%direccion%") do set "nombreArchivo=%%~nxF"

    echo Ejecutando %direccion%
    start "" "%direccion%"

    rem Medir el tiempo de ejecución
    for /f "tokens=1-4 delims=:,." %%a in ("%time%") do (
        set /a startTime=%%a*3600 + %%b*60 + %%c
    )

    rem Espera a que el proceso termine (ajusta el tiempo si es necesario)
    timeout /t 5 /nobreak >nul

    for /f "tokens=1-4 delims=:,." %%a in ("%time%") do (
        set /a endTime=%%a*3600 + %%b*60 + %%c
    )

    rem Calcular el tiempo transcurrido
    set /a elapsedTime=endTime-startTime
    set /a contador=elapsedTime
    echo Tiempo transcurrido: %contador% segundos

    rem Preguntar si se desea ejecutar otro archivo
    set /p opc2="¿Deseas ejecutar otro archivo? (s/n): "
    if /i "%opc2%"=="s" (
        goto :Ejecutar
    ) else (
        echo Cerrando programa
        timeout /t 2 /nobreak >nul
        exit /b
    )

:CerrarProceso
    cls
    echo Cerrar el proceso %nombreArchivo%
    taskkill /IM "%nombreArchivo%" /F
    if %errorlevel%==0 (
        echo Proceso cerrado correctamente.
    ) else (
        echo No se pudo cerrar el proceso o el proceso no esta en ejecucion.
    )
    timeout /t 2 /nobreak >nul
    goto :Menu