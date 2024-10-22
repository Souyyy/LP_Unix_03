# Compte Rendu TP03

## Exercice : Paramètre

**Explication du script** : Ce script vérifie si des paramètres ont été passés lors de son exécution. Il utilise la variable `$#`, qui représente le nombre total de paramètres fournis. Si ce nombre est supérieur à 0, le script affiche plusieurs informations nottament le nombre de parametres, le nom du script avec `$0`, le troisième paramètre avec `$3`, et la liste complète des paramètres avec `$@`. Si aucun paramètre n’est ajouté, le script affiche un message indiquant qu'aucun paramètre n'a été fourni. 

```
#!/bin/bash
#Paramètre

# Vérifie si des parametres ont ete passes au script
if [ $# -gt 0 ]
then
    # Si oui, affiche le nombre total de paramètres, le nom du script, le 3eme parametre, et la liste des parametres
    echo "Bonjour, vous avez rentré $# paramètres."
    echo "Le nom du script est $0"
    echo "Le 3ème paramètre est $3"
    echo "Voici la liste des paramètres $@"
else
    # Si non, afficher un message d'erreur
    echo "Il n'y a pas de parametre."
    exit 1
fi
```

## Exercice : Vérification du nombre de paramètres

**Explication du script** : Ce script vérifie d'abord si deux paramètres ont été passés lors de son exécution. Pour verifier ceci on utilise le `if` et `-eq`pour `equal`. Si c'est le cas, alors on fait la concaténation des 2 paramètres (`$1`et `$2`) qui sont enregistrer dans une variable qu'on affiche dans un echo. Si ce n'est pas le cas, alors on affiche un message d'erreur et on sort du programme avec un exit 1 qui renvoie une erreur.

```
#!/bin/bash
# Vérification du nombre de paramètres

# Vérifie si 2 parametres ont ete passes au script
if [ $# -eq 2 ];
then
    # Si la condition est vrai, on concatène les deux parametres
    CONCAT="$1$2"
    echo "Voici la concaténation: $CONCAT"
else
    # Si la condition est fausse, affiche un message d'erreur
    echo "Vous devez saisir 2 paramètres"
    exit 1
fi
```

## Exercice : Argument type et droits

**Explication du script** : Ce script vérifie d'abord si un paramètre a été passé lors de son exécution. Si c'est le cas on verifie si ce paramètre et un fichier (`-e`) sinon on regarde si il s'agit d'un repertoire ou autre, Une fois qu'on s'assure qu'il s'agit d'un fichier on regarde si le fichier contient des informations (`-s`) et on renvoie un message qui le precise. Par la meme condition on verfie les permissions du fichier par l'utilisateur (dans notre cas `root`) pour cette verification on verifie `-r` (lecture) ,`-w` (write) et `-x` (éxecution) et enfin on affiche les permissions du fichier.

```
#!/bin/bash
# Argument type et droits

utilisateur=$(whoami)
valeur_perm=""

if [ $# -eq 1 ];
then
    # Vérification si c'est un fichier
    if [ -e $1 ];
    then
        # Vérification si le fichier et vide ou non
        if [ -s $1 ];
        then
            echo "Le fichier $1 est un fichier ordinaire qui n'est pas vide."
        else
            echo "Le fichier $1 est un fichier ordinaire qui est vide."
        fi

        # Vérification des permissions
        if [ -r "$1" ]; then
            valeur_perm+="lecture, "
        fi
        if [ -w "$1" ]; then
            valeur_perm+="écriture, "
        fi
        if [ -x "$1" ]; then
            valeur_perm+="exécution"
        fi

        # Affichage des permissions si elles existent
        if [ -n "$valeur_perm" ]; then
            echo "\"$1\" est accessible par $utilisateur en $valeur_perm."
        else
            echo "$1 n'est pas accessible par $utilisateur."
        fi

    # Vérification si le parametre est un repertoire ou autre
    elif [ -d $1 ];
    then
        echo "Le paramètre $1 est un répertoire."
    else
        echo "Le paramètre $1 est un lien symbolique ou un type spécial."
    fi
    
else
    echo "Vous devez saisir 1 paramètre."
fi
```

## Exercice : Afficher le contenu d'un repertoire

**Explication du script** : Ce script vérifie d'abord si un paramètre a été passé lors de son exécution. Si c'est le cas on verifie si le parametre est un dossier (`if [ ! -d $1 ];`) Si ce n'est pas le cas on affiche un message qui demande a l'utilisateur d'ajouter un repertoire. Si il s'agit d'un repertoire on affiche les fichier (`-type f`) du repertoire avec `find` associé avec le parametre `$1` ainsi que de `-maxdepth 1` qui sert à ne pas afficher les sous repertoires. On fait egalement la meme chose pour les dossier en changeant uniquement le type par (`-type d`).

```
#!/bin/bash
# Afficher le contenu d'un repertoire

# Verifier si il y a un parametre associé.
if [ $# -eq 1 ];
then
    # Verifier si le parametre est un dossier
    if [ ! -d $1 ];
    then
        echo "Vous devez saisir un repertoire."
    else
        # Affiche les informations des fichiers
        echo "####### fichier dans $1/"
        find "$1" -maxdepth 1 -type f
        # Affiche les informations des dossiers
        echo "####### repertoires dans $1/"
        find "$1" -maxdepth 1 -type d
    fi
else
    echo "Vous devez saisir 1 paramètre."
    exit 1
fi
```

## Exercice : Lister les utilisateurs

**Explication du script** : 

```
#!/bin/bash
# Lister les utilisateurs

if [ -r /etc/passwd ];
then
    while read -r line;
    do
        utilisateur=$(echo "$line" | cut -d: -f1)
        uid=$(echo "$line" | cut -d: -f3)
        if [ "$uid" -gt 100 ];
        then
            echo "$utilisateur : $uid"
        fi
    done < /etc/passwd
else
    echo "Le fichier /etc/passwd n'est pas lisible."
    exit 1
fi
```

```
#!/bin/bash
# Lister les utilisateurs

if [ -r /etc/passwd ];
then
    awk -F: '$3 > 100 { print $1 }' /etc/passwd
else
    echo "Le fichier /etc/passwd n'est pas lisible."
    exit 1
fi
```

## Exercice : Mon utilisateur existe t'il ?

**Explication du script** : 

```
#!/bin/bash
# Lister les utilisateurs

if [ $# -eq 1 ];
then
    # Si un nom de login est passé
    if [[ "$1" =~ ^[a-zA-Z0-9._-]+$ ]];
    then
        # Rechercher le login dans le fichier /etc/passwd
        utilisateur=$(grep "^$1:" /etc/passwd)
        if [ -n "$utilisateur" ];
        then
            uid=$(echo "$utilisateur" | cut -d: -f3)
            echo "L'utilisateur '$1' existe avec l'UID : $uid"
        fi
    
    # Si un UID est passé
    elif [[ "$1" =~ ^[0-9]+$ ]];
    then
        utilisateur=$(awk -F: -v uid="$1" '$3 == uid { print $0 }' /etc/passwd)
        if [ -n "$utilisateur" ];
        then
            login=$(echo "$utilisateur" | cut -d: -f1)
            echo "L'utilisateur avec l'UID '$1' est : $login"
        fi
    
    else
        echo "L'argument doit etre un login ou un UID valide."
        exit 1
    fi
else
    echo "Vous devez saisir 1 paramètre."
    exit 1
fi
```

## Exercice : Création utilisateur

**Explication du script** : 

```
#!/bin/bash
# Création utilisateur

# Vérifier si le script est execute par root
if [ "$USER" != "root" ];
then
    echo "Le script doit etre execute en tant que root."
    exit 1
fi

# Demander les informations pour l'utilisateur
read -p "Entrez le login de l'utilisateur : " login
read -p "Entrez le nom de famille : " nom
read -p "Entrez le prenom : " prenom
read -p "Entrez l'UID : " uid
read -p "Entrez le GID : " gid
read -p "Entrez des commentaires : " commentaires

# Verifier si le login existe deja
if grep "^$login:" /etc/passwd > /dev/null;
then
    echo "Erreur : Un utilisateur avec le login '$login' existe déjà."
    exit 1
fi

# Vérifier si l'UID existe deja
if awk -F: -v uid="$uid" '$3 == uid { exit 0 }' /etc/passwd;
then
    echo "Un utilisateur avec l'UID '$uid' existe deja."
    exit 1
fi

# Verifier si le repertoire home existe deja
if [ -d "/home/$login" ];
then
    echo "Erreur : Le repertoire /home/$login existe deja."
    exit 1
fi

# Créer l'utilisateur avec useradd
useradd -u "$uid" -g "$gid" -c "$nom $prenom, $commentaires" -m -d "/home/$login" -s /bin/bash "$login"

# Vérifier si la creation a reussi
if [ $? -eq 0 ]; then
    echo "L'utilisateur '$login' a ete cree avec succes."
    echo "Détails :"
    echo "Login : $login"
    echo "Nom : $nom"
    echo "Prénom : $prenom"
    echo "UID : $uid"
    echo "GID : $gid"
    echo "Commentaires : $commentaires"
else
    echo "Impossible de créer l'utilisateur."
    exit 1
fi
```

## Exercice : Lecture au clavier

**Explication du script** : 

```
#!/bin/bash
# Lecture au clavier

if [ $# -eq 1 ];
then
    if [ ! -d $1 ];
    then
        echo "Vous devez saisir un repertoire."
    else
        # Parcourir chaque fichier du répertoire spécifié
        for fichier in "$1"/*; do
            # Utiliser la commande file pour vérifier s'il s'agit d'un fichier texte
            if file "$fichier" | grep -q "text"; then
                # Demander à l'utilisateur s'il souhaite visualiser le fichier
                echo -n "Voulez-vous visualiser le fichier '$fichier' ? (oui/non) : "
                read reponse
                if [ "$reponse" = "oui" ]; then
                    more "$fichier"
                fi
            fi
        done
    fi
else
    echo "Vous devez saisir 1 paramètre."
    exit 1
fi
```

## Exercice : Appréciation

**Explication du script** : 

```
#!/bin/bash
# Appreciation

while true;
do
    # Demander la note à l'utilisateur
    echo -n "Entrez une note (ou appuyez sur q pour quitter) : "
    read note

    # Vérifier si l'utilisateur veut quitter
    if [ "$note" = "q" ];
    then
        echo "Vous avez quitté le programme."
        exit 0
    fi

    # Vérifier si l'entrée est un nombre valide
    if [[ "$note" =~ ^[0-9]+$ ]];
    then
        note=$(($note)) # Convertir la chaîne en entier

        # Afficher un message en fonction de la note
        if [ "$note" -ge 16 ] && [ "$note" -le 20 ];
        then
            echo "Très bien"
        elif [ "$note" -ge 14 ] && [ "$note" -lt 16 ];
        then
            echo "Bien"
        elif [ "$note" -ge 12 ] && [ "$note" -lt 14 ];
        then
            echo "Assez bien"
        elif [ "$note" -ge 10 ] && [ "$note" -lt 12 ];
        then
            echo "Moyen"
        elif [ "$note" -lt 10 ];
        then
            echo "Insuffisant"
        fi
    else
        # Gerer les cas d'entrées non valides
        echo "Erreur : veuillez entrer un nombre valide ou q pour quitter."
    fi
done
```

