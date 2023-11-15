# Introduction à la cryptographie

## 1. Générer et partager une clé de chiffrement AES256 ainsi que les IV avec le destinataire
![Clé AES et IV](https://cdn.discordapp.com/attachments/1160870725842898969/1173634323530919946/image.png?ex=6564ab23&is=65523623&hm=1f6408567b931da284e0c47c05d8bab45d50f54d6d80233153ef7d2d9297433c&)

## 2. Comment générer une clé de chiffrement de manière sure ? Quel est le risque si les IV sont toujours les mêmes ?
Voici quelques bonnes pratiques pour générer des clés de manière sécurisée :
- Entropie élevée : Utilisez une source d'entropie de haute qualité pour générer vos clés. L'entropie mesure l'imprévisibilité d'une séquence de bits
- Utilisez des bibliothèques cryptographiques : Évitez de générer des clés par vous-même, car cela peut introduire des biais involontaires
- Longueur de clé adéquate : Utilisez des clés suffisamment longues pour résister aux attaques de force brute.
- Renouvelez les clés périodiquement : Même avec une clé forte, il est recommandé de les renouveler périodiquement pour prévenir les attaques à long terme.

Concernant les vecteurs d'initialisation (IV), leur réutilisation peut introduire des vulnérabilités importantes, un IV statique permet à un attaquant d'observer les modèles dans le chiffrement, ce qui peut conduire à des attaques, telles que les attaques par texte clair choisi (chosen plaintext attacks).

## 3. Chiffrer un message et l’envoyer
![Message chiffré](https://cdn.discordapp.com/attachments/1160870725842898969/1173634865313357874/image.png?ex=6564aba4&is=655236a4&hm=5f4c405a95e14516741b29ec1d6427308abff49c359233564dbfdcf649730222&)

## 4. Recevoir et déchiffrer le message
![Message déchiffré](https://cdn.discordapp.com/attachments/1160870725842898969/1173899783463776267/image.png?ex=6565a25e&is=65532d5e&hm=3de572f439945388f31efad5e25978f0dba2db8a626fb3675194d5a3adc140d8&)

## 5. Comment pourrait-on s'assurer de l'intégrité du message et de l'authenticité du destinataire ? Ajouter cette fonctionnalité à l'aide d'un script ou d'un outil en CLI.
Voici quelques méthodes couramment utilisées pour assurer l'intégrité d'un message :
- Hachage (Hashing) : Utiliser une fonction de hachage comme SHA-256 pour créer une empreinte (hash) du message. Le destinataire peut recalculer la valeur de hachage à partir du message reçu et vérifier si elle correspond à l'empreinte envoyée. Si les empreintes correspondent, le message est intact.
- Signature numérique : Utiliser une signature numérique qui combine une clé privée pour signer le message et une clé publique pour vérifier la signature. Si la signature est valide, cela garantit que le message n'a pas été modifié par des tiers et qu'il provient du titulaire de la clé privée.
- Cryptographie asymétrique : Utiliser un système de cryptographie asymétrique avec une paire de clés (publique et privée). Le message est chiffré avec la clé publique du destinataire et ne peut être déchiffré qu'avec sa clé privée, assurant ainsi que seul le destinataire légitime peut lire le message.

## Exemple de script 
```python
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import serialization, hashes
from cryptography.hazmat.primitives.asymmetric import rsa, padding

def generer_cle_asymetrique():
    private_key = rsa.generate_private_key(
        public_exponent=65537,
        key_size=2048,
        backend=default_backend()
    )
    public_key = private_key.public_key()
    return private_key, public_key

def signer_chiffrer(message, private_key, public_key):
    # Chiffrer le message avec la clé publique
    message_chiffre = public_key.encrypt(
        message,
        padding.OAEP(
            mgf=padding.MGF1(algorithm=hashes.SHA256()),
            algorithm=hashes.SHA256(),
            label=None
        )
    )

    # Signer le message chiffré avec la clé privée
    signature = private_key.sign(
        message_chiffre,
        padding.PSS(
            mgf=padding.MGF1(hashes.SHA256()),
            salt_length=padding.PSS.MAX_LENGTH
        ),
        hashes.SHA256()
    )

    return message_chiffre, signature

# Entrée utilisateur
message_original = input("Entrez votre message : ")

# Générer une paire de clés
private_key, public_key = generer_cle_asymetrique()

# Signer et chiffrer le message
message_original_bytes = message_original.encode()
message_chiffre, signature = signer_chiffrer(message_original_bytes, private_key, public_key)

# Convertir les clés en chaînes de caractères
public_key_str = public_key.public_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PublicFormat.SubjectPublicKeyInfo
).decode()

private_key_str = private_key.private_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PrivateFormat.TraditionalOpenSSL,
    encryption_algorithm=serialization.NoEncryption()
).decode()

# Enregistrer les résultats dans des fichiers à la racine du script
with open("public_key.pem", "wb") as fichier_public_key:
    fichier_public_key.write(public_key.public_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PublicFormat.SubjectPublicKeyInfo
    ))

with open("private_key.pem", "wb") as fichier_private_key:
    fichier_private_key.write(private_key.private_bytes(
        encoding=serialization.Encoding.PEM,
        format=serialization.PrivateFormat.TraditionalOpenSSL,
        encryption_algorithm=serialization.NoEncryption()
    ))

with open("message_chiffre.bin", "wb") as fichier_message_chiffre:
    fichier_message_chiffre.write(message_chiffre)

with open("signature.bin", "wb") as fichier_signature:
    fichier_signature.write(signature)

# Afficher les résultats
print("Message original:", message_original)
print("Message chiffré:", message_chiffre.hex())
print("Clé publique:\n", public_key_str)
print("Clé privée:\n", private_key_str)
print("Signature:", signature.hex())

# Attendre que l'utilisateur appuie sur une touche avant de fermer la fenêtre
input("Appuyez sur Entrée pour quitter...")
```
