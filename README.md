# wazuh-homelabb
Documentacion paso a paso de como instale Wazuh de forma manual
(indexer + manager + dashboard) en Arch Linux, usando paquetes
oficiales extraidos a mano, mas un agente en Windows para monitoreo.

## Precaucion y motivo
Evitar el AUR por el incidente de supply-chain de junio 2026
(paquetes comprometidos con infostealer/rootkit).

## Estructura 
- `docs/01-indexer.md` — instalación del Wazuh Indexer
- `docs/02-manager.md` — instalación del Wazuh Manager
- `docs/03-dashboard.md` — instalación del Wazuh Dashboard
- `docs/04-agent-windows.md` — enrolamiento del agente Windows
- `docs/05-verificacion.md` — pruebas de que todo funciona 
