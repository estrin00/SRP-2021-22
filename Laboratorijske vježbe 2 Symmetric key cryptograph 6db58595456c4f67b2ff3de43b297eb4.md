# Laboratorijske vježbe 2: Symmetric key cryptography - a crypto challenge

U sklopu ove vježbe dešifrirali smo odgovarajući ciphertext u kontekstu simetrične kriptografije. Izazov je bio taj što nismo imali pristup enkripcijskom ključu.

Najprije smo kreirali python virtualno okruženje naredbom: 

python -m venv env

Da bismo uopće pokrenuli skriptu, prešli smo u 'env' direktorij i onda ju tek aktivirali; to smo napravili naredbom:

cd env

cd .\ scripts

activate

Zatim smo instalirali python biblioteku 'cryptography' naredbom:

pip install cryptography

Treći korak je bilo otvaranje interaktivnog shella što smo otvorili tako što smo napisali naredbu 'python'.

*Plaintext* koji smo trebali otkriti enkriptiran je korištenjem *high-level* sustava za simetričnu enkripciju iz navedene biblioteke - Fernet. Pa smo pisali sljedeće:

from cryptography.fernet import Fernet

Za generiranje ključa:

key=Fernet.generate_key()

Za inicijalizaciju fernet sustava s dobivenim ključem:

f=Fernet(key)

Unijeli smo plaintext, a zatim enkriptirali naredbom:

ciphertext=f.encrypt(plaintext)

Kako bismo dekriptirali ciphertext koristili smo naredbu:

f.decrypt(ciphertext)

CRYPTO CHALLENGE:
Pristupili smo serveru na kojem su se nalazila imena studenata, tj. imena datoteka dobivena enkriptiranjem. Server: [http://a507-server.local](http://a507-server.local/)

U vlastitom direktoriju kreirali smo skriptu:

code brute_force.p  

gdje smo napisali kod: 

```
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('prezime_ime') + ".encrypted"
```

Zatim smo se vratili u cmd, a naredbom i funkcijom smo postigli da nam se generira hashirano ime. 

python brute_force.py 

if **name** == "**main**":
hash_value = hash("strinic_ela")
print(hash_value)

Sljedeći korak je bio downloadanje vlastite datoteke i spremanje u folder gdje je smješten brute_force.py. 

Za enkripciju smo koristili ključeve ograničene entropije - 22 bita, a koji su generirani pomoću Fernetu. Pošto znamo i plaintext, i ciphertext, trebali smo otkriti key, tj. dekriptirati vlastiti file. To smo napravili pomoću brute forceanja. 

if not(ctr + 1) % 1000:
print(f"[*] Keys tested: {ctr +1:,}", end="\r"

Pretposljednji korak je ispisivanje isprobanih ključeva. U CMDu pokrenemo skriptu i pratimo koliko se ključeva promijenilo.

 Za dekripciju filea, uzeli smo ciphertext iz filea i ubacili smo ga u funkciju.

plaintext = decrypt(key,ciphertext)

Da bismo otkrili je li dobiveni plaintext onaj koji tražimo, nakon dekriptiranja postavljamo if uvjet za provjeru formata slike. Ako je PNG formata, pohranili bismo plaintext i isprintali nađeni key. 

import base64
from cryptography.hazmat.primitives import hashes
from cryptography.fernet import Fernet

def hash(input):
if not isinstance(input, bytes):
input = input.encode()
digest = hashes.Hash(hashes.SHA256())
digest.update(input)
hash = digest.finalize()
return hash.hex()

def test_png(header):
if header.startswith(b"\211PNG\r\n\032\n"):
return True

def brute_force():
# Reading from a file
filename = "9319a3b572e2f5ef8889ac16cad0b2921dacfc62cf84a67cc0df718464911cc0.encrypted"
with open(filename, "rb") as file:
ciphertext = file.read()
# Now do something with the ciphertext
ctr = 0
while True:
key_bytes = ctr.to_bytes(32, "big")
key = base64.urlsafe_b64encode(key_bytes)
if not (ctr + 1) % 1000:
print(f"[*] Keys tested: {ctr +1:,}", end = "\r")
try:
plaintext = Fernet(key).decrypt(ciphertext)
header = plaintext[:32]
if test_png(header):
print(f"[+] KEY FOUND: {key}")
# Writing to a file
with open("BINGO.png", "wb") as file:
file.write(plaintext)
break
except Exception:
pass
# Now initialize the Fernet system with the given key
# and try to decrypt your challenge.
# Think, how do you know that the key tested is the correct key
# (i.e., how do you break out of this infinite loop)?
ctr += 1
if ****name**** == "****main****":
brute_force()