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

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n 172.17.0.2 -oN allPorts
```



Descubrimos abierto solo el puerto 80 con el servicio http.


**Resultados del escaneo:**
* **Puerto 80/TCP**: HTTP



### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados::

```bash
nmap -p80 -sCV 172.17.0.2 -oN targeted
```



<br><br>


### Enumeración Web (Puerto 80)
Al acceder al sitio web, nos encontramos con la siguiente página:
