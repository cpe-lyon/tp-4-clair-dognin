
# TP 4 - Utilisateurs, groupes et permissions

## Mathias Clair et Alexis Dognin 

## Exercice 1. Gestion des utilisateurs et des groupes

  **1. Commencez par créer deux groupes groupe1 et groupe2**


> On utilise les commandes suivantes 
```
adminsrv@serveur:~$ sudo addgroup groupe1
Adding group `groupe1' (GID 1001) ...
Done.
adminsrv@serveur:~$ sudo addgroup groupe2
Adding group `groupe2' (GID 1002) ...
Done.
adminsrv@serveur:~$

```

**2. Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de leur dossier personnel et avec bash pour shell**

> On créé les utilisateurs avec les commandes suivantes.

```
 sudo adduser u1 --home /home/u1 --shell $SHELL
 sudo adduser u2 --home /home/u2 --shell $SHELL
 sudo adduser u3 --home /home/u3 --shell $SHELL
 sudo adduser u4 --home /home/u4 --shell $SHELL

```

**3.  Ajoutez les utilisateurs dans les groupes créés :**
    
   **- u1, u2, u4 dans groupe1**
> On utilise les commandes suivantes 
```
adminsrv@serveur:~$ sudo usermod -a -G groupe1 u1
adminsrv@serveur:~$ sudo usermod -a -G groupe1 u2
adminsrv@serveur:~$ sudo usermod -a -G groupe1 u4

```

**-   u2, u3, u4 dans groupe2**
> On utilise les commandes suivantes 
```
adminsrv@serveur:~$ sudo usermod -a -G groupe2 u2
adminsrv@serveur:~$ sudo usermod -a -G groupe2 u3
adminsrv@serveur:~$ sudo usermod -a -G groupe2 u4

```

**4. Donnez deux moyens d’afficher les membres de groupe2**
> On utilise les commandes suivantes 
```
adminsrv@serveur:~$ grep "groupe2" /etc/group
groupe2:x:1002:u2,u3,u4

```

ou

```
adminsrv@serveur:~$ getent group groupe1
groupe1:x:1001:u1,u2,u4

```

**5. Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire de /home/u3 et /home/u4**
> On utilise les commandes suivantes 
```
adminsrv@serveur:/home$ sudo chgrp groupe1 u1 u2
adminsrv@serveur:/home$ sudo chgrp groupe2 u3 u4
adminsrv@serveur:/home$ ls -l
total 20
drwxr-xr-x 9 serveur serveur 4096 mars 13 16:43 serveur
drwxr-xr-x 2 u1      groupe1 4096 mars 13 14:06 u1
drwxr-xr-x 2 u2      groupe1 4096 mars 13 14:11 u2
drwxr-xr-x 2 u3      groupe2 4096 mars 13 14:12 u3
drwxr-xr-x 2 u4      groupe2 4096 mars 13 14:12 u4

```

**6. Remplacez le groupe primaire des utilisateurs :**
    
-   groupe1 pour u1 et u2
```
adminsrv@serveur:~$ sudo usermod u1 -g groupe1
adminsrv@serveur:~$ sudo usermod u2 -g groupe1
    
```
    
 -   groupe2 pour u3 et u4
    
    ```
    adminsrv@serveur:~$ sudo usermod u3 -g groupe2
    adminsrv@serveur:~$ sudo usermod u4 -g groupe2
    
    ```
    
**7. Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier associé.**
    
> On utilise les commandes suivantes 
```
adminsrv@serveur:/home$ sudo mkdir groupe1
adminsrv@serveur:/home$ sudo mkdir groupe2
adminsrv@serveur:/home$ sudo chgrp groupe1 groupe1
adminsrv@serveur:/home$ sudo chgrp groupe2 groupe2
adminsrv@serveur:/home$ ls -l
total 28
drwxr-xr-x 2 root    groupe1 4096 mars 13 14:39 groupe1
drwxr-xr-x 2 root    groupe2 4096 mars 13 14:39 groupe2
adminsrv@serveur:/home$ sudo chmod g+w groupe1
adminsrv@serveur:/home$ sudo chmod g+w groupe2
adminsrv@serveur:/home$ ls -l
total 28
drwxrwxr-x 2 root    groupe1 4096 mars 13 14:39 groupe1
drwxrwxr-x 2 root    groupe2 4096 mars 13 14:39 groupe2

``` 
 **8 Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier ?**

> On va ajouter un sticky bit au droits du dossier pour faire cela. On le fait via les commandes:

```
adminsrv@serveur:/home$ sudo chmod +t groupe1
adminsrv@serveur:/home$ sudo chmod +t groupe2

```

**9.Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?**

>Non, il n'est pas possible de se connecter en tant que u1 car nous n'avons pas encore activé l'utilisateur. On ne peut donc pas s'y connecter.

**10. Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son compte.**

```
adminsrv@serveur:/home$ usermod -U u1
```

**11. Quels sont l’uid et le gid de u1 ?**

```
u1@serveur:/home$ id
uid=1001(u1) gid=1001(groupe1) groups=1001(groupe1)

```

>u1 à pour uid 1001 et gid 1001.

**12. Quel utilisateur a pour uid 1003 ?**

```
u1@serveur:/home$ id 1003
uid=1003(u3) gid=1002(groupe2) groups=1002(groupe2)

```

>l'utilisateur qui à pour uid 1003 et l'utilisateur u3.

**13. Quel est l’id du groupe groupe1 ?**

```
u1@serveur:/home$ getent group groupe1
groupe1:x:1001:u1,u2,u4

```

> L'id du groupe 1 est le 1001.

**14. Quel groupe a pour guid 1002 ? ( Rien n’empêche d’avoir un groupe dont le nom serait 1002...)**

```
u1@serveur:/home$ getent group 1002
groupe2:x:1002:u2,u3,u4

```

> Le groupe qui à le guid 1002 est le groupe2.

**15. Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.**
> On ne peut pas car c'est son groupe primaire.   

**16. Modifiez le compte de u4 de sorte que : 
— il expire au 1er juin 2020
 — il faut changer de mot de passe avant 90 jours 
 — il faut attendre 5 jours pour modifier un mot de passe 
 — l’utilisateur est averti 14 jours avant l’expiration de son mot de passe 
 — le compte sera bloqué 30 jours après expiration du mot de passe**
    
> On utilise les commandes suivantes 
```
adminsrv@serveur:/home$ sudo chage -E 2010-06-01 u4
adminsrv@serveur:/home$ sudo chage -M 90 u4
adminsrv@serveur:/home$ sudo chage -m 5 u4
adminsrv@serveur:/home$ sudo chage -W 14 u4
adminsrv@serveur:/home$ sudo chage -l u4
Last password change                                    : mars 13, 2020
Password expires                                        : juin 11, 2020
Password inactive                                       : juillet. 11, 2020
Account expires                                         : juin 01, 2020
Minimum number of days between password change          : 5
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14

```

**17. Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?**
```
adminsrv@serveur:~$ getent passwd root
root:x:0:0:root:/root:/bin/bash

```

> L'interpréteur de commandes (Shell) de l'utilisateur root est /bin/bash.

**18. A quoi correspond l’utilisateur nobody ?**

>L'utilisateur nobody est utilisé par tout programme qui ne nécessite pas de permission particulière. Cela est particulièrement utile dans le cas de service qui peuvent être compromis (httpd par exemple). Cela permet de limiter les dégats.

**19. Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ? Quelle commande permet de forcer sudo à oublier votre mot de passe ? minutes par défaut.**

>Selon le manuel de la commande sudo, celle-ci conserve le mot de passe en mémoire pendant 15 minutes. Cela peux être réinitialiser avec la commande:

```
adminsrv@serveur:~$ sudo -K

```

## Exercice 2. Gestion des permissions

**1. Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques lignes de texte. Quels sont les droits sur test et fichier ?**

```
adminsrv@serveur:~$ ll
drwxrwxr-x  2 serveur serveur    4096 mars   13 11:56 test/

```

Test possède les droits suivants:

-   Utilisateur: Lecture, Écriture, Exécution
-   Groupe: Lecture, Écriture, Exécution
-   Autres: Lecture, Exécution

```
adminsrv@serveur:~/test$ ll
-rw-rw-r--  1 serveur serveur   48 mars 13 15:06 fichier

```

Pour le fichier, celui-ci possède les droits suivants:

-   Utilisateur: Lecture, Écriture
-   Groupe: Lecture, Écriture
-   Autres: Lecture

-   Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en tant que root. Conclusion ?

```
adminsrv@serveur:~/test$ sudo chmod 000 fichier
adminsrv@serveur:~/test$ sudo cat fichier
un fichier
avec des
lignes de 
texte
adminsrv@serveur:~/test$ sudo nano fichier
adminsrv@serveur:~/test$ sudo cat fichier
un fichier
avec des
lignes de 
texte
et une en plus

```

Les droits d'accès configurés ne s'applique pas au super utilisateur root.

-   Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un fichier s’il existe déjà. Que peut-on dire au sujet des droits ?

```
adminsrv@serveur:~/test$ sudo chmod 300 fichier
adminsrv@serveur:~/test$ echo "echo Hello" > fichier
adminsrv@serveur:~/test$ cat fichier
cat: fichier: Permission denied
adminsrv@serveur:~/test$ sudo cat fichier
echo Hello

```

Comme nous possédons le droit d'écriture sur le fichier, il est possible d'écrire dans celui-ci et donc de remplacer son contenu.

-   Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.

```
adminsrv@serveur:~/test$ ./fichier
bash: ./fichier: Permission denied
adminsrv@serveur:~/test$ sudo ./fichier
Hello

```

Comme nous n'avons pas les droits d'exécution, le système nous bloque. En revanche, comme root à tout les droits, cela ne le bloque pas.

-   Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ? Rétablissez le droit en lecture sur test

```
adminsrv@serveur:~/test$ sudo chmod u-r ../test
adminsrv@serveur:~/test$ ls
ls: cannot open directory '.': Permission denied
adminsrvserveur:~/test$ ./fichier
bash: ./fichier: Permission denied
adminsrv@serveur:~/test$  

```

Les droits sont testés au fur et à mesure des commandes. Et donc sans le droit de lecture nous ne pouvons pas afficher le contenu du dossier.

-   Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvez vous déduire de toutes ces manipulations ?

```
adminsrv@serveur:~/test$ nano nouveau
adminsrv@serveur:~/test$ mkdir sstest
adminsrv@serveur:~/test$ chmod u-w nouveau
adminsrv@serveur:~/test$ chmod u-w ../test
adminsrv@serveur:~/test$ nano nouveau
[ Error writing nouveau: Permission denied ]
adminsrv@serveur:~/test$ chmod u+w ../test
adminsrv@serveur:~/test$ nano nouveau
[ Error writing nouveau: Permission denied ] 
adminsrv@serveur:~/test$ rm nouveau
rm: remove write-protected regular file 'nouveau'? yes
adminsrv@serveur:~/test$ ls
fichier  sstest

```

Les fichiers n'héritent pas des droits d'un dossier, ils ont chacun des droits spécifiques.

-   Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?

```
adminsrv@serveur:~$ chmod u-x test
adminsrv@serveur:~$ nano test/nouveau
[ Path 'test' is not accessible ]
adminsrv@serveur:~$ cd test/
-bash: cd: test/: Permission denied
adminsrv@serveur:~$ ls test
ls: cannot access 'test/sstest': Permission denied
ls: cannot access 'test/fichier': Permission denied
fichier  sstest

```

Si une commande nécessite d'accéder au contenu d'un répertoire alors que le droit de lecture lui est retiré, il ne sera pas possible d'exécuter la commande.

-   Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd ..” ? Pouvez-vous donner une explication ?

```
adminsrv@serveur:~$ cd test/
adminsrv@serveur:~/test$ chmod u-x ../test
adminsrv@serveur:~/test$ nano nouveau
[ Path '.': Permission denied ]
adminsrv@serveur:~/test$ cd sstest
-bash: cd: sstest: Permission denied
adminsrv@serveur:~/test$ ls
ls: cannot open directory '.': Permission denied
adminsrv@serveur:~/test$ cd ..
adminsrv@serveur:~$ 

```

Les droits du répertoire courant sont aussi importants que si l'on essayait de faire les mêmes opérations depuis un répertoire parent. Comme pour la question précédente, les droits du dossier sont pris en compte pour chaque commande nécessitant d'utiliser le contenu d'un dossier.

-   Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.

```
adminsrv@serveur:~/test$ chmod 650 fichier
adminsrv@serveur:~/test$ ll
total 16
drwxr-xr-x  3 serveur serveur 4096 mars 13 12:30 ./
drwxr-xr-x 10 serveur serveur 4096 mars 13 11:56 ../
-rw-r-x---  1 serveur serveur   11 mars 13 12:12 fichier*
drwxrwxr-x  2 serveur serveur 4096 mars 13 12:21 sstest/

```

-   Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture, ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.

```
adminsrv@serveur:~$ touch fichier
adminsrv@serveur:~$ mkdir folder
adminsrv@serveur:~$ umask 177
adminsrv@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 mars 13 12:47 ./
drwxr-xr-x  9 root    root       4096 mars 13 14:39 ../
-rw-------  1 serveur serveur       0 mars 13 12:47 fichier
drw-------  2 serveur serveur    4096 mars 13 12:47 folder/

```

-   Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.

```
adminsrv@serveur:~$ umask 011
adminsrv@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 mars 13 12:47 ./
drwxr-xr-x  9 root    root       4096 mars 13 14:39 ../
-rwxr-xr-x  1 serveur serveur       0 mars 13 12:47 fichier*
drwxr-xr-x  2 serveur serveur    4096 mars 13 12:47 folder/
adminsrv@serveur:~$  

```

-   Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.

```
adminsrv@serveur:~$ umask 037
adminsrv@serveur:~$ ll
total 3728
drwxr-xr-x 10 serveur serveur    4096 mars 13 12:47 ./
drwxr-xr-x  9 root    root       4096 mars 13 14:39 ../
-rwxr-----  1 serveur serveur       0 mars 13 12:47 fichier*
drwxr-----  2 serveur serveur    4096 mars 13 12:47 folder/
serveur@serveur:~$  

```

-   Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous pourrez vous aider de la commande stat pour valider vos réponses) :
    
    -   chmod u=rx,g=wx,o=r fic  `534`
    -   chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---  `501`
    -   chmod 653 fic en sachant que les droits initiaux de fic sont 711  `517`
    -   chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---  `520`
-   Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier /etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?
    

```
serveur@serveur:~/folder$ ll /etc/passwd
-rw-r--r-- 1 root root 1801 sept. 30 15:08 /etc/passwd

```
En temps normal, la lecture du fichier passwd devrait être limité aux utilisateurs avec des privilèges suffisants.
