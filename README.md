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

```

## Exercice : Afficher le contenu d'un repertoire

```

```

## Exercice : Lister les utilisateurs

```

```

## Exercice : Mon utilisateur existe t'il ?

```

```

## Exercice : Création utilisateur

## Exercice : Lecture au clavier

## Exercice : Appréciation

