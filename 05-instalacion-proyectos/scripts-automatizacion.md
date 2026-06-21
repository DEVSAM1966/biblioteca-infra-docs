# 🤖 Scripts de Automatización

## 🟦 1. Estructura final de servicios en el VPS

| Servicio | Tecnología | Puerto | Gestor |
| --- | --- | --- | --- |
| Backend Java | Spring Boot | 9800 | systemd |
| Backend Node | Node.js (dist) | 9800 | PM2 |
| MySQL Java | Docker | 3306 | Docker |
| MySQL Node | Docker | 3307 | Docker |
| Frontend React | Vite/React | 5173 (interno) | PM2 |
| Nginx | Reverse Proxy | 80 | systemd |

---

## 🟩 2. Scripts oficiales para automatizar arranques

Los scripts se guardarán en: **/opt/biblioteca/scripts**

Crear carpeta:

```bash
sudo mkdir -p /opt/biblioteca/scripts
sudo chown -R $USER:$USER /opt/biblioteca/scripts
```

---

### ⭐ SCRIPT 1 — Arrancar SOLO el backend Java

Crear el siguiente archivo con nano:

```bash
nano /opt/biblioteca/scripts/start-java.sh
```

El contenido de este archivo:

```bash
#!/bin/bash

BLUE="\e[34m"
RED="\e[31m"
GREEN="\e[32m"
RESET="\e[0m"

echo -e "${BLUE}🟦 Iniciando contenedor MySQL del backend Java...${RESET}"
docker start biblio_codigojava_mysql >/dev/null 2>&1

echo -e "${RED}🟥 Deteniendo backend Node...${RESET}"
pm2 stop biblioteca-node >/dev/null 2>&1

echo -e "${BLUE}🟦 Iniciando backend Java...${RESET}"
sudo systemctl start biblioteca >/dev/null 2>&1

echo -e "${GREEN}✔ Backend Java activo en puerto 9800${RESET}"
```

Damos los siguientes permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-java.sh
```

---

### ⭐ SCRIPT 2 — Arrancar SOLO el backend Node (ANSI)

Crear el siguiente archivo con nano:

```bash
nano /opt/biblioteca/scripts/start-node.sh
```

El contenido de este archivo será:

```bash
#!/bin/bash

ORANGE="\e[38;5;208m"
RED="\e[31m"
GREEN="\e[32m"
RESET="\e[0m"

echo -e "${ORANGE}🟧 Iniciando contenedor MySQL del backend Node...${RESET}"
docker start biblio_mysql >/dev/null 2>&1

echo -e "${RED}🟥 Deteniendo backend Java...${RESET}"
sudo systemctl stop biblioteca >/dev/null 2>&1

echo -e "${ORANGE}🟧 Iniciando backend Node...${RESET}"
pm2 start biblioteca-node >/dev/null 2>&1

echo -e "${GREEN}✔ Backend Node activo en puerto 9800${RESET}"
```

Damos los permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-node.sh
```

---

### ⭐ SCRIPT 3 — Arrancar el frontend React (ANSI)

Crearemos con nano el siguiente archivo:

```bash
nano /opt/biblioteca/scripts/start-frontend.sh
```

El contenido de este archivo será:

```bash
#!/bin/bash

GREEN="\e[32m"
RESET="\e[0m"

echo -e "${GREEN}🟩 Iniciando frontend React...${RESET}"
pm2 start "npm run preview" --name biblioteca-frontend >/dev/null 2>&1

echo -e "${GREEN}✔ Frontend activo detrás de Nginx${RESET}"
```

Y daremos estos permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-frontend.sh
```

---

### ⭐ SCRIPT 4 — Arrancar TODOS los servicios (ANSI)

1. **Script de arranque interactivo preguntando que backend se activa.**

Creamos el siguiente archivo con nano:

```bash
nano /opt/biblioteca/scripts/start-all.sh
```

Su contenido será el siguiente, con menú interactivo (pregunta que backend activamos): 

```bash
#!/bin/bash

# ===== COLORES ANSI =====
BLUE="\e[34m"
RED="\e[31m"
GREEN="\e[32m"
ORANGE="\e[38;5;208m"
CYAN="\e[36m"
RESET="\e[0m"

echo -e "${CYAN}"
echo "==============================================="
echo "   🚀 SISTEMA DE ARRANQUE — BIBLIOTECA VPS"
echo "==============================================="
echo -e "${RESET}"

echo -e "${BLUE}Seleccione el backend que desea activar:${RESET}"
echo -e "${GREEN}1) Backend Java${RESET}"
echo -e "${ORANGE}2) Backend Node.js${RESET}"
echo -e "${RED}3) Cancelar${RESET}"
echo -n "Opción: "
read opcion

case $opcion in

# ============================================================
# 🟦 OPCIÓN 1 — ACTIVAR BACKEND JAVA
# ============================================================
1)
    echo -e "${BLUE}🟦 Activando backend Java...${RESET}"

    echo -e "${BLUE}→ Iniciando contenedor MySQL del backend Java...${RESET}"
    docker start biblio_codigojava_mysql >/dev/null 2>&1

    echo -e "${RED}→ Deteniendo backend Node y su contenedor...${RESET}"
    pm2 stop biblioteca-node >/dev/null 2>&1
    docker stop biblio_mysql >/dev/null 2>&1

    echo -e "${BLUE}→ Iniciando backend Java (systemd)...${RESET}"
    sudo systemctl start biblioteca >/dev/null 2>&1

    echo -e "${GREEN}✔ Backend Java activo en puerto 9800${RESET}"
    ;;

# ============================================================
# 🟧 OPCIÓN 2 — ACTIVAR BACKEND NODE
# ============================================================
2)
    echo -e "${ORANGE}🟧 Activando backend Node.js...${RESET}"

    echo -e "${ORANGE}→ Iniciando contenedor MySQL del backend Node...${RESET}"
    docker start biblio_mysql >/dev/null 2>&1

    echo -e "${RED}→ Deteniendo backend Java y su contenedor...${RESET}"
    sudo systemctl stop biblioteca >/dev/null 2>&1
    docker stop biblio_codigojava_mysql >/dev/null 2>&1

    echo -e "${ORANGE}→ Iniciando backend Node (PM2)...${RESET}"
    pm2 start biblioteca-node >/dev/null 2>&1

    echo -e "${GREEN}✔ Backend Node activo en puerto 9800${RESET}"
    ;;

# ============================================================
# ❌ CANCELAR
# ============================================================
3)
    echo -e "${RED}Operación cancelada.${RESET}"
    exit 0
    ;;

*)
    echo -e "${RED}❌ Opción no válida.${RESET}"
    exit 1
    ;;
esac

# ============================================================
# 🟩 ARRANCAR FRONTEND (SIEMPRE)
# ============================================================
echo -e "${GREEN}🟩 Iniciando frontend React...${RESET}"
/opt/biblioteca/scripts/start-frontend.sh >/dev/null 2>&1
echo -e "${GREEN}✔ Frontend activo detrás de Nginx${RESET}"

# ============================================================
# 🟦 VERIFICACIÓN FINAL
# ============================================================
echo -e "${CYAN}"
echo "==============================================="
echo "   ✔ SISTEMA LISTO — SERVICIOS ACTIVOS"
echo "==============================================="
echo -e "${RESET}"

echo -e "${CYAN}Backend activo en puerto 9800:${RESET}"
ss -tlnp | grep 9800

```

Le daremos los siguientes permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-all.sh
```

2. **Script de arranque - Activa contenedor Java + backend Java + frontend, y apaga Node**

Con nano creamos el siguiente archivo:

```bash
nano /opt/biblioteca/scripts/start-java-stack.sh
```

Su contenido es el siguiente:

```bash
#!/bin/bash

BLUE="\e[34m"
RED="\e[31m"
GREEN="\e[32m"
RESET="\e[0m"

echo -e "${BLUE}🟦 ACTIVANDO STACK JAVA...${RESET}"

# --- Iniciar contenedor + backend Java usando tu script ---
echo -e "${BLUE}→ Ejecutando start-java.sh...${RESET}"
/opt/biblioteca/scripts/start-java.sh >/dev/null 2>&1

# --- Detener contenedor + backend Node ---
echo -e "${RED}→ Deteniendo backend Node y su contenedor MySQL...${RESET}"
pm2 stop biblioteca-node >/dev/null 2>&1
docker stop biblio_mysql >/dev/null 2>&1

# --- Iniciar frontend ---
echo -e "${GREEN}→ Iniciando frontend React...${RESET}"
/opt/biblioteca/scripts/start-frontend.sh >/dev/null 2>&1

echo -e "${GREEN}✔ Stack Java activo (backend + frontend)${RESET}"
```

Damos los permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-java-stack.sh
```

3. **Script de arranque - Activa contenedor Node + backend Node + frontend, y apaga Java**

Creamos con nano el archivo:

```bash
nano /opt/biblioteca/scripts/start-node-stack.sh
```

Su contenido es el siguiente:

```bash
#!/bin/bash

ORANGE="\e[38;5;208m"
RED="\e[31m"
GREEN="\e[32m"
RESET="\e[0m"

echo -e "${ORANGE}🟧 ACTIVANDO STACK NODE.JS...${RESET}"

# --- Iniciar contenedor + backend Node usando tu script ---
echo -e "${ORANGE}→ Ejecutando start-node.sh...${RESET}"
/opt/biblioteca/scripts/start-node.sh >/dev/null 2>&1

# --- Detener contenedor + backend Java ---
echo -e "${RED}→ Deteniendo backend Java y su contenedor MySQL...${RESET}"
sudo systemctl stop biblioteca >/dev/null 2>&1
docker stop biblio_codigojava_mysql >/dev/null 2>&1

# --- Iniciar frontend ---
echo -e "${GREEN}→ Iniciando frontend React...${RESET}"
/opt/biblioteca/scripts/start-frontend.sh >/dev/null 2>&1

echo -e "${GREEN}✔ Stack Node activo (backend + frontend)${RESET}"
```

Damos los siguientes permisos:

```bash
chmod +x /opt/biblioteca/scripts/start-node-stack.sh
```

---

## 🟧 3. Pasos manuales (si no se usan scripts)

### 🟦 3.1 Arrancar contenedores MySQL

**MySQL Java:**

```bash
docker start biblio_codigojava_mysql
```

**MySQL Node:**

```bash
docker start biblio_mysql
```

### 🟩 3.2 Activar backend Java

```bash
sudo systemctl stop biblioteca-node
sudo systemctl start biblioteca
```

### 🟧 3.3 Activar backend Node

```bash
sudo systemctl stop biblioteca
pm2 start biblioteca-node
```

### 🟨 3.4 Activar frontend React

```bash
pm2 start biblioteca-frontend
```

---

## 🟥 4. Verificación rápida

**¿Qué backend está activo?**

```bash
ss -tlnp | grep 9800
```

**Ver logs del backend Node:**

```bash
pm2 logs biblioteca-node
```

**Ver logs del backend Java:**

```bash
sudo journalctl -u biblioteca -f
```

**Ver contenedores MySQL:**

```bash
docker ps
```

---

## ⭐ 5. Recomendación final de Spock (*)

**“La automatización elimina el error humano.**
**La lógica elimina la incertidumbre.**
**Juntas… garantizan que el VPS nunca quede a oscuras.”**


    — Comandante Spock, Primer Oficial y Oficial Científico de la USS Enterprise (NCC‑1701 y NCC‑1701‑A). 


(*)Personaje de ficción de la serie Star Trek.

