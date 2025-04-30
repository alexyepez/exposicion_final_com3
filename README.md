# Presentación: Virtualización de Servidores

## 1. Introducción
La virtualización de servidores es una tecnología clave en la gestión de infraestructuras de red modernas, utilizada en el proyecto final de Comunicaciones III para implementar servicios de correo (Postfix) y archivos (Samba) en un entorno eficiente y escalable. Esta presentación explica los conceptos, operación, beneficios, requerimientos y una guía práctica para implementar la virtualización en el laboratorio físico, alineada con los objetivos del proyecto.

---

## 2. Conceptos de Virtualización de Servidores

### 2.1 ¿Qué es la virtualización de servidores?
La virtualización de servidores es el proceso de crear múltiples servidores virtuales (máquinas virtuales o VMs) en un solo servidor físico mediante un software llamado **hipervisor**. Cada máquina virtual ejecuta su propio sistema operativo y aplicaciones, compartiendo los recursos del hardware físico (CPU, memoria, almacenamiento, red).

### 2.2 Tipos de virtualización
- **Virtualización completa**: El hipervisor abstrae completamente el hardware (ejemplo: VMware ESXi, Microsoft Hyper-V).
- **Para-virtualización**: Las VMs son conscientes del hipervisor (ejemplo: Xen con modificaciones en el SO).
- **Virtualización a nivel de SO**: Múltiples instancias de un SO comparten el mismo kernel (ejemplo: contenedores Docker).
- **En el proyecto**: Usamos virtualización completa con VMware Workstation para alojar un servidor Ubuntu con Samba y Postfix.

### 2.3 Componentes clave
- **Hipervisor**: Software que gestiona las VMs (Tipo 1: ESXi, Tipo 2: VMware Workstation).
- **Máquinas virtuales**: Instancias aisladas con SO y aplicaciones.
- **Hardware físico**: Servidor con CPU, RAM, almacenamiento y red.
- **Red virtual**: Switches virtuales para conectar VMs a la red física o entre sí.

---

## 3. Forma de Operación

### 3.1 ¿Cómo funciona la virtualización?
1. **Instalación del hipervisor**: Se instala en el servidor físico (Tipo 1) o en un SO anfitrión (Tipo 2).
2. **Creación de VMs**: Se configuran máquinas virtuales asignando recursos (CPU, RAM, disco).
3. **Gestión de recursos**: El hipervisor asigna dinámicamente CPU, memoria y red a las VMs según la demanda.
4. **Conexión de red**: Las VMs se conectan a switches virtuales, que se vinculan a la red física mediante adaptadores.
5. **Ejecución de servicios**: Cada VM ejecuta aplicaciones (en nuestro caso, Samba y Postfix en Ubuntu).

### 3.2 En el contexto del proyecto
- **Hipervisor**: VMware Workstation (Tipo 2) en un PC del laboratorio o VMware ESXi (Tipo 1) si el hardware lo permite.
- **VM**: Una máquina virtual Ubuntu Server con:
  - IP: 172.16.20.161 (IPv4) y 2001:dbad:acad:5000::2 (IPv6).
  - Servicios: Samba (archivos con 3 perfiles) y Postfix/Dovecot (correo para 10 usuarios).
- **Red**: La VM se conecta a la red física del laboratorio (subred 172.16.20.160/28) mediante un adaptador en modo puente, permitiendo acceso desde todas las VLANs.

### 3.3 Flujo operativo
1. El hipervisor (VMware) inicia la VM Ubuntu.
2. Ubuntu ejecuta Samba y Postfix, escuchando en 172.16.20.161.
3. Los clientes en las VLANs (Contabilidad, Desarrollo, etc.) acceden a los servicios a través del enrutamiento inter-VLAN configurado en el Router EDGE.
4. El hipervisor gestiona los recursos para garantizar estabilidad.

---

## 4. Beneficios de la Virtualización de Servidores

### 4.1 Beneficios
- **Eficiencia de recursos**: Múltiples VMs en un solo servidor físico reducen costos de hardware.
- **Escalabilidad**: Fácil añadir nuevas VMs para nuevos servicios o edificios.
- **Aislamiento**: Cada VM es independiente, mejorando la seguridad y estabilidad.
- **Flexibilidad**: Soporte para diferentes SOs (Linux, Windows) en el mismo hardware.
- **Gestión simplificada**: Herramientas como VMware permiten snapshots, clonación y migración de VMs.
- **En el proyecto**: 
  - Un solo servidor físico aloja Samba y Postfix, reduciendo la necesidad de hardware adicional.
  - Facilita pruebas y recuperación mediante snapshots antes de la implementación en el laboratorio.

### 4.2 Requerimientos
- **Hardware**:
  - CPU con soporte para virtualización (Intel VT-x o AMD-V).
  - Mínimo 8 GB de RAM (16 GB recomendado para múltiples VMs).
  - Almacenamiento: 100 GB de disco duro (SSD preferido).
  - Tarjeta de red compatible con el laboratorio.
- **Software**:
  - Hipervisor: VMware Workstation (Windows/Linux) o ESXi (servidor dedicado).
  - Sistema operativo para la VM: Ubuntu Server 22.04 LTS.
- **Red**:
  - Conexión a un switch físico en la subred 172.16.20.160/28.
  - Configuración de VLANs en switches Cisco para acceso desde todas las VLANs.
- **Conocimientos**:
  - Configuración de redes (VLANs, enrutamiento).
  - Administración de Linux (Samba, Postfix, Dovecot).
  - Uso del hipervisor (creación y gestión de VMs).

---

## 5. Guía Paso a Paso: Implementación en el Laboratorio Físico

### 5.1 Objetivo
Implementar un servidor virtualizado con Ubuntu Server en el laboratorio físico, alojando Samba y Postfix, accesible desde las VLANs del proyecto (Contabilidad, Desarrollo, Diseño, Adm. Red).

### 5.2 Requerimientos específicos
- **Equipo físico**: PC o servidor en el laboratorio con:
  - CPU con virtualización habilitada (verificar en BIOS).
  - 16 GB de RAM.
  - 100 GB de almacenamiento.
  - Windows/Linux como SO anfitrión (para VMware Workstation).
- **Software**:
  - VMware Workstation 17 (o ESXi si es un servidor dedicado).
  - ISO de Ubuntu Server 22.04 LTS.
- **Red**:
  - Conexión al switch físico en la subred 172.16.20.160/28.
  - Switches Cisco configurados con VLANs 100, 200, 300, 400.
- **Configuración previa**:
  - Router EDGE con enrutamiento inter-VLAN (Router-on-a-Stick).
  - Direcciones IP del proyecto: 172.16.16.0/20 (IPv4) y 2001:dbad:acad::/48 (IPv6).

### 5.3 Pasos de implementación

#### Paso 1: Instalar VMware Workstation
1. Descarga VMware Workstation desde [vmware.com](https://www.vmware.com).
2. Instala en el PC del laboratorio (Windows/Linux).
3. Verifica que la virtualización esté habilitada en la BIOS:
   - Reinicia el PC, entra a la BIOS (tecla F2, Del, o similar).
   - Habilita Intel VT-x o AMD-V.
4. Abre VMware Workstation y verifica que detecte el soporte de virtualización.

#### Paso 2: Crear la máquina virtual
1. En VMware, selecciona **File > New Virtual Machine**.
2. Elige **Typical (recommended)** y haz clic en **Next**.
3. Selecciona **Installer disc image file (iso)** y carga el archivo ISO de Ubuntu Server 22.04.
4. Configura:
   - Nombre: `Servidor-Ubuntu-Proyecto`.
   - Ubicación: Carpeta en el disco del laboratorio.
5. Asigna recursos:
   - CPU: 2 vCPUs.
   - RAM: 4 GB.
   - Disco: 50 GB (dinámico).
6. Configura la red:
   - Adaptador: **Bridged** (conectar a la red física del laboratorio).
7. Finaliza y enciende la VM.

#### Paso 3: Instalar Ubuntu Server
1. Sigue el asistente de instalación de Ubuntu:
   - Idioma: Español.
   - Configuración de red: Asigna manualmente:
     - IPv4: 172.16.20.161/28
     - IPv6: 2001:dbad:acad:5000::2/64
     - Gateway: 172.16.20.160 (IPv4), 2001:dbad:acad:5000::1 (IPv6)
     - DNS: 8.8.8.8
   - Nombre del servidor: `mail.empresa.com`.
2. Selecciona la instalación mínima.
3. Crea un usuario administrativo (ejemplo: `admin`, contraseña: `Admin123`).
4. Habilita SSH para acceso remoto:
   ```bash
   sudo apt update
   sudo apt install openssh-server -y
   sudo systemctl enable ssh
   ```
5. Reinicia la VM.

#### Paso 4: Configurar Samba
1. Instala Samba:
   ```bash
   sudo apt install samba -y
   ```
2. Crea directorios para los recursos compartidos:
   ```bash
   sudo mkdir -p /srv/samba/contabilidad
   sudo mkdir -p /srv/samba/desarrollo
   sudo mkdir -p /srv/samba/admon
   ```
3. Crea grupos y usuarios:
   ```bash
   sudo groupadd contabilidad
   sudo groupadd desarrollo
   sudo groupadd admon
   sudo useradd -m -g contabilidad -s /bin/false usuario_cont1
   sudo useradd -m -g desarrollo -s /bin/false usuario_dev1
   sudo useradd -m -g admon -s /bin/false usuario_adm1
   sudo smbpasswd -a usuario_cont1
   sudo smbpasswd -a usuario_dev1
   sudo smbpasswd -a usuario_adm1
   ```
4. Configura permisos:
   ```bash
   sudo chown :contabilidad /srv/samba/contabilidad
   sudo chmod 750 /srv/samba/contabilidad
   sudo chown :desarrollo /srv/samba/desarrollo
   sudo chmod 770 /srv/samba/desarrollo
   sudo chown :admon /srv/samba/admon
   sudo chmod 770 /srv/samba/admon
   ```
5. Edita `/etc/samba/smb.conf`:
   ```ini
   [global]
      bind interfaces only = yes
      interfaces = 172.16.20.161 2001:dbad:acad:5000::2
   [Contabilidad]
      path = /srv/samba/contabilidad
      read only = yes
      valid users = @contabilidad
      browsable = yes
   [Desarrollo]
      path = /srv/samba/desarrollo
      read only = no
      valid users = @desarrollo
      browsable = yes
   [Admon]
      path = /srv/samba/admon
      read only = no
      create mask = 0777
      directory mask = 0777
      valid users = @admon
      browsable = yes
   ```
6. Reinicia Samba:
   ```bash
   sudo systemctl restart smbd nmbd
   ```
7. Abre puertos en el firewall:
   ```bash
   sudo ufw allow Samba
   ```

#### Paso 5: Configurar Postfix y Dovecot
1. Instala Postfix y Dovecot:
   ```bash
   sudo apt install postfix dovecot-core dovecot-imapd -y
   ```
2. Configura Postfix (`/etc/postfix/main.cf`):
   ```ini
   myhostname = mail.empresa.com
   mydomain = empresa.com
   inet_interfaces = 172.16.20.161, 2001:dbad:acad:5000::2
   mynetworks = 172.16.16.0/20, 2001:dbad:acad::/48
   mydestination = $myhostname, localhost.$mydomain, $mydomain
   ```
3. Crea 10 usuarios para correo:
   ```bash
   sudo useradd -m -s /bin/false correo1
   sudo passwd correo1
   # Repite para correo2 a correo10
   ```
4. Configura Dovecot:
   - Edita `/etc/dovecot/conf.d/10-auth.conf`:
     ```ini
     disable_plaintext_auth = no
     auth_mechanisms = plain login
     ```
   - Edita `/etc/dovecot/conf.d/10-mail.conf`:
     ```ini
     mail_location = maildir:~/Maildir
     ```
   - Edita `/etc/dovecot/conf.d/10-master.conf`:
     ```ini
     service imap-login {
       inet_listener imap {
         address = 172.16.20.161, 2001:dbad:acad:5000::2
         port = 143
       }
     }
     ```
5. Reinicia servicios:
   ```bash
   sudo systemctl restart postfix dovecot
   ```
6. Abre puertos:
   ```bash
   sudo ufw allow 25/tcp
   sudo ufw allow 143/tcp
   ```

#### Paso 6: Conectar la VM al laboratorio físico
1. Conecta el PC del laboratorio (con VMware) al switch físico en el puerto asignado a la subred 172.16.20.160/28.
2. Verifica que el switch esté configurado con un puerto troncal o acceso a la VLAN correspondiente (por ejemplo, VLAN 400 para Adm. Red o un trunk si el servidor es accesible desde todas las VLANs).
3. En VMware, asegura que el adaptador de red esté en **Bridged**:
   - Edita la configuración de la VM > Network Adapter > Bridged.
4. Prueba la conectividad desde un cliente en el laboratorio:
   - Ping: `ping 172.16.20.161`
  .BASELINE

System: - Samba: `\\172.16.20.161\Contabilidad` desde un cliente Windows o `smbclient //172.16.20.161/Contabilidad -U usuario_cont1` desde Linux.
   - Postfix: Configura un cliente de correo (Thunderbird) con IMAP (172.16.20.161, puerto 143, usuario: correo1@empresa.com).

#### Paso 7: Validar la implementación
1. **Conectividad**: Verifica que los clientes en todas las VLANs (100, 200, 300, 400) puedan acceder a Samba y Postfix.
2. **Samba**:
   - Contabilidad: Solo lectura.
   - Desarrollo: Lectura/escritura.
   - Admon: Control total.
3. **Postfix**: Envía correos entre los 10 usuarios (correo1@empresa.com a correo10@empresa.com).
4. **Estabilidad**: Asegura que el hipervisor no presente cuellos de botella (monitorea CPU/RAM en VMware).

### 5.4 Notas para el laboratorio
- **Tiempo**: La instalación y configuración toman aproximadamente 1 hora si el hardware está preparado.
- **Respaldo**: Crea un snapshot de la VM antes de conectarla al laboratorio para facilitar la recuperación en caso de errores.
- **Documentación**: Registra los resultados (capturas de pantalla de VMware, acceso a servicios) para la presentación.

---

## 6. Conclusiones
La virtualización de servidores es una solución poderosa para optimizar recursos, mejorar la escalabilidad y facilitar la gestión de servicios como Samba y Postfix en el proyecto final. La implementación en VMware Workstation permite desplegar un servidor Ubuntu robusto en el laboratorio físico, cumpliendo con los requerimientos de accesibilidad, seguridad y funcionalidad. La guía proporcionada asegura una configuración exitosa el 19 de mayo de 2025.

---

## 7. Anexos
- **Capturas de pantalla**: Configuración de VMware, Ubuntu, Samba, Postfix.
- **Comandos adicionales**:
  - Verificar recursos en Ubuntu: `top`, `df -h`.
  - Monitorear VMware: Usar la interfaz de VMware Workstation.
- **Referencias**:
  - VMware Workstation Documentation.
  - Ubuntu Server Guide.
  - Samba/Postfix official documentation.
