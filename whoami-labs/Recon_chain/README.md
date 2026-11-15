#  >_ Recon chain



| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | Whoamilabs |
| **Dificultad** | 🟡 Media |
| **OS** | Linux |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-11-11 |

---

📝 Descripción

Máquina enfocada en la explotación de un servicio web vulnerable y posterior escalada de privilegios mediante... Pivoting

---


## 🔍 1. Fase de Reconocimiento y Enumeración

Comenzando realizando un ping a la IP de la máquina objetivo. Descubrimos que es una máquina Linux y nos responde.
<img width="907" height="158" alt="ping" src="https://github.com/user-attachments/assets/1d6a56c1-1780-4589-913e-9fd389078a1d" />



### Escaneo de Puertos (`nmap`)
Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:

```bash
sudo nmap -p- --open --min-rate=5000 -n -Pn 172.17.0.2 -oN allPorts
```

**Resultados del escaneo:**
* **Puerto 21/TCP**:
* **Puerto 22/TCP**:
* **Puerto 25/TCP**:
* **Puerto 80/TCP**: HTTP
* **Puerto 111/TCP**: 
* **Puerto 3128/TCP**: 
* **Puerto 8888/TCP**: 



nmap -p21,22,25,80,111,3128,8888 -sCV -n 172.18.0.2 -oN targeted



### Enumeración Web (Puerto 80)
Al inspeccionar el sitio web, nos encontramos con la siguiente interfaz:



---

## 💥 2. Fase de Explotación 

---

## 👑 3. Escalada de Privilegios
