# hebergementweb
---------------------------------------------------------------------------------------------------------------------------------------------------------
**Prérequis :**

Pour cet EI nous allons partir du principe que :
- nos machines sont déja installées (3 machines virtuelles Debian)
- nous possédons un compte utilisateur sans droits, nous effectuerons une élévation de privilège pour cet exercice, au début avec ```sudo su```, après passage de la commande, il n'est plus nécéssaire de mettre "sudo" avant chaque commande.
- Le serveur SSH est installé lors de la mise en place des VM, sinon
  ```
  apt-get install openssh-server
  ```
- pour une attribution d'ip différente de la machine, passer le mode de connection de la VM en "Accès par pont"

**Logiciels utilisés :**

- Virtualbox
- Notepad++
- Putty
  
---------------------------------------------------------------------------------------------------------------------------------------------------------

1) **Installation des dépendances :**


**La partie suivante est à appliquer aux 3 machines :**

après **login**, nous passons en SuperUser avec la commande 
```
sudo su
```



/!\ pour utiliser des anciennes versions de php, nous allons faire appel a des repository publiques, cela représente un risque de sécurité, continuez avec prudence !

puis :

```
apt install software-properties-common
```

**Ensuite :**

``` 
apt install python3-launchpadlib
```

**puis :**

```
add-apt-repository ppa:ondrej/php
```



On met à jour le catalogue d'application (apt), le paramètre -y répond "yes" a toutes les demandes :

```
apt-get update -y
```

**puis**

```
apt-get upgrade -y
``` 

**puis**

```
sudo apt-get install ca-certificates apt-transport-https software-properties-common wget curl lsb-release
```

**puis :**

```
curl -sSL https://packages.sury.org/php/README.txt | sudo bash -x
```

**puis**

```
sudo apt-get update
```

**crédits à [Antonin](https://github.com/antoninpomies/HebergementWeb) :** 
```
apt install sudo apache2 mariadb-server php libapache2-mod-php php-mysql openssh-server php8.1 libapache2-mod-php8.1 php8.2-fpm php7.4-fpm -y && systemctl enable apache2
```

une fois mis a jour, nous allons nous connecter en **SSH** (Secure SHell) via **Putty**, a l'adresse ip renvoyée par cette commande :

```
ip a | grep inet | grep ens33
```

Cela nous apporte principalement la gestion du copier-coller avec un clic droit !

---------------------------------------------------------------------------------------------------------------------------------------------------------

2) **Configuration des serveurs :**

La partie suivante ne s'applique qu'aux serveurs web (SRV-WEB1 et SRV-WEB2) :

```
sudo cp /etc/php/7.4/fpm/pool.d/www.conf /etc/php/7.4/fpm/pool.d/domaine1-php7.conf
```
```
sudo cp /etc/php/8.2/fpm/pool.d/www.conf /etc/php/8.2/fpm/pool.d/domaine1-php8.conf
```

**puis :**
 ```
 nano /etc/php/7.4/fpm/pool.d/domaine1-php7.conf
```

 et changer
 
 - **[www]** par -> **[domaine1-php7]** ou tout autre nom de sous domaine de votre choix
 - La ligne **listen =** -> **listen = /run/php/php7.4-fpm-domaine1.sock**

 **même chose sur la version 8.2 :**

```
nano /etc/php/8.2/fpm/pool.d/domaine1-php8.conf
```

 et changer
 
 - **[www]** par -> **[domaine1-php8]** ou tout autre nom de sous domaine de votre choix
 - La ligne **listen =** -> **listen = /run/php/php8.2-fpm-domaine1.sock**




**Refaire cette partie pour le deuxième serveur :**




```
sudo cp /etc/php/7.4/fpm/pool.d/www.conf /etc/php/7.4/fpm/pool.d/domaine2-php7.conf
```

```
sudo cp /etc/php/8.2/fpm/pool.d/www.conf /etc/php/8.2/fpm/pool.d/domaine2-php8.conf
```

**puis :**
 ```
 nano /etc/php/7.4/fpm/pool.d/domaine2-php7.conf
```

 et changer
 
 - **[www]** par -> **[domaine2-php7]** ou tout autre nom de sous domaine de votre choix
 - La ligne **listen =** -> **listen = /run/php/php7.4-fpm-domaine2.sock**
 
 **même chose sur la version 8.2 :**

```
nano /etc/php/8.2/fpm/pool.d/domaine2-php8.conf
```

 et changer
 
 - **[www]** par -> **[domaine2-php8]** ou tout autre nom de sous domaine de votre choix
 - La ligne **listen =** -> **listen = /run/php/php8.2-fpm-domaine2.sock**


---------------------------------------------------------------------------------------------------------------------------------------------------------

**3) Création des fichiers de conf VirtualHost :**

**Toujours a effectuer sur les serveurs SRV-WEB1 et SRV-WEB2**



**Sur SRV-WEB1 :** 

on créer le dossier avec :

```
mkdir -p /var/www/domaine1-php7
```

```
nano /etc/apache2/sites-available/domaine1-php7.conf
```

et coller la configuration suivante (à adapter) :

```
<VirtualHost *:80>
   ServerName php7.domaine1.com
   DocumentRoot /var/www/domaine1-php7
   <FilesMatch \.php$>
       SetHandler proxy:unix:/run/php/php7.4-fpm-domaine1.sock|fcgi://localhost/
   </FilesMatch>
</VirtualHost>
```

**puis :**
on créer le dossier avec :

```
mkdir -p /var/www/domaine1-php8
```

```
nano /etc/apache2/sites-available/domaine1-php8.conf
```

et coller la configuration suivante (à adapter) :

```
<VirtualHost *:80>
   ServerName php8.domaine1.com
   DocumentRoot /var/www/domaine1-php8
   <FilesMatch \.php$>
       SetHandler proxy:unix:/run/php/php8.2-fpm-domaine1.sock|fcgi://localhost/
   </FilesMatch>
</VirtualHost>
```

On active les modules nécéssaires aux sites :     

```
a2ensite domaine1-php7.conf
```
```
a2ensite domaine1-php8.conf
```
modules proxy et proxy_fcgi :
```
a2enmod proxy proxy_fcgi
```
```
sudo a2enmod proxy_http
```

**Sur SRV-WEB2 :** 


on créer le dossier avec :

```
mkdir -p /var/www/domaine2-php7
```


```
nano /etc/apache2/sites-available/domaine2-php7.conf
```

et coller la configuration suivante (à adapter) :

```
<VirtualHost *:80>
   ServerName php7.domaine2.com
   DocumentRoot /var/www/domaine2-php7
   <FilesMatch \.php$>
       SetHandler proxy:unix:/run/php/php7.4-fpm-domaine2.sock|fcgi://localhost/
   </FilesMatch>
</VirtualHost>
```

**puis :**

on créer le dossier avec :

```
mkdir -p /var/www/domaine2-php8
```

```
nano /etc/apache2/sites-available/domaine2-php8.conf
```

et coller la configuration suivante (à adapter) :

```
<VirtualHost *:80>
   ServerName php8.domaine2.com
   DocumentRoot /var/www/domaine2-php8
   <FilesMatch \.php$>
       SetHandler proxy:unix:/run/php/php8.2-fpm-domaine2.sock|fcgi://localhost/
   </FilesMatch>
</VirtualHost>
```

On active les modules nécéssaires aux sites :     

```
a2ensite domaine2-php7.conf
```
```
a2ensite domaine2-php8.conf
```
modules proxy et proxy_fcgi :
```
a2enmod proxy proxy_fcgi
```

**Pour terminer notre configuration, nous allons créer nos 4 pages phpinfo.php :**

**Sur SRV-WEB1 :**


```
nano /var/www/domaine1-php7/infophp.php
```
Dans **infophp.php** renseigner :
```
<?php

phpinfo( );

?>
```

**Puis :**

```
nano /var/www/domaine1-php8/infophp.php
```
Dans **infophp.php** renseigner :
```
<?php

phpinfo( );

?>
```

**Sur SRV-WEB2 :**


```
nano /var/www/domaine2-php7/infophp.php
```
Dans **infophp.php** renseigner :
```
<?php

phpinfo( );

?>
```

**Puis :**

```
nano /var/www/domaine2-php8/infophp.php
```
Dans **infophp.php** renseigner :
```
<?php

phpinfo( );

?>
```

On s'assure de n'avoir fait aucune erreur dans nos fichiers avec la commande : 
```
apachectl configtest
```

Si tout se passe bien, on passe à la suite.

**On relance ensuite les services sur les deux serveurs :**
```
systemctl restart apache2
```
```
systemctl restart php7.4-fpm
```
```
systemctl restart php8.2-fpm
```


---------------------------------------------------------------------------------------------------------------------------------------------------------

**4) Partie Reverse proxy**

**Sur le SRV-RPX :**

Pour récupérer l'adresse ip des serveurs, saisissez la commande suivante :
```
ip addr
```

créer un fichier nominatif de configuration avec :
```
nano /etc/apache2/sites-available/domaine1.com.conf
```
et coller la configuration suivante :
```
<VirtualHost *:80>
    ServerName domaine1.com
    ProxyPreserveHost On
    ProxyPass / http://adresse_ip_serveur_web1/
    ProxyPassReverse / http://adresse_ip_serveur_web1/
</VirtualHost>
```

créer un fichier nominatif de configuration avec :
```
nano /etc/apache2/sites-available/domaine2.com.conf
```
et coller la configuration suivante :
```
<VirtualHost *:80>
    ServerName domaine2.com
    ProxyPreserveHost On
    ProxyPass / http://adresse_ip_serveur_web2/
    ProxyPassReverse / http://adresse_ip_serveur_web2/
</VirtualHost>
```


on redémarre le service apache2 :
```
systemctl restart apache2
```
on vérifie qu'il est en status "Active" :
```
systemctl status apache2
```

---------------------------------------------------------------------------------------------------------------------------------------------------------

**5) Modification du fichier Hosts**

Sous 
```
C:\Windows\System32\drivers\etc\
```

**éditez** avec **Notepad++** en mode **Admin** le fichier **hosts**, c'est un fichier système, vous devrez donc avoir les droits sur ce fichier, saisissez les lignes suivantes :

```
<ip du reverse proxy> domaine1.com
<ip du reverse proxy> domaine2.com


2a01:cb18:505:c600:a00:27ff:fe8e:d56f php7.domaine1.com
2a01:cb18:505:c600:a00:27ff:fe8e:d56f php8.domaine1.com
2a01:cb18:505:c600:a00:27ff:fe8e:d56f php7.domaine2.com
2a01:cb18:505:c600:a00:27ff:fe8e:d56f php8.domaine2.com
```
^ Nous renseignons l'adresse du reverse proxy puisque se sera lui qui redirigera les utilisateurs vers la bonne adresse ip en fonction de l'URL saisie.

**6) vérifications**


Se rendre aux URL suivantes : 

```domaine1.com```
```domaine2.com```
```php7.domaine1.com```
```php8.domaine1.com```
```php7.domaine2.com```
```php8.domaine2.com```
