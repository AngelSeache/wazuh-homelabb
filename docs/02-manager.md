# Wazuh Manager — instalación manual en Arch Linux

## Pasos

### 1. Descargar y extraer
    wget https://packages.wazuh.com/4.x/yum/wazuh-manager-4.14.0-1.x86_64.rpm
    bsdtar -xf wazuh-manager-4.14.0-1.x86_64.rpm
    sudo cp -r etc usr var /

### 2. Usuario del sistema
    sudo groupadd -f wazuh
    sudo useradd -r -g wazuh -d /var/ossec -s /sbin/nologin wazuh
    sudo chown -R wazuh:wazuh /var/ossec

### 3. Certificados (reusa los generados junto al indexer)
    sudo mkdir -p /var/ossec/etc/sslmanager
    sudo cp wazuh-certificates/wazuh-1-key.pem /var/ossec/etc/sslmanager.key
    sudo cp wazuh-certificates/wazuh-1.pem /var/ossec/etc/sslmanager.cert
    sudo chown wazuh:wazuh /var/ossec/etc/sslmanager.key /var/ossec/etc/sslmanager.cert
    sudo chmod 640 /var/ossec/etc/sslmanager.key /var/ossec/etc/sslmanager.cert

### 4. Arrancar
    sudo systemctl daemon-reload
    sudo systemctl enable --now wazuh-manager

## Verificación
    sudo ss -tulnp | grep -E '1514|1515'

Resultado esperado: wazuh-authd en 1515, wazuh-remoted en 1514.
