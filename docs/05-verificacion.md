# Verificación final

## Objetivo
Confirmar que el agente Windows quedó correctamente registrado y activo en el manager, y que el stack completo (indexer + manager + dashboard + agente) está operativo.

## Pasos

### 1. Listar agentes desde el manager
Ejecutar en la máquina Arch:
    sudo /var/ossec/bin/agent_control -l

### 2. Resultado obtenido
    Wazuh agent_control. List of available agents:
       ID: 000, Name: archlinux (server), IP: 127.0.0.1, Active/Local
       ID: 001, Name: DESKTOP-ECGCVQB, IP: any, Active

- ID 000: el propio manager (agente local, siempre presente)
- ID 001: PC Windows 10, estado `Active`

### 3. Verificación desde el dashboard
Ingresar a `https://192.168.18.172:5601` (o `https://127.0.0.1:5601` desde la propia máquina Arch) → menú lateral → **Agents**. El agente `DESKTOP-ECGCVQB` debe listarse con estado `Active` y empezar a mostrar eventos/alertas a medida que el sistema Windows genera actividad.

## Estado final del stack

| Componente | Estado | Detalle |
|---|---|---|
| Wazuh Indexer | ✅ Operativo | Cluster GREEN, TLS con wazuh-certs-tool.sh |
| Wazuh Manager | ✅ Operativo | Puertos 1514/1515 abiertos, API Online |
| Wazuh Dashboard | ✅ Operativo | Puerto 5601, accesible vía https://192.168.18.172:5601 |
| Agente Windows 10 | ✅ Activo | ID 001, DESKTOP-ECGCVQB |

## Pendientes / mejoras futuras
- Cambiar credenciales por defecto (admin/admin) del dashboard.
- Fijar IP estática o reserva DHCP para la máquina Arch, para evitar que vuelva a cambiar y rompa el acceso.
- Si se quiere un nombre de agente más descriptivo, reinstalar con `WAZUH_AGENT_NAME` explícito.

- <img width="1826" height="925" alt="image" src="https://github.com/user-attachments/assets/d0aa46ed-a3ea-43e3-add7-eb5bcbd8ae27" />

