Le protocole interbot doit permettre plusieurs fonctionnalités :
 - partage des données d'un bot à tous les autres
 - envoi de commande entre bots pour un partage des commandes sur tout le réseau interbot
 - ajout et suppression des bots connectés à la communauté

1/ Présentation générale

Chaque membre de la communauté peut faire office de client ou de serveur. S'il fait juste office de client, il ne pourra pas accepter de clients et donc aider à répartir la charge entre les membres.

2/ Communication

Les membres de la communauté doivent répliquer les informations qu'ils reçoivent aux autres membres.
Chaque membre est connecté à un nœud serveur, SAUF s'il n'y en a pas, dans ce cas il DOIT être serveur. Ainsi, lorsque ce membre reçoit des données d'un enfant, il les propage a son parent et s'il reçoit des données de son parent, il les propage à ses enfants. Chaque nœud propageant les données à son parent et ses enfants pour que tous reçoivent ces données.

3/ Ajout et Suppression de bots connectés à la communauté

Chaque propriétaire de noeud peut (ou non) autoriser un bot à se connecter.
Lors de l'ajout d'un bot au réseau, sa clé publique est manuellement ajoutée par les différents propriétaires des autres robots du réseau.
Pour supprimer un bot du réseau, chaqun des différents propriétaire de bots peuvent (ou non) supprimer la clé publique du robot concerné, il ne pourra donc plus s'y connecter.

4/ Partage de données d'un bot à tous les autres

Les données envoyées au travers du réseau transitent en clair, mais elles sont signées, ce qui permet d'être sûr qu'elles ne soient pas corrompues et d'en connaitre son expéditeur. Les données dont la signature ne correspond pas DOIVENT ne pas être prises en compte.

Si un bot a besoin de la communauté pour obtenir une information, il en fait la demande a son parent et ses enfants.

Tous les membres propagent la version qu'ils possèdent et le membre demandeur choisit par la suite dans ceux qui possèdent la version la plus à jour, celui qui lui enverra l'information.
Une fois choisi, ce dernier devra donc envoyer la version qu'il possède.

5/ Connexion aux membres

La connexion aux membres se fait de manière sécurisée par l'utilisation du protocole SSL avec certificat.
Un membre se connectera à un autre en utilisant les protocole TCP et IP, ce qui garantira l'authenticité du membre.
Une fois la session TCP (syn/syn ack/ack) ouverte, le client et le serveur DOIVENT ouvrir une session SSL, et DOIVENT utiliser le Perfect Forward Secrecy.
Lorsque la session SSL est complète, [ identification / Cryptage supplémentaire ? ]
Une fois la session active, celle ci DOIT rester active. Pour cela le serveur DOIT envoyer des requêtes de keep-alive. Celles ci DOIVENT contenir une chaine de caractère aléatoire, et le client DOIT renvoyer en réponse la même chaine de caractère. [ nécessaire pour du keep-alive ? ]

6/ Format des données d'un "message"

Pour pouvoir communiquer entre eux, les bots s'envoient des "messages".
Ce message contient :
- L'expéditeur
- La signature
- Le contenu du message

Pour ceci, le format suivant est utilisé :
- id_bot (16 bytes) : md5 de la clé publique du bot concerné
- signature_length (2 bytes) : longueur (en byte) de la signature du message
- message_length (3 bytes) : longueur (en byte) du message
- signature (taille dans signature_length) : signature
- message (taille dans message_length) : contenu du message
