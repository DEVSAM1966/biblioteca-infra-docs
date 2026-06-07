# 🔐 Configuración SSH Segura

Este documento define la configuración recomendada para asegurar el acceso SSH en un VPS Ubuntu Server (24.x o 26.x).  
El objetivo es permitir únicamente autenticación por clave pública, deshabilitar el acceso inseguro y reforzar el servicio SSH.

---

## [1. Verificar instalación de OpenSSH](ca://s?q=Verificar_instalacion_OpenSSH)

Ubuntu Server suele incluir OpenSSH por defecto, pero se recomienda verificarlo.

### Comprobar si el servicio está instalado

```bash
sudo systemctl status ssh
```

### Instalarlo si fuera necesario

```bash
sudo apt install -y openssh-server
```

---

## [2. Crear o registrar la clave pública SSH](ca://s?q=Crear_o_registrar_clave_publica_SSH)

La autenticación por clave pública es obligatoria para deshabilitar el acceso por contraseña.

### En tu PC local (no en el servidor) de cada persona del equipo

```bash
ssh-keygen -t ed25519 -C "sebastian@biblioteca"
```

```bash
ssh-keygen -t ed25519 -C "mabel@biblioteca"
```

    Nota:
        sebastian@biblioteca y mabel@biblioteca no son correos reales y servira para identificar una clave cuando se tiene muchas.  Si se prefiere puede usarse un correo real, no será usado para validar.


La clave pública estará en (verificarlo), pongo la ubicación en un PC con linux:
```bash
~/.ssh/id_ed25519.pub
```

### Copiar la clave al servidor

**Sustituir usuario por los usuarios: sebastian, mabel.**

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub usuario@IP_DEL_SERVIDOR
```

Si ssh-copy-id no está disponible:
```bash
cat ~/.ssh/id_ed25519.pub | ssh usuario@IP "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

## [3. Configurar permisos correctos](ca://s?q=Configurar_permisos_SSH)

En el servidor:
```bash
chmod 700 ~/.ssh  
chmod 600 ~/.ssh/authorized_keys
```

Permisos incorrectos → SSH los ignora.

---

## [4. Editar configuración del servicio SSH](ca://s?q=Editar_configuracion_SSH)

Editar el archivo principal:

```bash
sudo nano /etc/ssh/sshd_config
```

Modificar o añadir las siguientes líneas:

- Deshabilitar login por contraseña

    **PasswordAuthentication no**

- Deshabilitar autenticación por teclado interactivo

    **KbdInteractiveAuthentication no**

- Deshabilitar acceso root

    **PermitRootLogin no**

- Solo claves públicas

    **PubkeyAuthentication yes**

- (Opcional) Cambiar el puerto SSH

    **Port 2222**

Despues guardar y cerrar.

---

## [5. Probar la conexión ANTES de reiniciar SSH](ca://s?q=Probar_conexion_SSH)

Abrir una segunda terminal y probar:

```bash
ssh -p 22 usuario@IP_DEL_SERVIDOR
```

Si cambiaste el puerto:

```bash
ssh -p 2222 usuario@IP_DEL_SERVIDOR
```

👉 **Nunca reinicies SSH sin probar antes**, para evitar bloquearte fuera del servidor.

---

## [6. Reiniciar el servicio SSH](ca://s?q=Reiniciar_servicio_SSH)

```bash
sudo systemctl restart ssh
```

---

## [7. Verificar que el puerto está escuchando](ca://s?q=Verificar_puerto_SSH)

```bash
sudo ss -tulpn | grep ssh
```

---

## [8. (Opcional) Deshabilitar completamente el acceso root](ca://s?q=Deshabilitar_acceso_root)

```bash
sudo passwd -l root
```

---

## [9. (Opcional) Configurar Fail2ban](ca://s?q=Configurar_Fail2ban)

Recomendado para bloquear intentos de fuerza bruta.

```bash
sudo apt install -y fail2ban
```

---

## [10. Resumen de seguridad aplicada](ca://s?q=Resumen_seguridad_SSH)

- Acceso solo por clave pública  
- Root deshabilitado  
- Contraseñas deshabilitadas  
- Puerto opcionalmente cambiado  
- Permisos correctos en ~/.ssh  
- Servicio SSH reforzado  
