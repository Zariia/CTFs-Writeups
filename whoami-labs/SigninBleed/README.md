# >_ [Armageddon]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-26 |

---

## 📝 Descripción
Websploit
Máquina enfocada en la explotación web  para encontrar la flag.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -n -Pn 172.17.0.2 -oN allPorts
```



Descubrimos abierto  el puerto 80 con el servicio http y el puerto 22.

**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP
* * **Puerto 22/TCP**: SSH



### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p22,80 -sCV 172.17.0.2 -oN targeted
```


<br><br>
Aquí descubrimos que nos enfrentamos a u.....

### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con una página.....
