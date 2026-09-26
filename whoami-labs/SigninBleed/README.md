# >_ [SigninBleed]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-26 |

---

## 📝 Descripción

Máquina enfocada en la explotación web y uso de Sql Injection para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```

<img width="733" height="243" alt="1Escaneo" src="https://github.com/user-attachments/assets/3b1bcf10-17ea-4547-a60d-da583fce5ed3" />
<br><br>

Descubrimos abierto  el puerto 80 con el servicio http y el puerto 22.

**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP
* **Puerto 22/TCP**: SSH



### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```

<img width="1391" height="654" alt="2Escaneo puertos" src="https://github.com/user-attachments/assets/b59e354d-3d49-4a54-bedd-92c6865e75bf" />

<br><br>

### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con una página web que tiene un login, pide un usuario y una contraseña para poder acceder al portal interno.
Comenzamos probando a entrar dejandolo vacío y rellenando los campos pero siempre nos muestra el mismo error: Credenciales incorrectas.


<img width="1190" height="702" alt="3Web" src="https://github.com/user-attachments/assets/2fffb034-c169-4bb0-8277-ee699101a596" />

Probamos con SQL Injection en el campo del usuario y conseguimos acceder.

<img width="1118" height="644" alt="4sql" src="https://github.com/user-attachments/assets/f77cb3d2-2724-40cd-8ae1-966038ea8e33" />
<br><br>

Al entrar vemos que nos muestra una nota y en ella tenemos la flag, por lo que ya no es necesario realizar una escalada de privilegios ni continuar con la explotación.

<img width="664" height="516" alt="5flagWeb" src="https://github.com/user-attachments/assets/87b38e76-57d3-46ac-baad-41cde3cf9fd5" />
<br><br>

Comprobamos que es la flag de root que nos pide el laboratorio y lo damos por finalizado.
<br><br>
<img width="866" height="807" alt="flagFinal" src="https://github.com/user-attachments/assets/a49d7422-da9c-48c5-bc18-71acc6e6ea6d" />

