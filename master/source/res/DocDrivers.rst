Annexe - Documentation technique
================================

:Type: Documentation technique
:Organisateur: ABI
:Auteur: CDS
:Objectif: Documenter le fonctionnement logiciel des matériels de contrôle d'accès

Ce document présente l'installation et le fonctionnement des drivers de bissas

#. **DRIVERS DES MATERIELS de CONTROLE D'ACCES**
#. Les matériels de contrôle d'accès sont fournis avec des drivers dont l'API est composée des opérations essentielles suivantes
#. **Tous les matériels**
#. -- DriverMatériel(port entier, refContrôleur Contrôleur). Crée et lance une instance du driver avec
#. - port : le numéro du port de connexion au panneau de brassage du bissas
#. - refContrôleur la référence (un oid) du contrôleur de bissas qui va le commander
#. Ce constructeur générique a des sous-opérations pour chacun des types de matériels, par exemple DriverBarrière(port entier, refContrôleur Contrôleur) pour les barrières.
#. Des opérations sont spécifiques à chaque type de matériel
#. **Borne à ticket**
#. -- imprimerTicket(z chaîne, n entier). Imprime et sort un ticket de parking de numéro n indiquant une entrée dans la zone z à l'heure et la date courante.
#. -- diffuser(m message). Diffuse le message audio m sur les hauts-parleurs.
#. -- ouvrirMicro(). Ouvre le micro.
#. **Barrière**
#. -- lever() : chaîne. Lève la barrière. Renvoie KO en cas d'échec (impossible de lever la barrière), OK sinon 
#. -- baisser() : chaîne. Baisse la barrière. Renvoie KO en cas d'échec (impossible de baisser la barrière), OK sinon
#. **Capteur de passage**
#. -- détecter(t temps) : chaîne. Détecte le passage d'un un véhicule entre le lancement de l'opération et le temps t (en secondes). Renvoie KO si aucun véhicule n'a été détecté dans l'intervalle de temps; OK sinon.
#. **Caméra de surveillance**
#. -- lancer() : chaîne. Lance la diffusion de la caméra. Renvoie un lien vers le flux vidéo si la caméra fonctionne, KO sinon.
#. **Borne de paiement**
#. Les méthodes ne sont pas encore documentées
#. #########
#. **INSTALLATION DES MATERIELS de CONTROLE D'ACCES**
#. ######
#. L'installation d'un matériel s'effectue de la façon suivante
#. -- sur le serveur applicatif : création d'une instance de Matériel (dans la sous-classe correspondante) en lui donnant le numéro de série du matériel et d'un contrôleur de matériel en lui donnant l'oid du Matériel.
#. Exemple : appel du constructeur Barrière(k5554v2), qui crée une instance de Barrière d'oid b5670 et appel du constructeur ContrôleurBarrière(b5670), qui crée une instance de ContrôleurBarrière d'oid cb9973 liée à b5670
#. -- Connexion physique du matériel au panneau de brassage du serveur de contrôle
#. -- Installation du code du driver de matériel sur le serveur de contrôle
#. -- Création sur le serveur de contrôle d'une instance du driver de matériel, en lui donnant en paramètre le numéro du port de connexion au panneau de brassage et l'oid du contrôleur de matériel qui va le commander par la suite
#. Exemple : appel de DriverBarrière(61,cb9973) qui crée une instance de DriverBarrière d'oid db6643
#. -- Appel par cette instance de driver de l'opération lierAMateriel(oid DriverMateriel) du contrôleur de matériel. Elle indique au contrôleur de bissas la référence objet (oid) du driver de matériel qu'il va commander
#. Exemple le DriverBarrière d'oid db6643 appelle lierAMateriel(db6643) sur le ContrôleurBarrière d'oid cb9973
#. De la sorte, le contrôleur de matériel connait les oids du driver de matériel qu'il contrôle et de l'objet persistant qui représente le matériel dans le système, et le driver de matériel connait l'oid de son contrôleur 
#. Exemple : le contrôleur de barrière cb9973 connait l'oid db6643 du driver de matériel qu'il contrôle et de l'objet persistant b5670 qui représente la barrière dans le système, et le driver de barrière db6643 connait l'oid de son contrôleur cb9973
#. -- Pour finir selon les cas
#. - le driver d'un borne à tickets d'entrée par badge se met en attente du passage d'un badge ou d'un appel audio 
#. - le driver d'un borne à tickets d'entrée payante se met en attente d'une demande de ticket ou d'un appel audio 
#. - le driver d'un borne à tickets de sortie par badge se met en attente du passage d'un badge ou d'un appel audio
#. - le driver d'un borne à tickets de sortie payante se met en attente d'une introduction de ticket ou d'un appel audio
#. - le driver d'un borne de paiement se met en attente d'une introduction de ticket
#. ######
#. **FONCTIONNEMENT EN CONTINU**
#. ######
#. **Borne à tickets d'entrée par badge**
#. A la lecture d'un badge, le driver de borne à tickets d'entrée appelle l'opération contrôlerEntrée(code entier) de son contrôleur de matériel où code est le numéro du badge lu. La méthode de cette opération va lancer une méthode de gestion de l’entrée - par ce point d’accès - d'un contrôleur de point d'accès.
#. **Borne à tickets d'entrée payante**
#. A la demande de ticket, le driver de borne à tickets appelle l’opération contrôlerEntrée() de son contrôleur de matériel. La méthode de cette opération va lancer une méthode de gestion de l’entrée - par ce point d’accès - d'un contrôleur de point d'accès.
#. **Borne à tickets de sortie par badge**
#. A la lecture d'un badge, le driver de borne à tickets d'entrée appelle l'opération contrôlerSortie(code entier) de son contrôleur de matériel où code est le numéro du badge lu. La méthode de cette opération va lancer une méthode de gestion de l’entrée - par ce point d’accès - d'un contrôleur de point d'accès.
#. **Borne à tickets de sortie payante**
#. A la lecture d'un ticket, le driver de borne appelle l'opération contrôlerSortie(noTicket entier) de son contrôleur de matériel où noTicket est le numéro du ticket de stationnement. La méthode de cette opération va lancer une méthode de gestion de l’entrée - par ce point d’accès - d'un contrôleur de point d'accès.
#. **Toutes les borne à tickets**
#. A l'appui sur le bouton d'appel audio, le driver de borne appelle l'opération appelAudio() de son contrôleur de matériel. La méthode de cette opération va gérer l'appel audio depuis ce point de passage.
#. **Borne de paiement**
#. A la lecture d'un ticket, le driver de borne de paiement d'entrée appelle l'opération contrôlerPaiement(noTicket entier) de son contrôleur de matériel où noTicket est le numéro du ticket de stationnement lu. La méthode de cette opération va gérer le paiement par cette borne point d'accès.
