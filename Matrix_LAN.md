# Guide Technique d'installation de Matrix homeserver - Synapse :

## Mise à jour du système Ubuntu

Pour s'assurer que les paquets qui vont être installés puis mis à jour sont tous signés par les développeurs, il faut ajouter la clé suivante :

```bash
sudo apt install -y lsb-release wget apt-transport-https
sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/matrix-org.list
```
Il faut ensuite mettre à jour le référentiel et les paquets :

```bash
sudo sh -c 'apt update && apt upgrade'
```

## Installation de Synapse

Lors de l'installation un nom de domaine vous sera demandé veillez à bien le noter, il nous sera utile pour plus tard et surtout <strong> NON MODIFIABLE</strong>.

Installation du paquet [matrix-synapse-py3](https://matrix.org/docs/guides/installing-synapse)

Exemple : homeserver.com
```bash
sudo apt install matrix-synapse-py3
```

Pour démarrer Synapse et le lancer automatiquement au démarrage, taper les commandes suivantes : 

```bash
sudo systemctl start matrix-synapse.services
sudo systemctl enable matrix-synapse.services
```

Vérifier à l'aide de la commande ```ss -plntu``` si le homeserver matrix écoute sur le port ```8008```.

## Installation d'Apache

Installation du paquet [apache2](apt://apache2)

```bash
sudo apt install apache2
```
Pour démarrer Apache et le lancer automatiquement au démarrage, taper les commandes suivantes :

```bash
sudo systemctl start apache2
sudo systemctl enable apache2
```

## Mise à jour des fichiers hosts

Sur votre serveur Ubuntu :

```bash
sudo gedit /etc/hosts
```

Ajouter l'adresse IP de votre serveur Ubuntu.

L'adresse IP doit être <strong>STATIC</strong> voir dans les paramètre réseau en manuel.

Sur votre client (Windows) : Editer le fichier hosts dans ``C:/Windows/System32/drivers/etc`` et ajouter la même ligne que sur le fichier host du serveur Ubuntu.

## Configuration de Synapse (en local)

Génération de la <strong>clé secrète</strong>, conservez-la précieusement dans un fichier texte.

```bash
cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1
```

Configuration du homeserveur : éditer le fichier de configuration

```bash
sudo gedit /etc/matrix-synapse/homeserver.yaml
```

```bash
- port: 8008
    tls: false
    bind adresses ['192.168.1.X'] --> @IP du serveur Ubuntu
    type: http
x_forwarded: true
```

```bash
enable_registration: False
```

```bash
registration_shared_secret: [clé_secrète]
```

Redémarrer ensuite le serveur matrix :

```bash
sudo systemctl restart matrix-synapse.services
```

Si le service redémarre sans erreur la configuration a bien été prise en compte.

Taper dans un navigateur de recherche sur une machine de votre réseau local : ```http://mondomaine.com``` ou ``http://adresse_IP_Ubuntu``

Vous devriez obtenir une page avec le logo Matrix puis la phrase : <strong>It works! Synapse is running</strong>

## Création du 1er compte admin sur le homeserveur Synapse

Accéder au terminal de votre serveur et taper la commande suivante:

```bash
/opt/venvs/matrix-synapse/bin/register_new_matrix_user -c /etc/matrix-synapse/homeserver.yaml http://mondomaine.com:8008
```

```bash
New user Localpart [root]: admin
Password: XXXXX
Confirm password: XXXXX
Make admin [no]: yes
Sending registration requests...
Sucsess
```

## Installation du client Element

Matrix homeserver n'a pas de page d'administration il faut passer par un client par exemple Element.

Pour l'installer : [https://element.io/get-started](https://element.io/get-started)

Une fois le client installé nous pourrons nous connecter à l'aide du compte ``admin`` que nous avons créé.

## Connexion au compte admin sur Element

Démarrer le client Element, par défaut, il propose une connexion aux serveur Matrix.org, cliquez sur “changer”, indiquer dans la zone “url du serveur d’accueil” : 

L’adresse IP du serveur qui héberge l’application Matrix : <strong> http://[adresse IP]:[Port Matrix]</strong>.

Exemple : http://192.168.1.X:8008 quand Matrix écoute sur le port 8008.

Cliquer ensuite sur <strong>Suivant</strong>, puis entrez le compte <strong>admin</strong> avec son mot de passe choisi lors de la création.

Vous êtes maintenant connecté, il ne vous reste plus qu’à créer un Salon en cliquant sur le bouton `+` à côté de salon.

## Source

- https://matrix.org/docs/guides/installing-synapse
- https://www.natrius.eu/dokuwiki/doku.php?id=digital:server:matrixsynapse

## License

[LICENSE](LICENSE)
