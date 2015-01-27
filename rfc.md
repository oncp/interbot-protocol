Le protocole interbot doit permettre plusieurs fonctionnalités :
 - partage des données d'un bot à tous les autres
 - envoi de commande entre bots pour un partage des commandes sur tout le réseau interbot
 - ajout et suppression des bots connectés à la communauté

1/ Présentation générale

Chaque membre de la communauté peut faire office de client ou de serveur. S'il fait juste office de client, il ne pourra pas accepter de clients et donc aider à répartir la charge entre les membres.
Lors de la première connexion à la communauté, un nouveau bot DOIT se connecter sur un membre serveur, afin d'enregistrer sa demande de connexion, qui sera relayées aux autres membres pour une décision.

Les membres de la communauté doivent se partager les informations, selon deux modes de communication :
 - mode HUB : en mode HUB, chaque membre est connecté à un nœud serveur, SAUF s'il n'y en a pas, dans ce cas il DOIT être serveur. Ainsi, lorsque ce membre reçoit des données d'un enfant, il les propage a son parent et s'il reçoit des données de son parent, il les propage à ses enfants. Chaque nœud propageant les données à son parent et ses enfants pour que tous reçoivent ces données.
 - mode p2p : en mode p2p, le membre qui a besoin d'une information fait une demande générale, afin de savoir qui possède la version la plus récente de l'information. 
Tous les membres propagent la version qu'ils possèdent et le membre demandeur choisit par la suite dans ceux qui possèdent la version la plus à jour.
Ces dernier devront donc envoyer au demandeur la version qu'ils possèdent.
Une fois la dernière version téléchargée, le membre demandeur envoi un hash de sa version à tous les membres, afin de savoir si les données sont valides.


2/ Ajout et Suppression de bots connectés à la communauté

a?) Pour ajouter un bot à la communauté, celui ci doit se connecter à la communauté par le biais d'un membre actif faisant office de serveur.
Cette demande d'ajout DOIT être accompagné d'une clé publique d'un système de certificat, généré par le biais d'un outil dédié, tel OpenSSL. Cette clé publique permettra la communication ultérieure entre le nouveau bot et les membres actuels, et sera la seule preuve attestant de l'authenticité du bot. Il est donc important pour le nouveau bot de garder la clé privée en mémoire afin de pouvoir déchiffrer toutes les communications.
Une fois la demande effectuée -NDLR : expliquer en détail comment la demande est effectuée, IRC, HTTP, etc-, le membre ayant fait office de serveur réplique la commande aux autres bots afin que tout le monde puisse avoir l'information de demande d'ajout. Une période d'une semaine sera mise en place, durant laquelle les membres de la communauté, pourront choisir d'autoriser ou d'interdire le nouveau bot.
Au bout de ces 7 jours, la demande d'ajout est inspectée par la communauté.
Si plus de 50% des membres ayant répondus ont validé cette demande, le nouveau bot est intégré à la communauté. Les membres n'ayant pas répondu sont considérés comme inactifs et seront supprimés de la communauté.
S'ils veulent par la suite réintégrer la communauté, ils devront effectuer une nouvelle demande d'ajout.
NDLR : il faut réfléchir à comment valider la liste des bots de chaque membres, si un membre est corrompu, il peut valider l'ajout de son coté, et répliquer les datas au nouveau membre sans que les autres le sache.

b?) Pour ajouter un bot à la communauté, son propriétaire doit en faire la demande sur irc en donnant sa clé publique.
Les propriétaires de bots du réseau décident d'ajouter (ou non) sa clé a leurs bots respectifs.
Si au moins X% des bots de la communauté ont la clé ajoutée (manuellement) et qu'il y a au moins N bots disponibles (sécu) alors tous les bots du réseau l'ajoutent eux aussi.

Pour supprimer un bot de la communauté, une demande de suppression est envoyée. Cette demande peut être envoyée manuellement ou automatiquement -par exemple, lorsqu'un membre est considéré comme down suite à de trop nombreuses demandes sans réponse- Le même principe qu'une demande d'ajout est par la suite effectuée.

3/ Partage de données d'un bot à tous les autres

Il existe deux modes de partage de données. Le mode hub qui sera utilisé pour le partage en temps réel. Ce mode est utilisé lorsque pour les bots sont synchronisés.
Un bot est considéré synchronisé lorsque celui ci suppose que toutes les données qu'il possède sont à jour, cette validation se faisant par le mode p2p.

4/ Connexion aux membres

La connexion aux membres se fait de manière sécurisée par l'utilisation du protocole SSL avec certificat, ainsi d'un chiffrement symétrique [REFLECHIR A L'ALGO]. La clé de ce chiffrement symétrique sera différent à chaque session et sera calculé grâce à un échange Diffie Hellman.
Un membre se connectera à un autre en utilisant les protocole TCP et IP, ce qui garantira l'authenticité du membre.
Une fois la session TCP (syn/syn ack/ack) ouverte, le client et le serveur DOIVENT ouvrir une session SSL, et DOIVENT utiliser le Perfect Forward Secrecy.
Lorsque la session SSL est complète, la négociation du Diffie Helmann DOIT débuter. Une fois la négociation effectuée, les deux membres sont supposés avoir une clé commune.
Un premier échange entre le client et le serveur DOIT être effectué pour valider le bon fonctionnement du chiffrement. Pour ce faire, le client DOIT choisir une chaine de texte qu'il chiffrera avec [ALGO DE CHIFFREMENT]. Le serveur DOIT la déchiffrer, opérer à un inversement des caractères de gauche à droite, puis la rechiffrer pour renvoyer la chaine au client.
Si le client recoit sa chaine inversée, la session est considérée comme étant sécurisée et l'échange de donnée PEUT se faire.
Par exemple, si le client envoie la chaine "test123", le serveur DOIT renvoyer "321tset".
La chaine de caractère DOIT être différente pour chaque nouvelle session.
Une fois la session active, celle ci DOIT rester active. Pour cela le serveur DOIT envoyer des requêtes de keep-alive. Celles ci DOIVENT contenir une chaine de caractère aléatoire, et le client DOIT renvoyer en réponse la même chaine de caractère.