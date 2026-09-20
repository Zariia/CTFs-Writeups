
# >_ [Profile Peek]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-16 |

---

## 📝 Descripción
Web Exploit

Máquina enfocada en la explotación web para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.

<img width="906" height="164" alt="ping" src="https://github.com/user-attachments/assets/27eddb6c-3e3e-4091-878d-75060cf5f14a" />



Es una máquina Linux.

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open  -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 50002/TCP**: upnp
  

<img width="1139" height="208" alt="1Escaneo" src="https://github.com/user-attachments/assets/a6beb018-60a5-4259-b148-77a087bcdc26" />




### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrados:

```bash
nmap -p5000 -sCV 172.17.0.2 -oN targeted
```
<img width="1121" height="832" alt="2Escaneo" src="https://github.com/user-attachments/assets/cbf1d6e3-544c-49d4-bd23-a22c9ab638ad" />


Por lo que muestra, hay una página web así que vamos a verla.




### Enumeración Web (Puerto 5000)
Al acceder al sitio web, nos encontramos con la siguiente página:

<img width="639" height="377" alt="3Pag" src="https://github.com/user-attachments/assets/6e53a436-abbd-4e15-8f13-6c83db3dbbed" />

Es una intranet a la que acceden los empleados, vamos a ir pulsando en todas las opciones que nos aparecen arriba para ver que contienen.

Entramos a Login y vemos un panel para autentificarse pero denajo del botón de enviar está el usuario y la contraseña de una cuenta demo llamada alice.<br><br>
<img width="656" height="429" alt="4logins" src="https://github.com/user-attachments/assets/5046736b-c903-431e-9667-6f516fa02161" />


Continuamos con la siguiente opción que es Perfiles, aquí sale el uid, usuario y nombre de 20 empleados.<br><br>
<img width="789" height="933" alt="5profile" src="https://github.com/user-attachments/assets/27573df5-1423-4311-a91f-99a83696c3ec" />

En Mi perfil muestra los datos del empleado seleccionado y quién tiene la sesión actual.<br><br>
<img width="760" height="547" alt="7alice" src="https://github.com/user-attachments/assets/9ccd9e3b-1778-46ef-b817-801ae04e4b83" />


En About, nos dice lo que es y los endpoints que existen, que son todas las pestañas que hemos visto anteriormente.<br><br>
<img width="729" height="388" alt="6about" src="https://github.com/user-attachments/assets/398b7304-c5aa-4553-929b-73974446ad05" />


Una vez visto todo, vamos a logeamos como alice ya que nos dejan su usuario y contraseña al ser la cuenta demo. 
Pasamos de ser un usuario anónimo con uid -1 a ser alice con uid 1.


<img width="665" height="441" alt="8alice" src="https://github.com/user-attachments/assets/e5eb7026-7bc2-479e-a4b5-a78d58f5d465" />


En la lista de perfiles busco alguno que sea administrador y encuentro que el número 17 podría serlo, miramos su perfil y encontramos la flag!!

<img width="974" height="457" alt="9fin" src="https://github.com/user-attachments/assets/75c20c4d-5044-4c73-b9f9-115e07445027" />


Vamos a verificar que sea la que nos piden y efectivamente es esta.


<img width="866" height="776" alt="final" src="https://github.com/user-attachments/assets/b2abf699-2755-4453-813f-670abe63f1bb" />


