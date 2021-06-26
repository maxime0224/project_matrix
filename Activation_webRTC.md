#Activation du service de WebRTC / relais VoIP sur Matrix

## Prérequis
- Ubuntu 18.04 ou plus récent
- Les droits super utilisateur (Root)
- Un homeserver Synapse installé et configuré

## Sommaire

- [Étape 1 - Installation du service coturn](#etape1)
- [Étape 2 - Démarrage du service coturn](#etape2)
- [Étape 3 - Configuration du Pare-Feu UFW](#etape3)
- [Étape 4 - Configuration du serveur Matrix-Synapse](#etape4)
- [Étape 5 - Redémarrage du serveur Matrix](#etape5)
- [Étape 6 - Configuration NAT (Optionnel)](#etape6)

[Source](#source)

[License](#license)




## Étape 1 - Installation du service coturn <a class="anchor" id="etape1"></a>

Taper les commandes suivantes :

```bash
sudo apt install coturn
```

Paramètre du service :

```bash
# Générer un mot de passe à l'aide de pwgen

(optionnel) sudo apt install pwgen
pwgen -s 64 1

# Récupérer la clé généré par pwgen
# Editer le fichier de configuration de coturn

sudo gedit /etc/turnserver.conf
```
A la fin du fichier ajouter les lignes suivantes :

```bash
use-auth-secret
static-auth-secret=mot_de_passe_pwgen
realm=turn.mondomaine.com
use-auth-secret
no-tcp
user-quota=12
total-quota=1200
```

Nota :
- Remplacer mot_de_passe_pwgen par le mot de passe généré par pwgen.
- Remplacer turn.mondomaine.com par le nom de domaine de votre homeserver ou un sous domaine si vous en disposez d'un.
- no-tcp permet de ne pas écouter sur le protocole TCP, car il est indispensable de passer par un protocole UDP dans le cas de VoIP.
- Les valeurs de user-quota et total-quota sont celles recommandées par le développeur.

## Étape 2 - Démarrage du service coturn <a class="anchor" id="etape2"></a>

Pour démarrer correctement le serveur au 1er lancement taper les commandes suivantes :

```bash
sudo systemctl start coturn
sudo systemctl stop coturn
sudo systemctl restart coturn
```

## Étape 3 - Configuration du pare-feu <a class="anchor" id="etape3"></a>

| Sens                         | Port | Protocole |
|------------------------------|------|----------|
| INPUT                        | UDP  | 3478     |
| OUTPUT (RELATED,ESTABLISHED) | UDP  | 3478     |

Pour mettre en place cette configuration sur le pare-feu ufw, taper la commande suivante :

```bash
sudo ufw allow 3478/udp
```

## Étape 4 - Configuration du serveur Matrix-Synapse <a class="anchor" id="etape4"></a>

Editer le fichier /etc/matrix-synapse/homeserver.yaml

```bash
sudo gedit /etc/matrix-synapse/homeserver.yaml
```

Cherchez les lignes :
- tur_uris
- turn_shared_secret
- turn_user_lifetime
- turn_allow_guests

Puis remplacer-les par les lignes ci-dessous :

```bash
turn_uris: [ "turn:turn.mondomaine.com:3478?transport=udp" ]
turn_shared_secret: mot_de_passe_pwgen 
turn_user_lifetime: 86400000
turn_allow_guests: True
```

Nota :
- Remplacer mot_de_passe_pwgen par le mot de passe généré par pwgen.
- Remplacer turn.mondomaine.com par le nom de domaine de votre homeserver ou un sous domaine si vous en disposez d'un.

## Étape 5 - Redémarrage du serveur Matrix <a class="anchor" id="etape5"></a>

Pour finir redémarrer le serveur matrix avec la commande ci-dessous.

```bash
sudo systemctl restart matrix-synapse
```

## Étape 6 - Configuration NAT (Optionnel) <a class="anchor" id="etape6"></a>

Il faut sur votre box/routeur créer une règle NAT si votre serveur détient uniquement une adresse IP Privée.

``Port Interne : 3478``

``Port Externe : 3478``

``Protocole : UDP``

``Equipement : @IP Privée/nom_DNS_equipement``

``IP Externe : Toutes``

## Source <a class="anchor" id="source"></a>

- https://github.com/matrix-org/synapse/blob/develop/docs/turn-howto.md

## License <a class="anchor" id="license"></a>

[LICENSE](LICENSE_NGINX)