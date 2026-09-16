
# >_ [Breach]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-10 |

---

## 📝 Descripción
Clave SSH y escalada de privilegios mediante grupos.

Máquina enfocada en la explotación de una clave id_rsa y posterior escalada de privilegios mediante la pertenencia a un grupo con permisos excesivos.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.

<img width="883" height="154" alt="ping" src="https://github.com/user-attachments/assets/e240499e-55c9-4c11-828b-61c5b77667d3" />

Es una máquina Linux.

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP
  
<img width="1070" height="229" alt="1Escaneo_inicial" src="https://github.com/user-attachments/assets/58a550b2-39d5-49ad-bd09-da9f905fa195" />

### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

<img width="1066" height="390" alt="2EscaneoPuertos" src="https://github.com/user-attachments/assets/4e7b97d4-ed72-46a7-a71a-b1c9037aa3d3" />
<br><br>
Tenemos un servidor web y el servicio SSH expuesto. Sin embargo, como todavía no conocemos ninguna credencial válida para acceder mediante SSH, comenzamos enumerando el servicio web.


### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con la siguiente página:

<img width="1638" height="980" alt="4Web" src="https://github.com/user-attachments/assets/d9e357e0-7da2-4603-b83b-f775e6674533" />

Como la página principal no nos proporciona información relevante, vamos a aplicar fuzzing de directorios usando gobuster para buscar rutas adicionales:


```bash
gobuster dir -u http://172.17.0.2/ \
    -w /usr/share/rockyou.txt \
    -x php,txt,html -t 100 -k -r -q --no-error
```

<img width="1689" height="432" alt="3Escaneo" src="https://github.com/user-attachments/assets/35814cd7-3748-4319-86a9-41f921fb6b43" />

<br><br>
Le damos tiempo para ver si encuentra algo más y mientras vamos a mirar las dos que nos ha enumerado: 

home.php y services.php 


Accedemos a ambas pero no encontramos nada útil en ellas que nos permita avanzar en la explotación.

<img width="1550" height="727" alt="5WebHome" src="https://github.com/user-attachments/assets/7be350de-a93d-4754-9303-3ed92e916043" />

<br><br>

<img width="812" height="302" alt="6WebServices" src="https://github.com/user-attachments/assets/b0c6eb94-30d7-435e-84f2-a308e9621059" />
<br><br>

Una vez finalizado el escaneo, Gobuster encontró un par de rutas más, about e internal. En internal vemos que si contiene información interesante.

---

## 💥 2. Fase de Explotación 

### Vector de Ataque
En la enumeración web, descubrimos un directorio interno llamado internal que contiene un id_rsa.


<img width="707" height="232" alt="6WebInternal" src="https://github.com/user-attachments/assets/fa6441d5-cb2f-4c38-b77c-ad34b76fa5c9" />
<br><br>
Pulsamos en el enlace y nos aparece el id_rsa que es una clave privada para openssh.

<img width="558" height="785" alt="7Privatekey" src="https://github.com/user-attachments/assets/47700c59-1ebe-424b-bde4-8232b6382ba6" />
<br><br>
Copiamos la clave y la guardamos en un archivo que llamaremos id_rsa, después le damos permisos.

```bash
chmod 600 id_rsa
```

En internal vimos que ponía "devops backup key" asi que probamos a conectarnos por el puerto 22 con el usuario devops y con el id_rsa que acabamos de obtener.

<img width="1070" height="357" alt="8sshDentro" src="https://github.com/user-attachments/assets/136f6680-e730-4b4f-b969-dbf62749731f" />
<br><br>

Ya estamos dentro y somos el usuario devops.

---

## 👑 3. Escalada de Privilegios

Para finalizar la máquina tenemos que escalar a root, ya que allí se encuentra la bandera.
Comenzamos revisando que grupos tenemos y vemos uno sospechoso, docker.
Vamos a buscar todos los archivos y directorios del sistema que pertenecen al grupo docker. Aparece uno, docker.sock

<img width="865" height="87" alt="9previaMontamos" src="https://github.com/user-attachments/assets/f032e3da-5419-4260-8493-b2babb462659" />
<br><br>
Vemos que con esto, podemos escalar privilegios mediante pertenencia al grupo docker, así que procedemos a montar un docker con ubuntu, por ejemplo, con el comando:

```bash
docker run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```

<img width="865" height="256" alt="9Montamos" src="https://github.com/user-attachments/assets/bf8463e7-dfcc-441c-adbd-3186a1d5cee9" />
<br><br>


Al hacer esto, la terminal engaña al sistema y pasa a controlar los archivos reales de la máquina víctima, no del contenedor, y así pasamos a ser el usuario root.

Entramos como root y ya podemos ver la flag


<img width="918" height="378" alt="10Fin" src="https://github.com/user-attachments/assets/e798c51a-eb56-42fc-9a69-97dc1403685c" />
<br><br>

Para finalizar, vamos a nuestra terminal y ponemos la flag completa.

<img width="970" height="821" alt="FinnnMio" src="https://github.com/user-attachments/assets/054c810f-8a88-47c7-a480-e067a786090f" />

<br><br>


---



