# Bridge Matrix Telegram

## Prérequis
- Ubuntu 18.04 ou plus récent
- Un nom de domaine public
- Les droits super utilisateur (Root)
- Un homeserver Synapse installé et configuré
- **Python 3.7** ou supérieur avec **pip** et **virtualenv**.

## Sommaire

-  [Étape 1 - Création du Bridge Telegram](#etape1)
-  [Étape 2 - Téléchargement des fichiers du Bridge Telegram](#etape2)
-  [Étape 3 - Enregistrement sur l'API de Telegram](#etape3)
-  [Étape 4 - Enregistrement du Bot Telegram](#etape4)
-  [Étape 5 - Configuration du Bridge Matrix Telegram](#etape5)
-  [Étape 6 - Démarrage du Bridge Telegram Matrix](#etape6)
-  [Étape 7 - Invitation du Bot dans Telegram et Element](#etape7)
    - [Sur Telegram](#etape7.1)
    - [Sur Element](#etape7.2)
  
[Source](#source)




## Étape 1 - Création du Bridge Telegram <a id="etape1"></a>

Créer un dossier matrix-telegram pour stocker le bridge Matrix Telegram sous root
```bash
cd /etc/matrix-synapse/
mkdir matrix-telegram
cd matrix-telegram
```

Ensuite installer le virtual environment à l'aide de virtualenv.
Placez-vous dans le dossier matrix-telegram.
Créer le virtualenv dans le dossier matrix-telegram puis activer le virtualenv 

```bash
cd /etc/matrix-synapse/matrix-telegram
virtualenv -p /usr/bin/python3 .    (ne pas oublier le point à la fin)
source ./bin/activate
```
Conseil : Tout au long du déploiement du bridge gardé ce terminal uniquement pour les commandes de la mise en place du bridge.
## Étape 2 - Téléchargement des fichiers du Bridge Telegram<a id="etape2"></a>

Lancement du téléchargement et de l'installation : 

```bash
cd /etc/matrix-synapse/matrix-telegram
pip install --upgrade mautrix-telegram[all]
```

Si lors du téléchargement et de l'installation du Bridge Telegram des erreurs surviennent, vérifier que les paquets suivants sont bien installés sur votre serveur Linux :
- libolm-dev
- python3-dev
- build-essential

Exemple d'erreur :
```bash
  Building wheel for python-olm (setup.py) ... error
    ERROR: Command errored out with exit status 1:
   command: /usr/bin/python3 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"'; __file__='"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' bdist_wheel -d /tmp/pip-wheel-8hbkxc9o
       cwd: /tmp/pip-install-qf63kod0/python-olm/
  Complete output (29 lines):
  make: ***  Aucune règle pour fabriquer la cible « ../include/olm/olm.h », nécessaire pour « include/olm/olm.h ». Arrêt.
  running bdist_wheel
  running build
  running build_py
  creating build
  creating build/lib.linux-x86_64-3.8
  creating build/lib.linux-x86_64-3.8/olm
  copying olm/_finalize.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/account.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/_compat.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/utility.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/session.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/pk.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/__init__.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/__version__.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/group_session.py -> build/lib.linux-x86_64-3.8/olm
  copying olm/sas.py -> build/lib.linux-x86_64-3.8/olm
  running build_ext
  generating cffi module 'build/temp.linux-x86_64-3.8/_libolm.c'
  creating build/temp.linux-x86_64-3.8
  building '_libolm' extension
  creating build/temp.linux-x86_64-3.8/build
  creating build/temp.linux-x86_64-3.8/build/temp.linux-x86_64-3.8
  x86_64-linux-gnu-gcc -pthread -Wno-unused-result -Wsign-compare -DNDEBUG -g -fwrapv -O2 -Wall -g -fstack-protector-strong -Wformat -Werror=format-security -g -fwrapv -O2 -g -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -I/usr/include/python3.8 -c build/temp.linux-x86_64-3.8/_libolm.c -o build/temp.linux-x86_64-3.8/build/temp.linux-x86_64-3.8/_libolm.o -I../include
  build/temp.linux-x86_64-3.8/_libolm.c:570:18: fatal error: olm/olm.h: Aucun fichier ou dossier de ce type
    570 |         #include <olm/olm.h>
        |                  ^~~~~~~~~~~
  compilation terminated.
  error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
  ----------------------------------------
  ERROR: Failed building wheel for python-olm
```
```bash
  Running setup.py install for python-olm ... error
      ERROR: Command errored out with exit status 1:
     command: /usr/bin/python3 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"'; __file__='"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' install --record /tmp/pip-record-to80sl53/install-record.txt --single-version-externally-managed --compile --install-headers /usr/local/include/python3.8/python-olm
         cwd: /tmp/pip-install-qf63kod0/python-olm/
    Complete output (29 lines):
    make: ***  Aucune règle pour fabriquer la cible « ../include/olm/olm.h », nécessaire pour « include/olm/olm.h ». Arrêt.
    running install
    running build
    running build_py
    creating build
    creating build/lib.linux-x86_64-3.8
    creating build/lib.linux-x86_64-3.8/olm
    copying olm/_finalize.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/account.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/_compat.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/utility.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/session.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/pk.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/__init__.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/__version__.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/group_session.py -> build/lib.linux-x86_64-3.8/olm
    copying olm/sas.py -> build/lib.linux-x86_64-3.8/olm
    running build_ext
    generating cffi module 'build/temp.linux-x86_64-3.8/_libolm.c'
    creating build/temp.linux-x86_64-3.8
    building '_libolm' extension
    creating build/temp.linux-x86_64-3.8/build
    creating build/temp.linux-x86_64-3.8/build/temp.linux-x86_64-3.8
    x86_64-linux-gnu-gcc -pthread -Wno-unused-result -Wsign-compare -DNDEBUG -g -fwrapv -O2 -Wall -g -fstack-protector-strong -Wformat -Werror=format-security -g -fwrapv -O2 -g -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -fPIC -I/usr/include/python3.8 -c build/temp.linux-x86_64-3.8/_libolm.c -o build/temp.linux-x86_64-3.8/build/temp.linux-x86_64-3.8/_libolm.o -I../include
    build/temp.linux-x86_64-3.8/_libolm.c:570:18: fatal error: olm/olm.h: Aucun fichier ou dossier de ce type
      570 |         #include <olm/olm.h>
          |                  ^~~~~~~~~~~
    compilation terminated.
    error: command 'x86_64-linux-gnu-gcc' failed with exit status 1
    ----------------------------------------
ERROR: Command errored out with exit status 1: /usr/bin/python3 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"'; __file__='"'"'/tmp/pip-install-qf63kod0/python-olm/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' install --record /tmp/pip-record-to80sl53/install-record.txt --single-version-externally-managed --compile --install-headers /usr/local/include/python3.8/python-olm Check the logs for full command output.
```

## Étape 3 - Enregistrement sur l'API de Telegram <a id="etape3"></a>

Au préalable, créer un compte Telegram sur votre téléphone avec un numéro de mobile valide

Rendez-vous sur le lien suivant pour accéder à l'API [Telegram](https://my.telegram.org/auth?to=apps)

Puis renseigner le numéro de mobile que vous avez utilisé lors de votre inscription sur Telegram

Vous allez ensuite recevoir un code sur l'application Telegram, il faut le renseigner dans la case Confirmation Code

Ensuite dans App title et Short name renseigner provisioning, ne pas renseigner d'adresse URL puis choisir une plateforme (par défaut Android).

Enfin cliquer sur Create application

Récupérer et stocker votre **api_id** et **api_hash** qui devront être renseigné dans le fichier de configuration du bridge

## Étape 4 - Enregistrement du Bot Telegram<a id="etape4"></a>

Rendez-vous sur l'application Mobile ou Desktop Telegram, connectez-vous à votre compte précédemment créé et commencer une discussion avec @BotFather

Dans la discussion avec @BotFather taper ```/start```

Puis ```/newbot``` pour créer un bot

Saisir le nom du bot ```example_bot```

Récupérer le token qui nous servira lors de la configuration.

Puis régler les paramètres de confidentialités à ***Disable*** pour permettre au bot d'accéder aux discussions des groupes auxquels il appartient :

```bash
/setprivacy
@example_bot
Disable
```
Rendez-vous sur le lien que BotFather vous a donné dans son ancien message [t.me/example_bot]()

Puis dans la discussion avec votre nouveau bot, taper la commande ```/start```

Vous venez de créer votre 1er Bot Telegram et de le lancer.

## Étape 5 - Configuration du Bridge Matrix Telegram <a id="etape5"></a>

Rendez-vous dans le dossier matrix-telegram où se trouve les fichiers du Bridge Matrix Telegram

Copier le fichier example-config.yaml en config.yaml

Nous allons maintenant modifier le fichier de configuration pour mettre en place le bridge.

```bash
cd /etc/matrix-synapse/matrix-telegram
cp example-config.yaml config.yaml
gedit config.yaml &
```

```bash
homeserver:
    # The address that this appservice can use to connect to the homeserver.
    address: https://adresse_du_homeserver.com
    # The domain of the homeserver (for MXIDs, etc).
    domain: nom_de_domaine_homeserver
appservice:
    address: http://localhost:29317
    port: 29317
    # Public part of web server for out-of-Matrix interaction with the bridge.
    # Used for things like login if the user wants to make sure the 2FA password isn't stored in
    # the HS database.
    public:
        # Whether or not the public-facing endpoints should be enabled.
        enabled: false
# Permissions for using the bridge.
# Permitted values:
#   relaybot - Only use the bridge via the relaybot, no access to commands.
#       user - Relaybot level + access to commands to create bridges.
#  puppeting - User level + logging in with a Telegram account.
#       full - Full access to use the bridge, i.e. previous levels + Matrix login.
#      admin - Full access to use the bridge and some extra administration commands.
# Permitted keys:
#        * - All Matrix users
#   domain - All users on that homeserver
#     mxid - Specific user
permissions:
    "*": "relaybot"
    "@admin:domain_homeserver": "admin"

telegram:
    api_id: votre_api_id
    api_hash: votre_api_hash
    bot_token: votre_token
```

```bash
telegram:
    # Telethon proxy configuration.
    # You must install PySocks from pip for proxies to work.
    proxy:
        # Allowed types: disabled, socks4, socks5, http
        type: socks5
        # Proxy IP address and port.
        address: 95.217.132.133      --> serveur socks5 français
        port: 3003
        # Whether or not to perform DNS resolving remotely.
        rdns: true
        # Proxy authentication (optional).
        username: "sockuser" --> username du serveur socks5
        password: "secret" --> mot de passe du serveur socks5
```
Une fois votre fichier de configuration config.yaml correctement modifié avec vos informations, il faut générer un fichier registration pour faire le lien avec votre homeserver

Toujours dans votre virtualenv sous /etc/matrix-synapse/matrix-telegram

Générer votre fichier registration :

```bash
python -m mautrix_telegram -g -r registration_telegram.yaml
```

Ajouter ensuite ce fichier dans la configuration de votre homeserver :

```bash
cd /etc/matrix-synapse/
gedit homeserver.yaml &
```
À l'aide d'un CTRL+F rechercher ***app_service_config_files:***

Puis modifier le fichier homeserver.yaml en renseignant le chemin au fichier registration :

```bash
app_service_config_files:
- /etc/matrix-synapse/matrix-telegram/registration_telegram.yaml
```

Redémarrer votre homeserver :

```bash
systemctl restart matrix-synapse
```

Reconfigurer la base de données du bridge :

```bash
cd /etc/matrix-synapse/matrix-telegram
alembic upgrade head
```

## Étape 6 - Démarrage du Bridge Telegram Matrix<a id="etape6"></a>

Toujours dans votre terminal avec votre virtualenv taper :

```bash
python -m mautrix_telegram
```

Si tout c'est bien passé vous devriez avoir ce type de message : ***Initializing appservice bot***

## Étape 7 - Invitation du Bot dans Telegram et Element<a id="etape7"></a>
##Sur Telegram<a id="etape7.1"></a>

Créer un nouveau groupe puis inviter votre bot @example_bot puis dans la gestion du groupe lui donner les droits administrateurs.

Une fois le bot invité, taper la commande ```/id```

Le bot doit vous répondre et vous donner un numéro commençant par un ```-```

Conserver ce numéro, c'est le numéro du bridge

##Sur Element<a id="etape7.2"></a>

Dans notre exemple, nous utilisons le client Element, n'importe quel client supportant Matrix peut-être utilisé

Créer un nouveau Salon puis inviter le bot @telegrambot:domain_homeserver. Attention ne pas confondre le Bot créé sur telegram et le bot créé sur le serveur linux !

Après quelques instants Telegram bridge bot doit vous envoyer un message du type : ***Hello, I'm a Telegram bridge bot. Use !tg help for help or !tg login to log in.***

Répondez par ```!tg login```

Le bot doit vous répondre : ***Please send your phone number (or bot auth token) here to start the login process.***

Ici le bot nous demande le numéro id que l'on a demandé au bot sur telegram.

Répondez par ```!tg bridge -numero_id```

Le bot vous répond ensuite : ***That Telegram chat has no existing portal. To confirm bridging the chat to this room, use continue***

Répondez par ```!tg continue```

Pour finir le bot vous informe que le bridge est opérationel : ***Bridging complete. Portal synchronization should begin momentarily.***

Vous avez maintenant un Bridge Matrix Telegram qui fonctionne.

Si vous êtes amené à modifier le fichier config.yaml de matrix-telegram penser à redémarrer le homeserveur.

Ne fermez pas le terminal virtualenv du bridge sinon cela arrêtera celui-ci

## Source <a id="source"></a>

- https://docs.mau.fi/bridges/python/setup/index.html?bridge=telegram
- https://wiki.calculate-linux.org/matrix_telegram_bridge