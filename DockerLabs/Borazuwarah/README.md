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
SUID & Sudo
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
| **22 / TCP** | 🟢 Abierto | SSH | OpenSSH 8.9p1 (Ubuntu Linux) |
| **80 / TCP** | 🟢 Abierto | HTTP | SimpleHTTPServer 0.6 (Python 3.10.12) |

Vemos que tenemos dos sitios web y el puerto ssh pero no conocemos ninguna credencial así que empezamos revisando los sitios web.

### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:


hydra -l borazuwarah -P /usr/share/rockyou.txt ssh://172.17.0.2

sudo -l 
