# Laboratorijske vježbe 3 - Message authentication and integrity

Cilj ove vježbe je primjeniti teoretske spoznaje o osnovnim kritografskim mehanizmima za autentikaciju i zaštitu integriteta poruka u praktičnom primjerima. Pri tome smo koristili simetrične i asimetrične kripto mehanizme: *message authentication code (MAC)* i *digitalne potpise* zasnovane na javnim ključevima.

### Izazov 1:

Otvarili smo python virtualno okruženje naredbom:

`python -m venv <ime>`

Pozicionirali smo se u željeni direktorij i pokrenuli skriptu.

`cd <ime> ; cd .\Scripts\ ; .\activate`

Zatim se vraćamo u svoje virtualno okruženje i naredbom *code .* otvaramo vs code.

Cilj nam je implementacija zaštite integriteta sadržaja dane poruke primjenom odgovarajućeg MAC algoritma. Koristili smo HMAC mehanizam iz Python biblioteke cryptography.

Kreirali smo tekstualnu datoteku imena "message.txt" i u nju pohranili sadržaj čiji smo integritet željeli zaštititi.

Na temelju pročitanog sadržaja iz datoteke i proizvoljno unesenog ključa generirali smo MAC pomoću funkcije "generate_MAC" te smo ga spremili u file imena "message.mac".

U funkciji "verify_MAC" usporedili smo novi MAC s onim koji smo poslali kao argument funkcije

`from cryptography.hazmat.primitives import hashes, hmac
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    signature = h.finalize()
    return signature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":
    key = b"my secret password"
    with open("message.txt", "rb") as file:
        content = file.read()   

    # mac = generate_MAC(key, content)

    # with open("message.mac", "wb") as file:
    #     file.write(mac)
		with open("message.mac", "rb") as file:
	       mac = file.read()

    is_authentic = verify_MAC(key, mac, content)

    print(is_authentic)`

Vrijednost varijable *is_authentic* bit će true. Ako promijenimo poruku (ono što piše u datoteci "message.txt") ili signature, vrijednost će biti false.

**Izazov 2:**

Cilj ovog izazova bilo je utvrditi vremenski ispravnu sekvenciju transakcija sa odgovarajućim dionicama. Nalozi za pojedine transakcije nalazili su se lokalnom web poslužitelju: [http://a507-server.local](http://a507-server.local/).

Pomoću GNU Wget softvera preuzeli smo potrebne file-ove s poslužitelja.

`if __name__ == "__main__":
key = "strinic_ela".encode()
# with open("./challenges/strinic_ela/mac_challenge/order_2.txt", "rb") as file:
#     content = file.read()
# with open("./challenges/strinic_ela/mac_challenge/order_2.sig", "rb") as file:
#     mac = file.read()
for ctr in range(1, 11):
	msg_filename = f"./challenges/strinic_ela/mac_challenge/order_{ctr}.txt"
	sig_filename = f"./challenges/strinic_ela/mac_challenge/order_{ctr}.sig"
	with open(msg_filename, "rb") as file:
		content = file.read()
	with open(sig_filename, "rb") as file:
		mac = file.read()
	is_authentic = verify_MAC(key, mac, content)
	print(is_authentic)
	print(f'Message {key.decode():>45} {"OK" if is_authentic else "NOK":<6}')`