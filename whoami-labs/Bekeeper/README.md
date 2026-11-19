#  >_ Bekeeper



| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoamilabs |
| **Dificultad** | 🟡 Media |
| **OS** | Linux |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-11-11 |

---

📝 Descripción

Máquina enfocada en la explotación de un servicio SMB vulnerable y posterior escalada de privilegios.

---


## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios expuestos en la máquina objetivo:

```bash
sudo nmap -p- --open --min-rate=5000 -n -Pn 172.17.0.2 -oN allPorts.txt
```

<img width="1119" height="365" alt="Escaneo puertos abiertos" src="https://github.com/user-attachments/assets/05b12950-c59d-4f37-b5c9-df0905f4bb11" />



**Resultados del escaneo:**
* **Puerto 21/TCP**: ftp
* **Puerto 25/TCP**: smtp
* **Puerto 80/TCP**: http
* **Puerto 110/TCP**:  pop3
* **Puerto 139/TCP**: netbios
* **Puerto 445/TCP**: microsoft-ds
* **Puerto 6379/TCP**: redis
* **Puerto 8888/TCP**: sun-answerbook

Una vez identificados todos los puertos abiertos, realizamos un escaneo más profundo de los servicios encontrados:
```bash
nmap -p 21,25,80,110,139,445,6379,8888 -sCV 172.17.0.2 -oN targeted
```


<img width="1553" height="895" alt="Escaneo de puertos" src="https://github.com/user-attachments/assets/57840bf8-0b6c-4d89-bedb-a767afdd33fd" />


Los resultados obtenidos muestran varios servicios expuestos, entre los que destacan SMB (445) y HTTP (80).
Estos servicios serán los principales objetivos de las siguientes fases de enumeración.


### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:
<img width="1575" height="941" alt="3web" src="https://github.com/user-attachments/assets/79797ab2-2cbf-4a69-89e9-aa1b746d9a0f" />

Tras revisarla no encontramos nada relevante que podamos explotar, así que vamos a mirar el puerto SMB que parece más interesante.


### Enumeración SMB (Puerto 445)

Dado que SMB es uno de los servicios expuestos, comenzamos enumerando los recursos compartidos disponibles en el servidor.

Para ello, utilizamos smbclient e intentamos acceder sin proporcionar credenciales:
```bash
smbclient -L //172.17.0.2 -N
```

<img width="818" height="232" alt="5smb in" src="https://github.com/user-attachments/assets/86236c8f-f39a-4a81-bfc1-c741565a93cf" />


---

## 💥 2. Fase de Explotación 

Vemos que hay cuatro recursos compartidos, vamos a comenzar entrando en backups para ver que copias de seguridad contiene y si hay información que nos sea útil. Una vez dentro listamos su contenido.

```bash
smbclient //172.17.0.2/backups -N
```

<img width="951" height="219" alt="6mio" src="https://github.com/user-attachments/assets/6a38db46-67fa-4cce-b519-954bd6572f36" />


Nos descargamos el archivo encontrado hashes_backup.txt para analizarlo en nuestro ordenador. Contiene dos hashes que podrían permitirnos acceder a la máquina junto al nombre de esos usuarios.


<img width="775" height="76" alt="7hashes" src="https://github.com/user-attachments/assets/d26346a4-6192-475b-a1a7-a55d81806e78" />


---

## 👑 3. Escalada de Privilegios



### Explotación del Vector de Escalada

Vamos a inicia un cliente SMB interactivo en Python utilizando la herramienta smbclient.py de Impacket para conectarnos al servidor remoto Samba usando el usuario(Administrator) y el hash que descubrimos anteriormente.
Al lograr la autenticación, se abre una consola interactiva y tenemos acceso completo al sistema.
<img width="1589" height="582" alt="8impacket" src="https://github.com/user-attachments/assets/61ae977a-36c8-41e8-9724-194eb9ee99c7" />

```bash
python3 /usr/share/doc/python3-impacket/examples/smbclient.py Administrator@172.17.0.2 -hashes :B9D34993351605B1D7B58D6D219F41A7
```

Primero usamos backups pero no podemos ver nada más que lo que vimos previamente, por lo que pasamos a usar C.
```bash
use C$
```

Una vez obtenido el acceso, revisamos los directorios personales de los dos usuarios, bk_service y administrator pero no hay nada en ellos.
<img width="1133" height="279" alt="9nohayna" src="https://github.com/user-attachments/assets/b69ef795-6c5b-4371-a703-960708d3e36b" />


Vamos a entrar a la carpeta personal de root, ya que tenemos acceso completo, dentro descubrimos el fichero flag.txt
<img width="586" height="159" alt="10rootflag" src="https://github.com/user-attachments/assets/d81162fb-13f4-470b-85fd-41ba891ec47c" />


Ponemos la flag encontrada y hemos resuelto la máquina.


<img width="569" height="565" alt="flag" src="https://github.com/user-attachments/assets/7706915a-73a4-4341-bb0c-e455277b2d4b" />



---

## 🏁 4. Conclusiones y Mitigación

Durante la resolución de Bekeeper he trabajado con diferentes técnicas de enumeración y explotación de servicios expuestos, especialmente con SMB y a la gestión de credenciales.

* **Vulnerabilidad Principal**:
  * SMB: exposición de recursos compartidos sin autenticación.
  * Credenciales: presencia de información sensible en archivos accesibles.


* **Remediación**:
  * Restringir el acceso a los recursos compartidos SMB mediante autenticación y permisos adecuados.
  * Evitar almacenar hashes, contraseñas u otra información sensible en directorios compartidos.
  * Revisar periódicamente los servicios expuestos y sus configuraciones.





