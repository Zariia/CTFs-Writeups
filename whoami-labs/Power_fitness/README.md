# >_ [Power_fitness]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-18 |

---

## 📝 Descripción
Cron

Máquina enfocada en la explotación web para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n 172.17.0.2 -oN allPorts
```

<img width="1155" height="202" alt="1Escaneo" src="https://github.com/user-attachments/assets/a9ed48a8-3a30-4dfb-8aa1-ccdabaf60f77" />

Descubrimos abierto solo el puerto 80 con el servicio http.


**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP



### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p80 -sCV 172.17.0.2 -oN targeted
```


<img width="1108" height="269" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/2a2efacb-9b27-4292-9000-0b280c656e83" />
<br><br>


### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con la siguiente página:
<img width="1635" height="836" alt="3Pagweb" src="https://github.com/user-attachments/assets/a14b05bb-b09a-407f-913e-56db5f9bbdd8" />



Como la página principal no nos proporciona información relevante, vamos a aplicar fuzzing de directorios usando gobuster para buscar rutas adicionales:


```bash
gobuster dir -u http://172.17.0.2/ \
    -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt \
    -t 200 -k -r --no-error
```
<img width="1878" height="574" alt="4gobuster" src="https://github.com/user-attachments/assets/aff88e26-c3c6-4f45-8674-83f14e7ecf0e" />

Cuando finaliza, vemos que encontró cuatro rutas, la más interesante es /backend, entramos para mirar que contiene y vemos que hay un archivo php llamado gym_console.php
Este archivo se ejecuta en la página web y es una consola que ejecuta los comandos que le pasemos pero como el usuario www-data.

<img width="1155" height="945" alt="7consolacatetc" src="https://github.com/user-attachments/assets/5154a1d4-8ea9-4e1f-bf48-d93b981609ba" />

En el archivo passwd solo vemos usuarios pero ninguna clave, así que miramos con sudo -l si podemos realizar



