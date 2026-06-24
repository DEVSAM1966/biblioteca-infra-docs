# 🔥 Configuración del Firewall (UFW)
Este documento define la configuración recomendada del firewall UFW para un VPS Ubuntu Server (24.x o 26.x).

El objetivo es permitir únicamente el tráfico necesario para la operación del servidor y bloquear todo lo demás.

    Nota:

        - La configuración del firewall se realiza una sola vez, con el usuario administrador.

        - UFW controla el tráfico de red del servidor completo, no por usuario.


## 1. Verificar si UFW está instalado
Ubuntu Server suele incluir UFW, pero es recomendable verificarlo.

**Comprobar instalación**

```bash
sudo ufw status
```

**Si no está instalado:**

```bash
sudo apt install -y ufw
```

---

## 2. Permitir conexiones SSH antes de activar UFW

    Regla crítica: si no permites SSH antes de activar UFW, te bloquearás fuera del servidor.

**Si usas el puerto SSH por defecto (22)**
```bash
sudo ufw allow 22/tcp
```

**Si se cambio el puerto SSH (por ejemplo, 2222)**
```bash
sudo ufw allow 2222/tcp
```

---

## 3. Permitir tráfico HTTP y HTTPS

Nginx necesita estos puertos para servir el frontend y actuar como reverse proxy.

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8080/tcp
```

---

## 4. (Opcional) Permitir MySQL solo si es necesario

    Recomendación:  
    No abrir MySQL al exterior salvo que se tenga una razón muy específica.
    Los backends acceden a MySQL desde Docker internamente, así que no se necesita abrir el puerto 3306.

Si por alguna razón es necesario el acceso remoto:

```bash
sudo ufw allow 3306/tcp
```

---

## 5. Bloquear todo lo demás por defecto

Configurar la política por defecto:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 6. Activar UFW

    Asegúrarse de haber permitido SSH antes de este paso.

```bash
sudo ufw enable
```

Confirmar con y.

---

## 7. Verificar reglas activas

```bash
sudo ufw status numbered
```

---

## 8. (Opcional) Eliminar reglas innecesarias

Listar reglas numeradas:

```bash
sudo ufw status numbered
```

Eliminar una regla:

```bash
sudo ufw delete NUMERO
```

---

## 9. (Opcional) Limitar intentos de conexión SSH

Protege contra ataques de fuerza bruta:

```bash
sudo ufw limit 22/tcp
```

Si usas un puerto personalizado:

```bash
sudo ufw limit 2222/tcp
```

---

## 10. Resumen de seguridad aplicada

- SSH permitido (puerto 22 o personalizado)

- HTTP permitido (80)

- HTTPS permitido (443)

- MySQL cerrado (recomendado)

- Todo el tráfico entrante bloqueado por defecto

- Firewall activado

- Reglas verificadas

- Protección contra fuerza bruta opcional


