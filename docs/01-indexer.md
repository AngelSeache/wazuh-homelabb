# Wazuh Indexer — instalación manual en Arch Linux

## Contexto
Se evita AUR (incidente de supply-chain jun-2026) y Docker.
Se instala extrayendo el .rpm oficial con bsdtar.

## Pasos

### 1. Dependencias (repos oficiales de Arch)
    sudo pacman -S libarchive wget

### 2. Descargar y extraer el paquete oficial
    wget https://packages.wazuh.com/4.x/yum/wazuh-indexer-4.14.0-1.x86_64.rpm
    bsdtar -xf wazuh-indexer-4.14.0-1.x86_64.rpm
    sudo cp -r etc usr var /

### 3. Crear usuario del sistema
    sudo groupadd -f wazuh-indexer
    sudo useradd -r -g wazuh-indexer -d /usr/share/wazuh-indexer -s /sbin/nologin wazuh-indexer
    sudo chown -R wazuh-indexer:wazuh-indexer /usr/share/wazuh-indexer /var/lib/wazuh-indexer /var/log/wazuh-indexer /etc/wazuh-indexer

### 4. Generar certificados TLS
    curl -sO https://packages.wazuh.com/4.14/wazuh-certs-tool.sh
    curl -sO https://packages.wazuh.com/4.14/config.yml
    # editar config.yml con la IP local en indexer/server/dashboard
    sudo ./wazuh-certs-tool.sh -A

### 5. Copiar certificados
    sudo mkdir -p /etc/wazuh-indexer/certs
    sudo cp wazuh-certificates/node-1-key.pem /etc/wazuh-indexer/certs/indexer-key.pem
    sudo cp wazuh-certificates/node-1.pem /etc/wazuh-indexer/certs/indexer.pem
    sudo cp wazuh-certificates/admin-key.pem /etc/wazuh-indexer/certs/admin-key.pem
    sudo cp wazuh-certificates/admin.pem /etc/wazuh-indexer/certs/admin.pem
    sudo cp wazuh-certificates/root-ca.pem /etc/wazuh-indexer/certs/root-ca.pem
    sudo chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
    sudo chmod 500 /etc/wazuh-indexer/certs
    sudo chmod 600 /etc/wazuh-indexer/certs/*

### 6. Arrancar
    sudo systemctl daemon-reload
    sudo systemctl enable --now wazuh-indexer

### 7. Inicializar seguridad interna
    sudo bash /usr/share/wazuh-indexer/bin/indexer-security-init.sh

## Verificación
    curl -k -u admin:admin https://127.0.0.1:9200

Resultado esperado: JSON con cluster_name "wazuh-cluster" y status GREEN.

## Problemas encontrados
- Al arrancar sin certificados: error "Unable to read the file root-ca.pem" →
  solucionado generando y copiando los certificados TLS oficiales.
