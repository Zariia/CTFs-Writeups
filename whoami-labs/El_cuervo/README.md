
# >_ [El cuervo]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-03 |

---

## 📝 Descripción
FTP y escalada de privilegios mediante capabilities.

Máquina enfocada en la explotación de un servicio ftp y posterior escalada de privilegios mediante capabilities.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.

<img width="889" height="159" alt="ping" src="https://github.com/user-attachments/assets/a35efc80-7100-48eb-8c52-183e08938898" />


Es una máquina Linux y nos responde.

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 21/TCP**: FTP
* **Puerto 22/TCP**: SSH
  
<img width="1434" height="266" alt="1Escaneo" src="https://github.com/user-attachments/assets/fdfd9213-7822-4d23-bc90-fbcf696b79fb" />


### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p21,22 -sCV 172.17.0.2 -oN targeted
```
<img width="1145" height="598" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/17221a87-e23d-4b88-8fa9-df7c2ba31a3f" />


<br><br>
Tenemos dos servicios, el FTP y el servicio SSH expuesto. ......

---

## 💥 2. Fase de Explotación 

### Vector de Ataque


```bash
chmod 
```

<br><br>

Ya estamos dentro y somos el usuario ....

---

## 👑 3. Escalada de Privilegios

Para finalizar la máquina tenemos que escalar a root, ya que allí se encuentra la bandera.
Comenzamos revisando que grupos tenemos y vemos uno sospechoso, docker.
Vamos a buscar todos los archivos y directorios del sistema que pertenecen al grupo docker. Aparece uno, docker.sock


<br><br>
Vemos que con esto, podemos escalar privilegios mediante pertenencia al grupo docker, así que procedemos a montar un docker con ubuntu, por ejemplo, con el comando:

```bash

```


<br><br>

get .backup_config.old

Entramos como root y ya podemos ver la flag



<br><br>

Para finalizar, vamos a nuestra terminal y ponemos la flag completa.



<br><br>


---
