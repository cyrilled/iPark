Annexe - Documentation technique
================================

:Type: Documentation technique
:Organisateur: ABI
:Auteur: CDS
:Objectif: Documenter le fonctionnement logiciel des matériels de contrôle d'accès

Ce document présente l'installation et le fonctionnement des drivers et contrôleurs de matériels de contrôle d'accès

#. **PRINCIPE DE FONCTIONNEMENT**
#. Le système suit le patron ECB (Entity-Control-Boundary, Métier-Contrôle-IHM en français)
#. Selon ce patron, l'objet d'IHM et l'objet de contrôle se connaissent mutuellement, l'objet domaine et l'objet de contrôle se connaissent mutuellement, mais l'objet d'IHM et l'objet métier ne se connaissent pas et ne communiquent pas entre eux.
#. Ainsi pour un point de passage (entrée ou sortie) le système utilise trois objets : un objet métier (Entity), un objet de contrôle (Control) et un objet d'IHM (Boundary).
#. L'objet d'IHM est un driver de point de passage, de classe DriverEntree ou DriverSortie, selon le cas.
#. L'objet de contrôle est un contrôleur de passage, de classe ControleurEntree ou ControleurSortie, selon le cas.
#. l'objet métier appartient à la classe PointEntree ou PointSortie selon le cas. Il est persistant sur le serveur applicatif.
#. Exemple : dpe3367 est une instance de DriverPointEntrée, cpe4571 est une instance de ControleurPointEntree, pe6874 est une instance de PointEntree représentant le point d'entrée du niveau 5, stockée de façon persistante sur le serveur applicatif.
#. dpe3367 et cpe4571 se connaissent mutuellement, pe6874 et cpe4571 se connaissent mutuellement, mais dpe3367 et pe6874 ne se connaissent pas et ne communiquent pas entre eux.
#. Comme un point de passage est composé de barrière(s), borne à tickets, ..., qu'on peut assembler à volonté, son driver n'est pas fourni par Z-Park, qui fournit uniquement les drivers des matériels de contrôle (barrière, borne à tickets, ...).
#. Le driver de point de passage reçoit les informations de ces drivers des matériels de contrôle (barrière, borne à tickets, ...) et les commande via leur opérations.


#. 
est représenté par un objet persistant de classe métier PointEntree ou PointSortie selon le cas. Un objet contrôleur est crée pour  Chacun de ces points de passage est relié a objet contrôleur de passage, de classe ControleurEntree ou ControleurSortie selon le cas. Celui-ci est relié à un objet d'IHM, de classe DriverPointEntrée ou DriverPointSortie selon le cas. 
#. Avec ECB, le système devrait utiliser pour chacun des matériels d'accès (barrière, borne à tickets, ...) trois objets : un objet métier (Entity), un objet de contrôle (Control) et un objet d'IHM (Boundary).
#. l'objet d'IHM est un driver du matériel, de classe DriverMateriel, fournie par Z-Park
#. l'objet de contrôle est un contrôleur de matériel, de classe ControleurMateriel
#. l'objet métier appartient à la classe correspondante (Barriere, BorneATicket, ...). Il est persistant sur le serveur applicatif.
#. 
Chaque instance de matériel est relié à l'instance de point de passage sur lequel il est mis en place.
#. Exemple : b5670 est une instance de Barriere représentant la barrière du point d'accès du niveau 5, stockée de façon persistante sur le serveur applicatif et reliée à pa896, instance de PointAcces représentant le point d'accès du niveau 5.
#. cb9973 est une instance de ControleurBarrière, db6643 est une instance de DriverBarriere
#. db6643 et cb9973 se connaissent mutuellement, b5670 et cb9973 se connaissent mutuellement, mais db6643 et b5670 ne se connaissent pas et ne communiquent pas entre eux.

 
#. **DRIVERS DES MATERIELS de CONTROLE D'ACCES**
#. Les matériels de contrôle d'accès sont fournis avec des drivers dont l'API est composée des opérations essentielles suivantes
#. **Tous les matériels**
#. -- DriverMatériel(port entier, refContrôleur Contrôleur). Crée et lance une instance du driver avec
#. - port : le numéro du port de connexion au panneau de brassage du matériel
#. - refContrôleur la référence (un oid) du contrôleur de matériel qui va le commander
#. Ce constructeur générique a des sous-opérations pour chacun des types de matériels, par exemple DriverBarrière(port entier, refContrôleur Contrôleur) pour les barrières.
#. Des opérations sont spécifiques à chaque type de matériel
#. **Borne à ticket**
#. -- imprimerTicket(z chaîne, n entier). Imprime et sort un ticket de parking de numéro n indiquant une entrée dans la zone z à l'heure et la date courante.
#. -- imprimerTicketForfaitaire(). Imprime et sort un ticket de parking forfaitaire.
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

#. **INSTALLATION DES MATERIELS POUR UN POINT DE PASSAGE**
#. ######
#. L'installation d'un point de passe s'effectue de la façon suivante
#. -- sur le serveur applicatif : création d'une instance persistante de point de passage (dans la classe PointEntree ou PointSortie selon le cas). la méthode de création lance elle-même la création d'une instance de contrôleur de point de passage en lui donnant son oid et récupère l'oid du contrôleur créé (les deux instances se connaissent maintenant). Elle lance aussi la création d'une instance de driver de point de passage en lui donnant son oid et récupère l'oid du contrôleur créé (les deux instances se connaissent maintenant).
#. Exemple : PointEntree("Niveau 5") crée l'instance pe6874 représentant le point d'entrée du niveau 5 et lance ControleurPointEntree(pe6874). ControleurPointEntree(pe6874) crée cpe4571 et enregistre (dans cpe4571) le lien vers pe6874 (de l'objet métier vers le contrôleur), puis lance DriverPointEntree(cpe4571). DriverPointEntree(cpe4571) crée dpe3367 et enregistre (dans dpe3367) le lien vers cpe4571 (du driver vers le contrôleur). Le résultat de DriverPointEntree(cpe4571) est dpe3367 qui est lui aussi stocké dans cpe4571 (lien du contrôleur vers le driver). Le résultat de ControleurPointEntree(pe6874) est cpe4571 qui est stocké dans pe6874 (lien de l'objet métier vers le contrôleur)

----------- AJOUTER UN DIAGRAMME d'OBJET  

#. Pour chaque matériel du point de passage
#. -- Connexion physique du matériel au panneau de brassage du serveur de contrôle
#. -- Installation du code du driver de matériel sur le serveur de contrôle
#. -- Création sur le serveur de contrôle d'une instance du driver de matériel, en lui donnant en paramètre le numéro du port de connexion au panneau de brassage et l'oid du contrôleur de point de passage qui va le commander par la suite
#. Exemple : appel de DriverBarrière(61,cb9973) qui crée une instance de DriverBarrière d'oid db6643
#. -- Appel par cette instance de driver de l'opération lierAMateriel(oid DriverMateriel) du contrôleur de matériel. Elle indique au contrôleur de matériel la référence objet (oid) du driver de matériel qu'il va commander


#. -- sur le serveur applicatif : création d'une instance de Matériel (dans la sous-classe correspondante) en lui donnant le numéro de série du matériel et d'un contrôleur de matériel en lui donnant l'oid du Matériel.
#. Exemple : appel du constructeur Barrière(k5554v2), qui crée une instance de Barrière d'oid b5670 et appel du constructeur ContrôleurBarrière(b5670), qui crée une instance de ContrôleurBarrière d'oid cb9973 liée à b5670
#. -- Connexion physique du matériel au panneau de brassage du serveur de contrôle
#. -- Installation du code du driver de matériel sur le serveur de contrôle
#. -- Création sur le serveur de contrôle d'une instance du driver de matériel, en lui donnant en paramètre le numéro du port de connexion au panneau de brassage et l'oid du contrôleur de matériel qui va le commander par la suite
#. Exemple : appel de DriverBarrière(61,cb9973) qui crée une instance de DriverBarrière d'oid db6643
#. -- Appel par cette instance de driver de l'opération lierAMateriel(oid DriverMateriel) du contrôleur de matériel. Elle indique au contrôleur de matériel la référence objet (oid) du driver de matériel qu'il va commander
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
#. **Lecteur de plaque**
#. A la lecture d'une plaque, le driver de lecteur de plaque appelle l'opération contrôlerEntrée(numero entier) (ou contrôlerSortie selon le cas) de son contrôleur d'entrée (ou de sortie selon le cas) où numero est le numéro d'immatriculation lu. Cette opération gère l’entrée (ou la sortie selon le cas) par ce point de passage.
#. **Borne à tickets d'entrée payante**
#. A la demande de ticket, le driver de borne à tickets appelle l’opération contrôlerEntrée() de son contrôleur de point d'entrée qui va gérer l’entrée par ce point d’accès.
#. **Borne à tickets de sortie payante**
#. A la demande de ticket, le driver de borne à tickets appelle l’opération contrôlerSortie() de son contrôleur de point d'entrée qui va gérer la sortie par ce point d’accès.
#. **Toutes les borne à tickets**
#. A l'appui sur le bouton d'appel audio, le driver de borne appelle l'opération appelAudio() de son contrôleur de passage qui va gérer l'appel audio depuis ce point de passage.

#. **Borne à tickets d'entrée ou de sortie par badge**
#. A la lecture d'un badge, le driver d'une borne à tickets appelle l'opération contrôlerEntrée(code entier) (ou contrôlerSortie selon le cas) de son contrôleur d'entrée (ou de sortie selon le cas) où code est le numéro du badge lu. Cette opération gère l’entrée (ou la sortie selon le cas) par ce point de passage.
#. **Borne de paiement**
#. A la lecture d'un ticket, le driver de borne de paiement d'entrée appelle l'opération contrôlerPaiement(noTicket entier) de son contrôleur de matériel où noTicket est le numéro du ticket de stationnement lu. La méthode de cette opération va gérer le paiement par cette borne point d'accès.
