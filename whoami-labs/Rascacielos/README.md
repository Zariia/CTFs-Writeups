# >_ [Rascacielos]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami-labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-18 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración


### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 nmap -p- --open -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP (Apache)

<img width="982" height="221" alt="1Escaneo" src="https://github.com/user-attachments/assets/ea744281-009e-4843-93b5-8ddea0cb16d7" />





### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p 22,80 -sCV -n 172.17.0.2
```

<img width="866" height="374" alt="2escaneopuertos" src="https://github.com/user-attachments/assets/b1d3344c-f1f4-46bf-864d-81eb6f852257" />

<br><br>


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con una página 
<img width="1092" height="746" alt="3web" src="https://github.com/user-attachments/assets/9df2629f-f566-4189-bc5a-182fb6295750" />
<br><br>

Hay un nombre de usuario en un comentario del código html de la web, nos lo guardamos y vamos a realizar enumeraremos con gobuster.
<img width="1091" height="725" alt="4" src="https://github.com/user-attachments/assets/99128df5-83ee-46a9-98c5-4031be3f4a73" />
<br><br>

** Username: R1ckRul3s**


Aplicamos fuzzing de directorios para buscar rutas ocultas:

```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/seclists/Discovery/Web-Content/common.txt  -x php,bak,html
```



<br><br>

---

## 💥 2. Fase de Explotación (Acceso Inicial)

### Vector de Ataque






## 👑 3. Escalada de Privilegios

### Enumeración del Sistema

```bash
# Comprobamos privilegios de SUDO
sudo -l
```
---

### Explotación del Vector de Escalada
