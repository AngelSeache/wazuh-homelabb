# Wazuh Dashboard — instalación manual en Arch Linux

## Pasos

### 1. Descargar y extraer
    wget https://packages.wazuh.com/4.x/yum/wazuh-dashboard-4.14.0-1.x86_64.rpm
    bsdtar -xf wazuh-dashboard-4.14.0-1.x86_64.rpm
    sudo cp -r etc usr /

### 2. Usuario del sistema
    sudo groupadd -f wazuh-dashboard
    sudo useradd -r -g wazuh-dashboard -d /usr/share/wazuh-dashboard -s /sbin/nologin wazuh-dashboard
    sudo chown -R wazuh-dashboard:wazuh-dashboard /usr/share/wazuh-dashboard /etc/wazuh-dashboard
    sudo mkdir -p /var/log/wazuh-dashboard /var/lib/wazuh-dashboard
    sudo chown -R wazuh-dashboard:wazuh-dashboard /var/log/wazuh-dashboard /var/lib/wazuh-dashboard

### 3. Certificados
    sudo mkdir -p /etc/wazuh-dashboard/certs
    sudo cp wazuh-certificates/dashboard-key.pem /etc/wazuh-dashboard/certs/
    sudo cp wazuh-certificates/dashboard.pem /etc/wazuh-dashboard/certs/
    sudo cp wazuh-certificates/root-ca.pem /etc/wazuh-dashboard/certs/
    sudo chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
    sudo chmod 500 /etc/wazuh-dashboard/certs
    sudo chmod 400 /etc/wazuh-dashboard/certs/*

### 4. Configurar puerto y credenciales
Editar /etc/wazuh-dashboard/opensearch_dashboards.yml:
    server.port: 5601
    opensearch.username: admin
    opensearch.password: admin

### 5. Arrancar
    sudo systemctl daemon-reload
    sudo systemctl enable --now wazuh-dashboard

## Verificación
    curl -k https://127.0.0.1:5601

Resultado esperado: HTTP 302 redirigiendo a /app/login

## Acceso web
https://<IP-arch>:5601 — usuario admin / contraseña admin (default, cambiar luego)

## Problemas encontrados
1. "EACCES: permission denied 0.0.0.0:443" → el proceso corre como usuario
   sin privilegios y no puede abrir puertos <1024. Solución: usar puerto 5601.
2. "ResponseError" constante → faltaban credenciales para conectar al indexer.
   Solución: descomentar opensearch.username / opensearch.password en el yml.
3. Error de parseo YAML → faltaba espacio después de los ":" en esas líneas
   (YAML es sensible a esto: "clave: valor", no "clave:valor").
