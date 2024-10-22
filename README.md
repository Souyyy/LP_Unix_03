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

else
    echo "Vous devez saisir 1 paramètre."
    exit 1
fi
```

## Exercice : Création utilisateur

```

```

## Exercice : Lecture au clavier

```

```

## Exercice : Appréciation

```

```

