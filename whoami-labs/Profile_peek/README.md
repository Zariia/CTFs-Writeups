
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
Clave Sgfgfupos.

Máquina enfocada en la explotación de  y posteriors.

---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.

<img width="906" height="164" alt="ping" src="https://github.com/user-attachments/assets/30fadd30-1f6a-4233-a163-caa9ebb7da55" />


Es una máquina Linux.

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open  -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 50002/TCP**: upnp
  

<img width="1139" height="208" alt="1Escaneo" src="https://github.com/user-attachments/assets/0374979a-8163-473d-a9a8-76e87e0598aa" />


### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p5000 -sCV 172.17.0.2 -oN targeted
```
<img width="1121" height="832" alt="2Escaneo" src="https://github.com/user-attachments/assets/f70b99cf-ea5e-46ec-8500-0cff793d5825" />
Por lo que muestra, hay una página web así que vamos a verla.




### Enumeración Web (Puerto 5000)
Al acceder al sitio web, nos encontramos con la siguiente página:

<img width="639" height="377" alt="3Pag" src="https://github.com/user-attachments/assets/6e53a436-abbd-4e15-8f13-6c83db3dbbed" />

Es una intranet a la que acceden los empleados, vamos a ir pulsando en todas las opciones que nos aparecen arriba para ver que contienen.

Login
Perfiles
Mi perfil
About




<img width="866" height="776" alt="final" src="https://github.com/user-attachments/assets/b2abf699-2755-4453-813f-670abe63f1bb" />


