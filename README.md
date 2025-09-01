# Bitcoin Node Backup

[![Status](https://img.shields.io/badge/status-stable-green)](https://github.com/)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Rclone](https://img.shields.io/badge/tool-rclone-yellow)](https://rclone.org/)

---

## 📦 Descripción

Este repositorio documenta la **copia de seguridad segura y confiable de un nodo completo de Bitcoin** en Google Drive. Permite mantener los datos críticos (`blocks`, `chainstate`, `indexes`) organizados y listos para restaurar en otra VM de manera confiable.

Se enfoca en:  

- Integridad de los datos respaldados.  
- Subidas incrementales para ahorrar ancho de banda.  
- Organización clara que facilita la restauración.  

---

## 📁 Estructura del Backup en Google Drive

bitcoin/

├── blocks/ # Archivos de bloques (.dat)

├── chainstate/ # Estado actual de la blockchain

└── indexes/ # Índices opcionales (txindex, addressindex, etc.)


Cada carpeta mantiene la misma estructura que la VM local para garantizar que el nodo pueda reiniciarse sin problemas.

---

## ⚙️ Requisitos

- **VM con nodo Bitcoin** (Ubuntu u otra distro Linux).  
- **rclone** instalado y configurado con acceso a Google Drive.  
- Acceso al directorio del nodo (`/mnt/bitcoin` en este ejemplo).  

---

## 🚀 Proceso de Backup

### 1️⃣ Subida de `blocks` (puede realizarse mientras el nodo corre)
```bash
rclone copy /mnt/bitcoin/blocks gdrive:bitcoin/blocks \
  --progress --update --transfers=4 
`````

### 2️⃣ Subida de chainstate (recomendado con nodo detenido) 
```bash
rclone copy /mnt/bitcoin/chainstate gdrive:bitcoin/chainstate \
  --progress --update --transfers=4
```
### 3️⃣ Subida de indexes (recomendado con nodo detenido)
```bash 
rclone copy /mnt/bitcoin/indexes gdrive:bitcoin/indexes \
  --progress --update --transfers=4
```

## 🔒 Notas de seguridad e integridad

--update sube solo archivos nuevos o modificados, evitando duplicación.

rclone verifica hashes automáticamente durante la transferencia.

Para chainstate e indexes, detener temporalmente el nodo evita copiar datos en modificación.

Para verificar la integridad después de la subida completa:
```bash
rclone check /mnt/bitcoin gdrive:bitcoin
```

## 💻 Restauración en otra VM

Para restaurar el nodo completo desde Google Drive:
```bash
rclone copy gdrive:bitcoin /mnt/bitcoin --progress
```

Mantener la misma estructura de carpetas garantiza que el nodo se inicie correctamente.

## 📊 Resumen Visual del Flujo
```bash
VM Local                Google Drive
----------------------------------------
blocks    ----copy-->    bitcoin/blocks
chainstate ----copy-->   bitcoin/chainstate
indexes   ----copy-->    bitcoin/indexes
```

## ✅ Conclusión

Este repositorio y metodología proporcionan un backup confiable y seguro de un nodo completo de Bitcoin, permitiendo restauración rápida y asegurando la integridad de los datos en la nube.


