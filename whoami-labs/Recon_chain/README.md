#  >_ Recon chain



| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoamilabs |
| **Dificultad** | 🟡 Media |
| **OS** | Linux |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-04-10 |

---

📝 Descripción

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante... Pivoting

---


## 🔍 1. Fase de Reconocimiento y Enumeración

Comenzando realizando un ping a la IP de la máquina objetivo. Descubrimos que es una máquina Linux y nos responde.
<img width="907" height="158" alt="ping" src="https://github.com/user-attachments/assets/1d6a56c1-1780-4589-913e-9fd389078a1d" />



### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:


<img width="1413" height="353" alt="1escaneo" src="https://github.com/user-attachments/assets/5e320023-f1e4-48cc-866b-3c2f761c8964" />


```bash
sudo nmap -p- --open -sS --min-rate=5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 21/TCP**: FTP
* **Puerto 22/TCP**: SSH
* **Puerto 25/TCP**: SMTP
* **Puerto 80/TCP**: HTTP
* **Puerto 111/TCP**: RPCBIND
* **Puerto 3128/TCP**: SQUID-HTTP
* **Puerto 8888/TCP**: SUN-ANSWERBOOK


<img width="1905" height="882" alt="2escaneopuertos" src="https://github.com/user-attachments/assets/12bc51cd-f64f-46fd-ba27-6c00053e90e0" />

```bash
nmap -p21,22,25,80,111,3128,8888 -sCV -n 172.18.0.2 -oN targeted
```


### Enumeración Web (Puerto 80 y 8888)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:
<img width="946" height="431" alt="3web" src="https://github.com/user-attachments/assets/2659dad3-f9cf-40e3-abca-098871aa1dca" />

Al no encontrar nada, vamos a mirar la web que hay en el puerto 8888. Tampoco vemos nada, así que vamos a acceder por ftp como el usuario anonymous que descubrimos en la enumeración previa.

<img width="820" height="290" alt="3web8888" src="https://github.com/user-attachments/assets/b463125a-4951-4edb-8f86-6b2738e5f954" />


---

## 💥 2. Fase de Explotación 

<img width="938" height="222" alt="4ftp" src="https://github.com/user-attachments/assets/43ba4e0d-fd7a-4a26-bac1-4b18d9bd5cb3" />




---

## 👑 3. Escalada de Privilegios






<img width="908" height="673" alt="Final" src="https://github.com/user-attachments/assets/6dcb4f8d-ea94-4080-ba70-3ae7a56ffeaa" />
