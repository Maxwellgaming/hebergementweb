# hebergementweb
---------------------------------------------------------------------------------------------------------------------------------------------------------
**Prérequis :**

Pour cet EI nous allons partir du principe que :
- nos machines sont déja installées (3 machines virtuelles Debian)
- nous possédons un compte utilisateur sans droits, nous effectuerons une élévation de privilège pour cet exercice, au début avec ```sudo su```, après passage de la commande, il n'est plus nécéssaire de mettre "sudo" avant chaque commande.
- Le serveur SSH est installé lors de la mise en place des VM, sinon ```apt-get install openssh-server```

**Logiciels utilisés :**

- Virtualbox
- Notepad++
- Putty
  
---------------------------------------------------------------------------------------------------------------------------------------------------------

1) **Installation des dépendances :**


**La partie suivante est à appliquer aux 3 machines :**

après **login**, nous passons en SuperUser avec la commande ```sudo su```


On met à jour le catalogue d'application (apt), le paramètre -y répond "yes" a toutes les demandes :

```apt-get update -y```

**puis**

```apt-get upgrade -y``` 


**crédits à [Antonin](https://github.com/antoninpomies/HebergementWeb) :** ```apt install sudo apache2 mariadb-server php libapache2-mod-php php-mysql openssh-server php7.4 libapache2-mod-php7.4 -y && systemctl enable apache2```

une fois mis a jour, nous allons nous connecter en **SSH** (Secure SHell) via **Putty**, a l'adresse ip renvoyée par cette commande :

```ip a | grep inet | grep ens33```

Cela nous apporte principalement la gestion du copier-coller avec un clic droit !

---------------------------------------------------------------------------------------------------------------------------------------------------------
**Configuration des serveurs :**

La partie suivante ne s'applique qu'aux serveurs web (SRV-WEB1 et SRV-WEB2) :





---------------------------------------------------------------------------------------------------------------------------------------------------------
