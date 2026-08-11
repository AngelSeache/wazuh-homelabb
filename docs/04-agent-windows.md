# Wazuh Agent — instalación en Windows 10

## Requisitos previos
- PC Windows 10 en la misma red que el manager (192.168.18.172)
- PowerShell como Administrador

## Pasos

### 1. Descargar el instalador
Desde el navegador en la PC Windows, descargar el MSI oficial:
    https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.7-1.msi

Nota: se usó la versión 4.14.7-1 (última disponible al momento), no la 4.14.0-1 planeada originalmente.

### 2. Desbloquear el archivo
Windows puede marcar el instalador descargado como bloqueado (Mark of the Web). Si `msiexec` falla o no arranca, desbloquear con:
    Unblock-File -Path .\wazuh-agent-4.14.7-1.msi

### 3. Instalar con ruta absoluta
Ejecutar en PowerShell como Administrador. Usar `$PWD` para forzar ruta absoluta (evita errores de resolución de ruta relativa):
    msiexec.exe /i "$PWD\wazuh-agent-4.14.7-1.msi" WAZUH_MANAGER="192.168.18.172"

### 4. Iniciar el servicio
    NET START WazuhSvc

### 5. Verificar el servicio en Windows
    Get-Service -Name WazuhSvc

Debe mostrar `Status: Running`, `Name: WazuhSvc`, `DisplayName: Wazuh`.

### 6. Verificar conexión desde el manager (Arch)
    sudo /var/ossec/bin/agent_control -l

El agente debe aparecer y pasar a estado `Active` en los primeros segundos/minutos.

## Notas
- `Status: Running` en Windows solo indica que el servicio arrancó — no confirma conexión con el manager. La verificación real se hace desde `agent_control -l` en el lado del manager.
- Confirmar siempre la IP del manager antes de instalar; un typo en `WAZUH_MANAGER` deja el agente corriendo pero sin conectarse (sin error visible en Windows).
- No se pasó `WAZUH_AGENT_NAME` en la instalación, por lo que el agente se registró con el hostname real de Windows (`DESKTOP-ECGCVQB`) en vez de un nombre custom como `pc-windows`. Si se quiere un nombre distinto, agregar el parámetro `WAZUH_AGENT_NAME="pc-windows"` al comando de instalación.

## Resultado confirmado
    $ sudo /var/ossec/bin/agent_control -l
    Wazuh agent_control. List of available agents:
       ID: 000, Name: archlinux (server), IP: 127.0.0.1, Active/Local
       ID: 001, Name: DESKTOP-ECGCVQB, IP: any, Active

Agente Windows conectado y activo.
