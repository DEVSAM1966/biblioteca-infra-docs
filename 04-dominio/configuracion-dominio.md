# 🌐 Configuración del Dominio

Partimos de que ya se es dueño del dominio www.codigojava.com.  

Los datos de conectividad de nuestro VPS es:

- Nombre del VPS: **vps-ea7501d2.vps.ovh.ca**
    
- Dirección IPv4: **51.79.84.186**

- Dirección IPv6: **2607:5300:205:200::3d24**
    
Procedemos a documentar los pasos a realizar.

---

## 🟦 1. Configurar DNS del dominio

En el panel donde compraste el dominio (ej: OVH, DonDominio, Namecheap, etc.) debes crear estos registros:

### ✔️ Registro A (para el dominio principal)

| Tipo | Nombre | Valor | TTL |
| --- | --- | --- | --- |
| A | @ | 51.79.84.186 | 3600 |

### ✔️ Registro A (para www)

| Tipo | Nombre | Valor | TTL |
| --- | --- | --- | --- |
| A | www | 51.79.84.186 | 3600 |

### ✔️ Registro AAAA (IPv6) — opcional

| Tipo | Nombre | Valor | TTL |
| --- | --- | --- | --- |
| AAAA | @ | 2607:5300:205:200::3d24 | 3600 |
| AAAA | www | 2607:5300:205:200::3d24 | 3600 |

Objetivo: que el dominio apunte al VPS. **La propagación puede tardar entre 5 minutos y 24 horas.**

---

## 🟦 2. Verificar propagación DNS

Podemos comprobar que apunte al VPS mediante:

```bash
dig codigojava.com
dig www.codigojava.com
```

O bien con las herramientas online:  

- https://dnschecker.org

- https://whatsmydns.net


## 🟦 3. Configurar Nginx en el VPS

Una vez que el dominio apunte al VPS, crea un archivo de configuración:

```bash
sudo nano /etc/nginx/sites-available/codigojava.com
```

Contenido recomendado:

```bash
server {
    listen 80;
    server_name codigojava.com www.codigojava.com;

    location / {
        proxy_pass http://localhost:3000;  # Frontend React
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /api/java/ {
        proxy_pass http://localhost:8080/; # Backend Java
    }

    location /api/node/ {
        proxy_pass http://localhost:3001/; # Backend Node
    }
}
```

Activar el sitio:

```bash
sudo ln -s /etc/nginx/sites-available/codigojava.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🟦 4. Instalar SSL con Let’s Encrypt (HTTPS)

**Esta parte no aplica para nosotros por no haber comprado al proveedor del dominio el certificado SSL.**

Instalar Certbot:

```bash
sudo apt install -y certbot python3-certbot-nginx
```

Generar certificados:

```bash
sudo certbot --nginx -d codigojava.com -d www.codigojava.com
```

Certbot:

- Configura HTTPS automáticamente

- Renueva certificados cada 90 días

---

## Propagación
Puede tardar 5–30 minutos a 24 horas.
