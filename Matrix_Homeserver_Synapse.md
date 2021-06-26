# Matrix Synpase
#Installation de Synpase HomeServeur sur Ubuntu 20.04

## Prérequis
- Ubuntu 18.04 ou plus récent
- Les droits super utilisateur (Root)
- Un nom de domaine

## Sommaire

-  [Étape 1 - Mise à jour du système](#etape1)
-  [Étape 2 - Installation de Synapse](#etape2)
- [Étape 3 - Configuration de Synapse](#etape3)
    - [Configuration du port 8008](#etape3.1)
- [Étape 4 -  Génération des certificats SSL Let's Encrypt](#etape4)
- [Étape 5 - Installation et configuration de Nginx comme reverse proxy](#etape5)
    - [Configuration .well-known](#etape5.1)
- [Étape 6 - Configuration du Pare-Feu UFW](#etape6)
- [Étape 7 - Création d'un utilisateur Matrix](#etape7)
- [Étape 8 - Installation du client Element](#etape8)
    - [Connexion au compte admin sur Element](#etape8.1)
- [Étape 9 - Configuration NAT (Optionnel)](#etape9)

[Source](#source)

[License](#license)




## Étape 1 - Mise à jour du système <a id="etape1"></a>

Pour un maximum de sécurité, connectez-vous à votre serveur Ubuntu et ajoutez la clé de dépôt pour vous assurer que toutes les installations et mises à jour ont été signées par les développeurs et pour empêcher l'installation de tout paquet non autorisé sur votre serveur. 

```bash
sudo apt install -y lsb-release wget apt-transport-https
sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/matrix-org.list
```

Ensuite mettez à jour le dépôt et mettez à niveau tous les packages avec la commande ci-dessous.

```bash
sudo sh -c 'apt update && apt upgrade'
```

## Étape 2 - Installation de Synapse <a id="etape2"></a>

Installez matrix-synapse à l'aide de la commande apt comme ci-dessous. (Si vous souhaitez ne pas répondre «oui» à toutes les questions ajoutez l'option <strong>-y</strong>)

Le nom du paquet est [matrix-synapse-py3](https://matrix.org/docs/guides/installing-synapse), c'est une nouvelle version qui utilise Python3.

```bash
sudo apt install matrix-synapse-py3
```

Pendant l'installation, un nom de serveur matrix va être demandé, c'est ici que vous devez taper votre nom domaine. (Ne pas utiliser de sous domaine, et être bien sûr que le domaine est trouvable par les DNS)

<strong>Ne pas laisser le champs vide et ne vous trompez pas de nom de domaine car ce n'est pas modifiable.</strong>

Une fois l'installation terminée, démarrer le service et autoriser le démarrage automatique après un redémarrage système.

```bash
sudo systemctl start matrix-synapse.service
sudo systemctl enable matrix-synapse.service
```

Le serveur Synpase est maintenant opérationnel et écoute par défaut sur le port <strong>8008</strong>. Vérifiez si le port est bien en écoute à l'aide la commande.

```bash
ss -plntu
```

## Étape 3 - Configuration de Synapse <a id="etape3"></a>

Après l'installation de Synpase, nous devons configurer l'adresse IP Locale du serveur, désactiver l'inscription Synpase et activer la "registration-shared-secret".

Avant de commencer à modifier le fichier de configuration du home serveur, il faut générer la "shared secret key" à l'aide de la commande ci-dessous.

```bash
cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -n 1
```

Maintenant que vous avez votre clé générée, nous allons désactiver l'inscription puis copier la clé dans le fichier de configuration du homeserver. Pour désactiver l'inscription il suffit de supprimer le commentaire de registration_shared_secret. (supprimer le # et ne laissez pas d'espace)

```bash
gedit /etc/matrix-synapse/homeserver.yaml
```

```enable_registration: False```

``` registration_shared_secret: [shared_secred_key] ```

### Configuration du port 8008 <a id="etape3.1"></a>

```bash
gedit /etc/matrix-synapse/homeserver.yaml
```

```bash
- port: 8008
    tls: false
    bind_addresses: ['0.0.0.0']
    type: http
x_forwarded: true
```

```tls: false --> nous allons plus tard configurer un reverse proxy pour sécuriser le trafic entre le client et le reverse proxy en TLS```

```bind_addresses: ['0.0.0.0'] --> autorise toutes les adresses (selon les FAI évite un problème avec la loopback)```

```type: http --> le trafic qui passe par le 8008 est du type HTTP```

```x_forwarded: true --> Implémente X-Forwarded-For en en-tête comme adresse IP du client, utile derrière un reverse proxy.```

Il faut maintenant redémarrer le service Synapse.

```bash
sudo systemctl restart matrix-synapse.service
```

Vérifier à nouveau si le serveur écoute bien sur le port 8008.

```bash
ss -plntu
```

Le service Synpase est démarré sur l'IP Locale. L'installation et la configuration de Synapse sont terminées.

## Étape 4 -  Génération des certificats SSL Let's Encrypt <a id="etape4"></a>

Nous allons activer HTTPS pour le reverse proxy Nginx puis générer les certificats SSL de Let's Encrypt. Pour commencer nous allons installer l'outil [letsencrypt]().

```bash
sudo apt install letsencrypt
```

Ensuite installer le [cerbot]() le plus récent.

```bash
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get install certbot python3-certbot-nginx
```

Enfin nous allons générer les fichiers de certificats SSL pour le nom de domaine du serveur matrix ``homeserver.example`` en utilisant la commande cerbot ci-dessous.

```bash
sudo certbot --nginx
```

L'outil Letsencrypt va générer des fichiers de certificat SSL en exécutant le serveur Web temporairement de manière «standalone» pour une vérification. Une fois terminée, nous verrons où les certificats sont stockés. Les fichiers de certificat SSL pour le nom de domaine ``homeserver.example`` sont générés dans le dossier ``/etc/letsencrypt/live/``.

```bash
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for homeserver.example
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/homeserver.example/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/homeserver.example/privkey.pem
   Your cert will expire on 2019-03-03. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Vérifier que l'auto générateur de certificat est actif car les certificats que sont valables que 90 jours.

```bash
sudo certbot renew --dry-run
```

## Étape 5 - Installation et configuration de Nginx comme reverse proxy <a id="etape5"></a>

Installez le serveur Web Nginx et configurez-le comme reverse proxy pour le homeserver qui écoute sur le port '8008'. Commencez par installer Nginx en utilisant la commande ci-dessous. (Il est possible d'utiliser l'option -y)

```bash
sudo apt install nginx
```

Une fois Nginx installé, démarrer le service et autoriser le démarrage automatique après un redémarrage système.

```bash
sudo systemctl start nginx.service
sudo systemctl enable nginx.service
```

Nous allons créer une nouvelle configuration d'un virtual host pour le nom de domaine du homeserver. Aller dans ``/etc/nginx`` et créer le nouveau fichier virtual host 'matrix'.

```bash
gedit /etc/nginx/sites-available/matrix
```

Collez-y la configuration suivante, en remplaçant le domaine ``maxrasp.ddns.net`` par le vôtre et l'adresse IP Privée ``192.168.1.8`` par la vôtre:

```bash
server {
  listen 80;
  listen [::]:80;
  server_name maxrasp.ddns.net;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  listen [::]:443 ssl;
  server_name maxrasp.ddns.net;

  ssl on;
  ssl_certificate /etc/letsencrypt/live/maxrasp.ddns.net/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/maxrasp.ddns.net/privkey.pem;

  access_log /var/log/nginx/matrix.access.log;
  error_log /var/log/nginx/matrix.error.log;

  location / {
    proxy_pass http://192.168.1.8:8008;
    proxy_set_header X-Forwarded-For $remote_addr;
    client_max_body_size 10M;
  }
}

server {
  listen 8448 ssl default_server;
  listen [::]:8448 ssl default_server;
  server_name maxrasp.ddns.net;

  ssl on;
  ssl_certificate /etc/letsencrypt/live/maxrasp.ddns.net/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/maxrasp.ddns.net/privkey.pem;

  access_log /var/log/nginx/matrix.access.log;
  error_log /var/log/nginx/matrix.error.log;

  location / {
    proxy_pass http://192.168.1.8:8008;
    proxy_set_header X-Forwarded-For $remote_addr;
  }
}
```

Activer le fichier vitual host et tester la configuration.

```bash
sudo ln -s /etc/nginx/sites-available/matrix /etc/nginx/sites-enabled/
```

```bash
sudo nginx -t
```

Si tout est bon, vous devez voir ceci :

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Puis redémarrer le service Nginx

```bash
sudo systemctl restart nginx.service
```

Nous avons terminé l'installation et la configuration du reverse proxy pour le homeserver Synapse.

### Configuration .well-known <a id="etape5.1"></a>

Créer un dossier ``.well-known`` à la racine de votre site web. Puis `matrix`, puis `server` et créer un fichier index.

```bash
mkdir /var/www/html/.well-known
mkdir /var/www/html/.well-known/matrix
mkdir /var/www/html/.well-known/matrix/server
touch /var/www/html/.well-known/matrix/server/index
```

Copier le contenu suivant en remplacant ``synapse.homeserver.example`` par votre nom de domaine.

```bash
{
    "m.server": "synapse.homeserver.example:443"
}
```

Donc, si vous accédez à [https://homeserver.example/.well-known/matrix/server](https://homeserver.example/.well-known/matrix/server), il peut soit télécharger le fichier du serveur soit vous l'affichez directement.

## Étape 6 - Configuration du Pare-Feu UFW<a id="etape6"></a>

Nous n'autoriserons que les connexions SSH, HTTP, HTTPS et 8448 (pour la fédération) sur la configuration du pare-feu UFW. Pour les ajouter à la configuration du pare-feu UFW, exécutez les commandes suivantes.

```bash
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw allow 8448
```

Il faut maintenant activer le Pare-Feu UFW et vérifier si les règles sont bien appliquées.

```bash
sudo ufw enable
sudo ufw status
```

## Étape 7 - Création d'un utilisateur Matrix <a id="etape7"></a>

Pour créer un utilisateur matrix, éxecuter la commande ci-dessous.

```bash
register_new_matrix_user -c /etc/matrix-synapse/homeserver.yaml http://localhost:8008
```

Vous devez maintenant saisir le nom d'utilisateur, le mot de passe et décider si l'utilisateur aura les privilèges d'administrateur ou non.
Le 1er utilisateur créé est le plus souvent un adminstrateur.


```bash
New user Localpart [root]: admin
Password: XXXXX
Confirm password: XXXXX
Make admin [no]: yes
Sending registration requests...
Success
```
## Étape 8 - Installation du client Element <a id="etape8"></a>

Matrix homeserver n'a pas de page d'administration il faut passer par un client par exemple Element.

Pour l'installer : [https://element.io/get-started](https://element.io/get-started)

Une fois le client installé nous pourrons nous connecter à l'aide du compte ``admin`` que nous avons créé.

### Connexion au compte admin sur Element <a id="etape8.1"></a>

Démarrer le client Element, par défaut, il propose une connexion aux serveur Matrix.org, cliquez sur “changer”, indiquer dans la zone “url du serveur d’accueil” :

L’adresse IP du serveur qui héberge l’application Matrix : <strong> http://[adresse IP]:[Port Matrix]</strong>.

Exemple : http://192.168.1.X:8008 quand Matrix écoute sur le port 8008.

Cliquer ensuite sur <strong>Suivant</strong>, puis entrez le compte <strong>admin</strong> avec son mot de passe choisi lors de la création.

Vous êtes maintenant connecté, il ne vous reste plus qu’à créer un Salon en cliquant sur le bouton `+` à côté de salon.

## Étape 9 - Configuration NAT (Optionnel) <a id="etape9"></a>

Il faut sur votre box/routeur créer une règle NAT si votre serveur détient uniquement une adresse IP Privée.

``Port Interne : 443``

``Port Externe : 443``

``Protocol : TCP/UDP``

``Equipement : @IP Privée/nom_DNS_equipement``

``IP Externe : Toutes``

## Source <a id="source"></a>

- https://matrix.org/docs/guides/installing-synapse
- https://www.natrius.eu/dokuwiki/doku.php?id=digital:server:matrixsynapse

## License <a id="license"></a>

[LICENSE](LICENSE_NGINX)