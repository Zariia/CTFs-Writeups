# >_ [Red Hat]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoami Labs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Windows  |
| **IP de la Máquina** | `192.168.0.33` |
| **Fecha de resolución** | 2026-10-31 |

---

## 📝 Descripción
Máquina enfocada en la explotación de un servicio SMB vulnerable.

---

## 🔍 1. Fase de Reconocimiento y Enumeración

Primero tenemos que buscar la IP de la máquina virtual.
Escaneamos el rango de IP del adaptador:

```
nmap -sn 192.168.0.0/24
```

Una vez encontramos la IP, le hacemos un ping y vemos que estamos ante una máquina Windows
<img width="726" height="126" alt="2pingwindows" src="https://github.com/user-attachments/assets/3fba42f0-0418-4184-8d13-cfcef1f65937" />



### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
 sudo nmap -sS --min-rate 5000 -p- --open 192.168.0.33 -oN tcp_scan.txt
```

<img width="1132" height="429" alt="3puertos" src="https://github.com/user-attachments/assets/f2bc07bf-cf99-422b-a9b7-941c9c23b585" />
<br><br>

**Resultados del escaneo:**
* **Puerto 135/tcp**: msrpc
* **Puerto 139/tcp**: netbios-ssn
* **Puerto 445/TCP**: microsoft-ds
* **Puerto 554/tcp**: rtsp
* **Puerto 2869/tcp**: icslap
* **Puerto 10243/tcp**: unknown
* **Puerto 49152/tcp**: unknown
* **Puerto 49153/tcp**: unknown
* **Puerto 49154/tcp**: unknown
* **Puerto 49155/tcp**: unknown
* **Puerto 49156/tcp**: unknown
* **Puerto 49158/tcp**: unknown


### Enumeración de servicios
Continuamos con un escaneo más profundo del servicio encontrado:

```bash
nmap -p 135,139,445,554,2869,10243,49152,49153,49154,49155,49156,49158 -sCV -n 192.168.0.33 -oN targeted.txt
```

<img width="1431" height="844" alt="4puertosescaneo" src="https://github.com/user-attachments/assets/38d63b57-646b-48be-9df1-392d616cb20c" />
<br><br>

Tenemos un puerto interesante, el 445 SMB:
**445/tcp   open  microsoft-ds Windows 7 Professional 7600 microsoft-ds (workgroup: WORKGROUP)**


### Enumeración SMB (Puerto 445)

<img width="998" height="485" alt="5smvvuln" src="https://github.com/user-attachments/assets/ca09a643-99f4-46b2-9fa1-cd3280250ed8" />
<br><br>


---

## 💥 2. Fase de Explotación
Explotar MS17-010 (EternalBlue)El reporte indica que el objetivo tiene SMBv1:True y es un Windows 7 sin actualizar. Esto lo hace el candidato perfecto para la vulnerabilidad EternalBlue.Paso con NetExec: Comprueba directamente si es vulnerable usando el módulo específico:bashnxc smb 192.168.0.33 -M ms17-010
Si da positivo (VULNERABLE): Puedes saltar directamente a Metasploit (exploit/windows/smb/ms17_010_eternalblue) para obtener una shell de NT AUTHORITY\SYSTEM.

El invitado está deshabilitado y no vemos nada mas, asique al metasploit

Comenzamos
<img width="1654" height="705" alt="2026-10-26_20-25" src="https://github.com/user-attachments/assets/d48b0b81-7566-4af9-8ac1-36e858934c1a" />
<br><br>


<img width="965" height="882" alt="7" src="https://github.com/user-attachments/assets/6977b33b-9224-4e20-97a4-1e1d5ae37514" />
<br><br>

<img width="595" height="174" alt="8" src="https://github.com/user-attachments/assets/f2c83d34-5bfc-4290-bfb9-3c78992ee346" />
<br><br>

<img width="729" height="294" alt="9" src="https://github.com/user-attachments/assets/ce4772ea-a963-4fe3-92c8-1eeee878aa0b" />
<br><br>

<img width="564" height="411" alt="10" src="https://github.com/user-attachments/assets/670f0408-cab1-4ce2-8981-e831f859f2f1" />


<img width="559" height="522" alt="11" src="https://github.com/user-attachments/assets/90f9b737-01be-4fbb-9ac5-6ebba137904f" />


<img width="548" height="199" alt="12 clavepng" src="https://github.com/user-attachments/assets/6aea97e2-bfd2-49b8-99d3-38cd0f523a1e" />


