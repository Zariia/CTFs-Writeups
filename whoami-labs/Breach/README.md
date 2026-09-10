
# >_ [Breach]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.0.0.2` |
| **Fecha de resolución** | 2026-09-10 |

---

## 📝 Descripción
SUID & Sudo
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante x.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Hacemos un ping a la máquina para ver a qué nos enfrentaremos.

<img width="883" height="154" alt="ping" src="https://github.com/user-attachments/assets/e240499e-55c9-4c11-828b-61c5b77667d3" />

Es una máquina Linux.


Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP
  
<img width="1070" height="229" alt="1Escaneo_inicial" src="https://github.com/user-attachments/assets/58a550b2-39d5-49ad-bd09-da9f905fa195" />


Continuamos con un escaneo profundo de servicios:

```bash
sudo nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

<img width="1066" height="390" alt="2EscaneoPuertos" src="https://github.com/user-attachments/assets/4e7b97d4-ed72-46a7-a71a-b1c9037aa3d3" />

Vemos que tenemos un sitio web y el puerto ssh pero no conocemos ninguna credencial así que empezamos revisando el sitio web.


### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente página:

<img width="1638" height="980" alt="4Web" src="https://github.com/user-attachments/assets/d9e357e0-7da2-4603-b83b-f775e6674533" />



Aplicamos fuzzing de directorios usando gobuster para buscar rutas ocultas, ya que la inicial no nos muestra nada útil:

<img width="1689" height="432" alt="3Escaneo" src="https://github.com/user-attachments/assets/35814cd7-3748-4319-86a9-41f921fb6b43" />


Le damos tiempo para ver si encuentra algo más y mientras vamos a mirar las dos que nos ha enumerado: 
home.php y services.php pero no encontramos nada útil en ellas

<img width="1550" height="727" alt="5WebHome" src="https://github.com/user-attachments/assets/7be350de-a93d-4754-9303-3ed92e916043" />


<img width="812" height="302" alt="6WebServices" src="https://github.com/user-attachments/assets/b0c6eb94-30d7-435e-84f2-a308e9621059" />

Gobuster encontró un par de rutas más, una de ella si que contiene información útil, entramos a internal y vemos que tiene....


<img width="707" height="232" alt="6WebInternal" src="https://github.com/user-attachments/assets/fa6441d5-cb2f-4c38-b77c-ad34b76fa5c9" />

Cuando pulsamos...

<img width="558" height="785" alt="7Privatekey" src="https://github.com/user-attachments/assets/47700c59-1ebe-424b-bde4-8232b6382ba6" />






