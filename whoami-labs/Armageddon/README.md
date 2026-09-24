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
<img width="1042" height="154" alt="ping" src="https://github.com/user-attachments/assets/6bc7e442-c8f8-48ff-b39c-2c8718a3c1cf" />



### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

<img width="804" height="215" alt="1Escaneo" src="https://github.com/user-attachments/assets/d6731585-3c86-4d86-8fb8-f186c0109e1d" />


Descubrimos abierto solo el puerto 80 con el servicio http.


**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP



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


