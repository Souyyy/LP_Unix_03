# Compte Rendu TP03

## Exercice : Paramètre

```
#!/bin/bash
#Paramètre

if [ $# -gt 0 ]
then
    # Bloc de code à exécuter si la condition est vraie
    echo "Bonjour, vous avez rentré $# paramètres."
    echo "Le nom du script est $0"
    echo "Le 3ème paramètre est $3"
    echo "Voici la liste des paramètres $@"
else
    # Bloc de code à exécuter si la condition est fausse
    echo "Il n'y a pas de parametre."
fi
```

## Exercice : Vérification du nombre de paramètres

```
#!/bin/bash
# Vérification du nombre de paramètres

if [ $# -eq 2 ];
then
    # Bloc de code à exécuter si la condition est vraie
    CONCAT="$1$2"
    echo "Voici la concaténation: $CONCAT"
else
    # Bloc de code à exécuter si la condition est fausse
    echo "Vous devez saisir 2 paramètres"
    exit 1
fi
```

## Exercice : Argument type et droits

```
#!/bin/bash
# Argument type et droits

utilisateur=$(whoami)
valeur_perm=""

if [ $# -eq 1 ];
then
    if [ -e $1 ];
    then
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

```
#!/bin/bash
# Afficher le contenu d'un repertoire

if [ $# -eq 1 ];
then
    if [ ! -d $1 ];
    then
        echo "Vous devez saisir un repertoire."
    else
        echo "####### fichier dans $1/"
        find "$1" -maxdepth 1 -type f 
        echo "####### repertoires dans $1/"
        find "$1" -maxdepth 1 -type d
    fi
else
    echo "Vous devez saisir 1 paramètre."
    exit 1
fi
```

## Exercice : Lister les utilisateurs

```
#!/bin/bash
# Lister les utilisateurs

if [ -r /etc/passwd ];
then
    while read -r line; do
        utilisateur=$(echo "$line" | cut -d: -f1)
        uid=$(echo "$line" | cut -d: -f3)
        if [ "$uid" -gt 100 ]; then
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

