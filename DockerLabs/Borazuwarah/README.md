# 🐳 [Borazuwarah]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Docker Labs |
| **Dificultad** | 🟢 Muy Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.0.0.2` |
| **Fecha de resolución** | 2026-09-28 |

---

## 📝 Descripción

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP

<img width="1691" height="244" alt="1Escaneo" src="https://github.com/user-attachments/assets/7e39bbe5-faa3-47b5-9546-232bfe09608a" />



*Escaneo profundo de servicios:*
```bash
 nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

| Puerto | Estado | Servicio | Versión |
| :---: | :---: | :--- | :--- |
| **22 / TCP** | 🟢 Abierto | SSH | OpenSSH 9.2p1 (Debian) |
| **80 / TCP** | 🟢 Abierto | HTTP | Apache httpd 2.4.59 |

<img width="1386" height="378" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/c6a1b7e5-43e5-441d-b96c-b8bc99de8c03" />

Vemos que tenemos un sitio web y el puerto ssh pero no conocemos ninguna credencial así que empezamos revisando el sitio web.

### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz que solo contiene una imagen, nos la descargamos.
<img width="685" height="495" alt="3imagen" src="https://github.com/user-attachments/assets/d56ec4c4-6ce3-4647-85dd-b3d17498e054" />

## 💥 2. Fase de Explotación
Vamos a analizar la imagen por si tuviera alguna información oculta, para ello usaremos ExifTool.

ExifTool es una aplicación de línea de comandos gratuita y de código abierto para leer, escribir y editar metadatos en una gran variedad de archivos, como imágenes, videos y documentos.
Es muy sencilla de usar una vez instalada, ponemos el nombre de la herramienta seguida de la imagen que queremos que analice:

```bash
exiftool imagen.jpeg
```
<img width="1029" height="597" alt="3imagenexiftool" src="https://github.com/user-attachments/assets/4a27c4bc-2e1a-42eb-837b-10da8ab75d80" />
En la descripción de la imagen tenemos un usuario: *User: borazuwarah*

Ahora que conocemos un usuaro válido vamos a usar fuerza bruta para sacar su contraseña y poder entrar por el puerto SSH. Para ello usaremos Hydra.

Hydra es una herramienta de código abierto utilizada para realizar ataques de fuerza bruta y de diccionario contra servicios de red y sistemas de autenticación.
Empleamos el diccionario de *rockyou.txt* y enseguida nos saca la contraseña.

```bash
hydra -l borazuwarah -P /usr/share/rockyou.txt ssh://172.17.0.2
```

<img width="1882" height="315" alt="3clavehydra" src="https://github.com/user-attachments/assets/693f115e-dfad-4485-a4cc-c1a9e082e66c" />

Ya tenemos el usuario y la contraseña, así que accedemos por el puerto 22 y estamos dentro.

<img width="1254" height="364" alt="4ssh" src="https://github.com/user-attachments/assets/7b20b68c-25ac-4c33-8e26-cdb643cd8707" />


--- 

## 👑 3. Escalada de Privilegios
#### Enumeración del Sistema
Ahora necesitamos escalar privilegios para ser root. Analizamos el entorno buscando vectores comunes de escalada y en id vemos que pertenecemos al grupo suda. Miramos que comandos podemos lanzar con sudo y podemos lanzar una bash.

```bash
sudo -l
```

<img width="1180" height="231" alt="5escalar" src="https://github.com/user-attachments/assets/0df2b851-0357-481c-ae19-e13dbc2a7726" />

Tras lanzarla ya somos el usuario root y hemos concluido con la máquina Borazuwarah
<img width="493" height="99" alt="6root" src="https://github.com/user-attachments/assets/da317840-a994-4dba-8630-9305a2d182e9" />


