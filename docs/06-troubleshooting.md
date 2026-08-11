# Troubleshooting

## Error: "Unable to connect" al entrar a https://<IP>:5601 desde el navegador

**Síntoma:**
Firefox muestra `Unable to connect` / `Firefox can't connect to the server at <IP>:5601`, pero `curl -k https://127.0.0.1:5601` desde la misma máquina Arch responde correctamente.

**Diagnóstico:**

### 1. Confirmar que el servicio está activo
    sudo systemctl status wazuh-dashboard --no-pager

### 2. Confirmar que escucha en todas las interfaces (no solo localhost)
    sudo ss -tlnp | grep 5601
    # Debe verse: LISTEN ... 0.0.0.0:5601

### 3. Confirmar la IP real de la máquina (puede cambiar por DHCP)
    ip -4 addr show | grep inet

### 4. Revisar firewall
    sudo systemctl status ufw firewalld 2>/dev/null
    sudo nft list ruleset 2>/dev/null

**Causa real en este caso:**
La IP de la máquina Arch había cambiado por DHCP de `192.168.18.72` a `192.168.18.172`, pero se seguía accediendo con la IP vieja. El dashboard estaba corriendo bien en `0.0.0.0:5601` sin firewall — el problema era solo la IP incorrecta en la URL.

**Solución:**
- Verificar la IP actual con `ip -4 addr show`.
- Acceder con la IP correcta: `https://192.168.18.172:5601`.
- Si la IP cambia seguido, considerar IP estática o reserva DHCP en el router.

**Nota:** el navegador puede mostrar advertencia de certificado TLS si la IP no coincide con el SAN del certificado generado (`wazuh-certs-tool.sh`). Es esperable en homelab; aceptar la excepción.
