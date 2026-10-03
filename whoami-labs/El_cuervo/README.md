
# >_ [El cuervo]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-03 |

---

## 📝 Descripción
FTP y escalada de privilegios mediante capabilities.

Máquina enfocada en la explotación de un servicio ftp y posterior escalada de privilegios mediante capabilities.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.
Es una máquina Linux y nos responde.

<img width="889" height="159" alt="ping" src="https://github.com/user-attachments/assets/a35efc80-7100-48eb-8c52-183e08938898" />


### Escaneo de Puertos (`nmap`)

Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

**Resultados del escaneo:**
* **Puerto 21/TCP**: FTP
* **Puerto 22/TCP**: SSH

  
```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```
  
<img width="1434" height="266" alt="1Escaneo" src="https://github.com/user-attachments/assets/fdfd9213-7822-4d23-bc90-fbcf696b79fb" />


### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados:

```bash
nmap -p21,22 -sCV 172.17.0.2 -oN targeted
```
<img width="1145" height="598" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/17221a87-e23d-4b88-8fa9-df7c2ba31a3f" />
<br><br>

Tenemos dos servicios, el FTP y el servicio SSH expuesto. En el servicio FTP nos dice que tiene el login de anonymous habilitado por lo que podemos entrar.

---

## 💥 2. Fase de Explotación 

### Vector de Ataque

```bash
ftp anonymous@172.17.0.2
```

<img width="918" height="211" alt="3Ftpanoymous" src="https://github.com/user-attachments/assets/6ce96e65-11c1-49e7-b4b6-7040207855b7" />
<br><br>

Ya estamos dentro como el usuario anonymous, miramos a ver si encontramos algo que nos pueda servir y solo hay un archivo *.backup_config.old*

Nos lo descargamos para mirarlo en nuestra máquina.

```bash
get .backup_config.old
```

<img width="1903" height="185" alt="4getfichero" src="https://github.com/user-attachments/assets/b48aa784-9e05-4291-9121-1c1d2c59c9fc" />
<br><br>

<img width="995" height="116" alt="5usuarioenbackup" src="https://github.com/user-attachments/assets/43cc847f-4df9-4947-a25b-a0bd132c2907" />
<br><br>

Al abrirlo encontramos unas credenciales que vamos a probar para acceder por ssh.

<img width="1191" height="377" alt="6ssh" src="https://github.com/user-attachments/assets/04de0ba5-e026-478b-a582-f3c9d7143671" />
<br><br>

Hemos conseguido acceder como el usuario student dentro de la máquina!
<img width="934" height="103" alt="7escalarajustar" src="https://github.com/user-attachments/assets/f50136ab-72fc-436a-a03c-63a4e29e8125" />

---

## 👑 3. Escalada de Privilegios

Para finalizar la máquina tenemos que escalar a root, ya que allí se encuentra la bandera.
Comenzamos revisando que grupos tenemos y vemos uno sospechoso, docker.
Vamos a buscar todos los archivos y directorios del sistema que pertenecen al grupo docker. Aparece uno, docker.sock


<br><br>
Vemos que con esto, podemos escalar privilegios mediante pertenencia al grupo docker, así que procedemos a montar un docker con ubuntu, por ejemplo, con el comando:

```bash
getcap -r / 2>/dev/null 
```
<img width="571" height="78" alt="8capability" src="https://github.com/user-attachments/assets/4ba7f1cf-a093-443d-8c6b-297f76e31d7c" />
<br><br>



Entramos como root y ya podemos ver la flag

<img width="1053" height="336" alt="9cfcc" src="https://github.com/user-attachments/assets/6d20671c-fe58-423e-8ed6-b46e614711c2" />
<br><br>

Para finalizar, vamos a nuestra terminal y ponemos la flag completa.


<img width="652" height="681" alt="final" src="https://github.com/user-attachments/assets/30980b05-7fe1-4eb1-809e-e30835aa8aad" />

<br><br>


---
