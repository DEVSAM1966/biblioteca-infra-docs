# ☁️ DNS con Cloudflare

Solo si quieres usar Cloudflare como proxy y firewall, lo cual da:

- CDN global (más rápido)

- HTTPS automático

- Firewall y protección DDoS

- Ocultar la IP real de tu VPS

- Caché inteligente

- Reglas de seguridad

Si queremos estas ventajas → sí, debemos usar Cloudflare.

Si no → seguir con el provvedor de dominio sin problema.


Pero decidimos usar Cloudflare, la zona DNS se gestiona allí, no en DonDominio.

**No es el caso, por tanto no aplica**

---

## 🟧 PASO 1 — Crear cuenta en Cloudflare y añadir el dominio

1. Entra en Cloudflare

2. Añade el dominio: codigojava.com

3. Cloudflare escaneará tus DNS actuales

4. Te mostrará una lista de registros (A, AAAA, CNAME, etc.)

---

## 🟧 PASO 2 — Configurar los registros en Cloudflare

En Cloudflare, debes dejar solo estos registros:
✔️ Registro A (dominio raíz)

- Tipo: A

- Nombre: @

- IP: 51.79.84.186

- Proxy: ON (nube naranja)

- TTL: Auto

✔️ Registro A (www)

- Tipo: A

- Nombre: www

- IP: 51.79.84.186

- Proxy: ON

- TTL: Auto

✔️ Registros AAAA (opcional)

- Tipo: AAAA

- Nombre: @

- IPv6: 2607:5300:205:200::3d24

- Proxy: ON

- Tipo: AAAA

- Nombre: www

- IPv6: 2607:5300:205:200::3d24

- Proxy: ON

❌ Elimina cualquier registro que no sea estos

Especialmente:

- CNAME de parking

- A con IP 31.214.178.55

- Entradas automáticas de DonDominio

- Glue Records (Cloudflare no los usa)

---

## 🟧 PASO 3 — Cambiar los nameservers en DonDominio

Cloudflare te dará dos nameservers, por ejemplo:

- abby.ns.cloudflare.com

- mark.ns.cloudflare.com

En el proveedor de dominios:

- Ir a Dominios → codigojava.com → Servidores DNS

- Elimina los de DonDominio

- Añade los dos de Cloudflare

- Guarda

⏳ La propagación tarda entre 5 minutos y 24 horas.

---

## 🟧 PASO 4 — Activar HTTPS automático en Cloudflare

En Cloudflare:

1. Ve a SSL/TLS

2. Selecciona Full (strict)

3. Activa:

    - Always Use HTTPS

    - Automatic HTTPS Rewrites

    - HSTS (opcional)

---

## 🟧 PASO 5 — Configurar Nginx para Cloudflare

Cloudflare enviará tráfico a tu VPS, así que tu Nginx debe escuchar en 80 y 443.

Pero si usas Cloudflare, Certbot sigue funcionando, solo que debes usar:

```bash
sudo certbot --nginx -d codigojava.com -d www.codigojava.com
```

Cloudflare no interfiere con Certbot si usas modo Full (strict).