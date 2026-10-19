# >_ [Rascacielos]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami-labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-10-18 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante abuso de permisos SUDO.

---

## 🔍 1. Fase de Reconocimiento y Enumeración


### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 22/TCP**: SSH
* **Puerto 80/TCP**: HTTP (Apache)

<img width="982" height="221" alt="1Escaneo" src="https://github.com/user-attachments/assets/ea744281-009e-4843-93b5-8ddea0cb16d7" />
<br><br>


### Enumeración de servicios
Continuamos con un escaneo más profundo de los servicios encontrados:

```bash
nmap -p 22,80 -sCV -n 172.17.0.2 -oN targeted
```

<img width="951" height="346" alt="2Escaneopuertos" src="https://github.com/user-attachments/assets/d81bb049-e791-48ee-ad82-df94946ff8b8" />
<br><br>


### Enumeración Web (Puerto 80)

Al inspeccionar el sitio web, nos encontramos con la página  por defecto de Apache sin nada relevante, por lo que vamos a realizar fuzzing de directorios para buscar rutas ocultas.

```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/rockyou.txt  -x php,bak,html -t 100 -k -r
```

<img width="1277" height="631" alt="3gobuster" src="https://github.com/user-attachments/assets/1ca9a452-1f77-4501-ae11-1745c2afef70" />
<br><br>

Nos encuentra varias rutas, mientras sigue buscando más, vamos a ver index.php ya que aquí si que hay una web de Rascacielos Corp. 

<img width="1656" height="921" alt="4indexphp" src="https://github.com/user-attachments/assets/7cc90996-7ad1-41e0-bbd4-5bf4b43fcbdd" />
<br><br>

Miramos también footer.php por si hubiera algo, pero no hay nada relevante.
<img width="697" height="305" alt="5 skylinusuario" src="https://github.com/user-attachments/assets/c69ac383-71e8-4644-94ca-de09bd8d3e12" />
<br><br>



---

## 💥 2. Fase de Explotación (Acceso Inicial)

Al ir moviendonos por /index.php vemos que en la url se va añadiendo ?page=algo, podemos probar.....

### Vector de Ataque






## 👑 3. Escalada de Privilegios

### Enumeración del Sistema

```bash
# Comprobamos privilegios de SUDO
sudo -l
```
---

### Explotación del Vector de Escalada
