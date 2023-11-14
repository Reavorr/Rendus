# Introduction à la cryptographie

## 0. Générer et partager une clé de chiffrement AES256 ainsi que les IV avec le destinataire
![Clé AES et IV](https://cdn.discordapp.com/attachments/1160870725842898969/1173634323530919946/image.png?ex=6564ab23&is=65523623&hm=1f6408567b931da284e0c47c05d8bab45d50f54d6d80233153ef7d2d9297433c&)

## 1. Comment générer une clé de chiffrement de manière sure ? Quel est le risque si les IV sont toujours les mêmes ?
Voici quelques bonnes pratiques pour générer des clés de manière sécurisée :
- Entropie élevée : Utilisez une source d'entropie de haute qualité pour générer vos clés. L'entropie mesure l'imprévisibilité d'une séquence de bits
- Utilisez des bibliothèques cryptographiques : Évitez de générer des clés par vous-même, car cela peut introduire des biais involontaires
- Longueur de clé adéquate : Utilisez des clés suffisamment longues pour résister aux attaques de force brute.
- Renouvelez les clés périodiquement : Même avec une clé forte, il est recommandé de les renouveler périodiquement pour prévenir les attaques à long terme.

Concernant les vecteurs d'initialisation (IV), leur réutilisation peut introduire des vulnérabilités importantes, un IV statique permet à un attaquant d'observer les modèles dans le chiffrement, ce qui peut conduire à des attaques, telles que les attaques par texte clair choisi (chosen plaintext attacks).

## 2. Chiffrer un message et l’envoyer
![Message chiffré](https://cdn.discordapp.com/attachments/1160870725842898969/1173634865313357874/image.png?ex=6564aba4&is=655236a4&hm=5f4c405a95e14516741b29ec1d6427308abff49c359233564dbfdcf649730222&)

## 3. Recevoir et déchiffrer le message
![Message déchiffré](https://cdn.discordapp.com/attachments/1160870725842898969/1173899783463776267/image.png?ex=6565a25e&is=65532d5e&hm=3de572f439945388f31efad5e25978f0dba2db8a626fb3675194d5a3adc140d8&)

## 4. Comment pourrait-on s'assurer de l'intégrité du message ? Ajouter cette fonctionnalité à l'aide d'un script ou d'un outil en CLI.

## 5. Comment pourrait-on s'assurer de l'authenticité de ce message ? Ajouter cette fonctionnalité à l'aide d'un script ou d'un outil en CLI.

## 6. Reproduire l'étape 0 avec un algorithme de chiffrement symétrique "post quantique"