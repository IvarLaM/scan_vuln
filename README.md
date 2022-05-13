## Scanner Vuln


### This script after running scan continuously my home network, retrieve the services running and search for vulnerabilities associated with the searchsploit command


### Dependencies:
```bash
sudo apt install nmap
```
```bash
sudo apt install exploitdb
```

### Usage:
```bash
./scan.sh
```

### Source Code:

```bash
#!/bin/bash

scan_ini(){

	touch nmap-ini-`date +%Y-%m-%d-%H-%M`.txt
	nmap -sn 192.168.1.0/24 | sed '/Host/d' | sed '/MAC/d' | sed '/done/d' | cut -d " " -f 6 | sed -e 's/[()]//g' | grep -v "^$" > nmap-ini-`date +%Y-%m-%d-%H-%M`.txt
}

scan(){

	touch nmap-`date +%Y-%m-%d-%H`.txt
	nmap -sn 192.168.1.0/24 | sed '/Host/d' | sed '/MAC/d' | sed '/done/d' | cut -d " " -f 6 | sed -e 's/[()]//g' | grep -v "^$" > nmap-`date +%Y-%m-%d-%H-%M`.txt

}

searchsploit(){

	nmap -A -iL nmap-`date +%Y-%m-%d-%H-%M`.txt | grep -v '^|' | grep '^[0-9]' | awk '{print $4}' | grep -v "^$" | while read line; do searchsploit "$line " 2>/dev/null | sed '/No Results/d' | grep $line; echo "$line"; done
	rm nmap*
}

scan_ini
dif=''
dif=''

while true; do

	scan_ini
	dif=$(wc -l /home/lucho/Documents/Script/Bash/nmap-ini-`date +%Y-%m-%d-%H-%M`.txt | awk '{print $1;}' | head -n 1)
	scan
	sleep 5
	dif2=$(wc -l /home/lucho/Documents/Script/Bash/nmap-`date +%Y-%m-%d-%H-%M`.txt | awk '{print $1;}' | head -n 1)

if [[ $dif -ge $dif2 ]]; then

	: No new device
else

	searchsploit
fi

done

```