# Total Lacaussade

Suite à l'installation d'une nouvelle Livebox, les PC du réseau ne pouvaient plus se connecter au serveur. La configuration du site comprenait :

Une baie de brassage ;
Un serveur et un portable au niveau N+1 avec le Wi-Fi activé ;
Un hub 4 ports au niveau N+1 ;
Deux hubs 4 ports au niveau N0 ;
Deux PC de caisse au niveau N0 avec le Wi-Fi activé.
Le problème principal : l'application Everest fonctionnait uniquement sur le serveur, mais ne se lançait pas sur les postes de caisse.

Solution mise en place :
Désactivation du Wi-Fi sur le serveur et les postes clients.
Assignation des postes et du serveur à un réseau privé.
Intervention à distance avec Marc pour rebrancher correctement les connecteurs RJ45.
Vérification sur chaque poste client via un test Telnet sur l’adresse 192.168.1.128 (port 1433), qui a confirmé l’absence de connexion initiale.