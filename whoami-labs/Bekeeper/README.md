#  >_ Bekeeper



| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoamilabs |
| **Dificultad** | 🟡 Media |
| **OS** | Linux |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-11-11 |

---

📝 Descripción

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante

---


## 🔍 1. Fase de Reconocimiento y Enumeración

### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open --min-rate=5000 -n -Pn 172.17.0.2 -oN allPorts.txt
```

**Resultados del escaneo:**
* **Puerto 21/TCP**:
* **Puerto 25/TCP**:
* **Puerto 80/TCP**: HTTP
* **Puerto 110/TCP**:
* **Puerto 139/TCP**:
* **Puerto 445/TCP**:
* **Puerto 6379/TCP**:
* **Puerto 8888/TCP**:


nmap -p 21,25,80,110,139,445,6379,8888 -sCV 172.17.0.2 -oN targeted



### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:

---

## 💥 2. Fase de Explotación 



---

## 👑 3. Escalada de Privilegios

### Enumeración del Sistema
Analizamos el entorno buscando vectores comunes de escalada (permisos SUID, tareas Cron, capacidades, contraseñas en texto plano).


### Explotación del Vector de Escalada
Encontrado un binario o configuración débil. Explicar cómo se abusa de ello para convertirse en `root`......




---

## 🏁 4. Conclusiones y Mitigación
* **Vulnerabilidad Principal**: Falta de sanitización en los parámetros web de entrada / Binarios con permisos SUDO mal configurados.
* **Remediación**: Actualizar los servicios vulnerables, implementar *whitelisting* en los inputs y aplicar el principio de menor privilegio retirando accesos SUDO innecesarios.





