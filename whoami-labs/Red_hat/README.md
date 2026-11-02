# >_ [Red Hat]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Windows  |
| **IP de la Máquina** | `192.168.0.33` |
| **Fecha de resolución** | 2026-10-31 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Primero tenemos que buscar la IP de la máquina virtual.
Escaneamos el rango de IP del adaptador:

```
nmap -sn 192.168.0.0/24
```

Una vez encontramos la IP, le hacemos un ping y vemos que estamos ante una máquina Windows
<img width="726" height="126" alt="2pingwindows" src="https://github.com/user-attachments/assets/3fba42f0-0418-4184-8d13-cfcef1f65937" />



### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 sudo nmap -sS --min-rate 5000 -p- --open 192.168.0.33 -oN tcp_scan.txt
```

<img width="1132" height="429" alt="3puertos" src="https://github.com/user-attachments/assets/f2bc07bf-cf99-422b-a9b7-941c9c23b585" />
<br><br>

**Resultados del escaneo:**
* **Puerto 135/tcp**: msrpc
* **Puerto 139/tcp**: netbios-ssn
* **Puerto 445/TCP**: microsoft-ds
* **Puerto 554/tcp**: rtsp
* **Puerto 2869/tcp**: icslap
* **Puerto 10243/tcp**: unknown
* **Puerto 49152/tcp**: unknown
* **Puerto 49153/tcp**: unknown
* **Puerto 49154/tcp**: unknown
* **Puerto 49155/tcp**: unknown
* **Puerto 49156/tcp**: unknown
* **Puerto 49158/tcp**: unknown


### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p80 -sCV -n 192 -oN targeted
```


<br><br>


### Enumeración SMB (Puerto 445)

Al inspeccionar el sitio web, nos encontramos con una página web llamada La Red, contiene una herramienta de diagnóstico en la que nos pide una dirección IP para hacer ping.


---

## 💥 2. Fase de Explotación (Acceso Inicial)

Comenzamos



## 👑 3. Escalada de Privilegios
