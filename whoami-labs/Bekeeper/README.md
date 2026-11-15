sudo nmap -p- --open --min-rate=5000 -n -Pn 172.17.0.2 -oN allPorts.txt

nmap -p 21,25,80,110,139,445,6379,8888 -sCV 172.17.0.2 -oN targeted
