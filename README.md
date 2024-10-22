
Compte Rendu : TP 03 - Scripts Bash

1. Script analyse.sh

Objectif : J’ai créé un script pour afficher des informations sur les paramètres passés lors de l’exécution. Il affiche le nombre de paramètres, le nom du script, et le 3ème paramètre si applicable.

Commandes utilisées :

    #!/bin/bash
    echo "Bonjour, vous avez rentré $# paramètres."
    echo "Le nom du script est $0"
    if [ $# -ge 3 ]; alors
      echo "Le 3ème paramètre est $3"
    fi
    echo "Voici la liste des paramètres : $@"

Test et Résultat :
J’ai testé avec trois paramètres :

    ./analyse.sh param1 param2 param3

Le résultat :

    Bonjour, vous avez rentré 3 paramètres.
    Le nom du script est ./analyse.sh
    Le 3ème paramètre est param3
    Voici la liste des paramètres : param1 param2 param3

Le script fonctionne  en affichant correctement les paramètres.

2. Script concat.sh

Objectif : Ce script permet de concaténer deux chaînes passées en paramètres. J’ai d’abord vérifié qu’il y a bien deux paramètres, puis je les ai concaténés.

Commandes utilisées :

    #!/bin/bash
    if [ $# -ne 2 ]; alors
      echo "Vous devez entrer exactement 2 paramètres."
      exit 1
    fi
    CONCAT="$1$2"
    echo "La concaténation est : $CONCAT"
    
    Test et Résultat :
    En le testant sur mon Mac avec les paramètres bonjour et tout_le_monde :
    
    ./concat.sh bonjour tout_le_monde

Le résultat est :

    La concaténation est : bonjourtout_le_monde

Tout fonctionne.

3. Script test-fichier

Objectif : J’ai conçu ce script pour tester si un fichier ou un répertoire existe et pour vérifier ses permissions .

Commandes utilisées :

    #!/bin/bash
    if [ -f "$1" ]; then
      echo "$1 est un fichier ordinaire."
    elif [ -d "$1" ]; then
      echo "$1 est un répertoire."
    else
      echo "$1 n'existe pas."
    fi
    
    if [ -r "$1" ]; alors
      echo "$1 est lisible."
    fi
    if [ -w "$1" ]; alors
      echo "$1 est modifiable."
    fi
    if [ -x "$1" ]; alors
      echo "$1 est exécutable."
    fi

Test et Résultat :
J’ai testé ce script sur un fichier que j’avais créé, test.txt :

    ./test-fichier test.txt

Le résultat :

    test.txt est un fichier ordinaire.
    test.txt est lisible.
    test.txt est modifiable.
    test.txt est exécutable.

Cela fonctionne.

4. Script listdir.sh

Objectif : Ce script liste les fichiers et répertoires d’un dossier donné.

Commandes utilisées :

    #!/bin/bash
    echo "####### fichiers dans $1"
    for fichier in "$1"/*; do
      if [ -f "$fichier" ]; then
        echo "$fichier"
      fi
    done
    
    echo "####### répertoires dans $1"
    for fichier in "$1"/*; do
      if [ -d "$fichier" ]; alors
        echo "$fichier"
      fi
    done

Test et Résultat :
J’ai utilisé ce script pour lister le contenu de mon dossier Documents :

    ./listdir.sh ~/Documents

Le script renvoie :

    ####### fichiers dans /Users/moi/Documents
    /Users/moi/Documents/rapport.docx
    ####### répertoires dans /Users/moi/Documents
    /Users/moi/Documents/Projets

Le script fonctionne  et sépare bien les fichiers et répertoires.

5. Lister les utilisateurs avec UID supérieur à 100

Objectif : Afficher les utilisateurs dont l’UID est supérieur à 100, généralement les utilisateurs non-système.

Commande utilisée :

    awk -F: '$3 > 100 {print $1}' /etc/passwd

Test et Résultat :
En exécutant cette commande, j’obtiens :

    ./list-users.sh

Le résultat :

    moi
    guest
    autreuser

Cela fonctionne  pour afficher les utilisateurs du système.

6. Vérification de l’existence d’un utilisateur

Objectif : J’ai écrit ce script pour vérifier l’existence d’un utilisateur donné.

Commande utilisée :

    #!/bin/bash
    if id "$1" &>/dev/null; then
      echo "L'utilisateur $1 existe avec l'UID $(id -u $1)."
    else
      echo "L'utilisateur $1 n'existe pas."
    fi

Test et Résultat :
J’ai vérifié un utilisateur existant sur la VM :

    ./check-user.sh moi

Le résultat :

    L'utilisateur moi existe avec l'UID 501.

Cela fonctionne comme attendu.

7. Création d’un utilisateur

Objectif : Ce script permet de créer un utilisateur après vérification de son existence.

Commandes utilisées :

    #!/bin/bash
    if [ "$USER" != "root" ]; alors
      echo "Vous devez être root pour créer un utilisateur."
      exit 1
    fi
    
    read -p "Login : " login
    if id "$login" &>/dev/null; alors
      echo "L'utilisateur existe déjà."
      exit 1
    fi
    
    read -p "Nom : " nom
    read -p "Prénom : " prenom
    read -p "UID : " uid
    read -p "GID : " gid
    read -p "Commentaire : " commentaire
    
    useradd -u "$uid" -g "$gid" -c "$commentaire" -m "$login"

Test et Résultat :
En l’exécutant, j’ai créé un utilisateur test :

    ./create-user.sh

Les informations fournies :
    
    Login : testuser
    Nom : Nazimbis
    Prénom : Haddad
    UID : 1005
    GID : 100
    Commentaire : Utilisateur de test

L’utilisateur a été créé avec succès sur le système.

8. Lecture clavier avec read

Objectif : Ce script demande à l’utilisateur d’entrer un nom, puis affiche ce nom.

Commande utilisée :

    #!/bin/bash
    echo -n "Entrez votre nom : "
    read nom
    echo "Votre nom est $nom"

Test et Résultat :
En testant ce script :

    ./read.sh

J’ai entré mon nom, et le résultat affiché est :

    Entrez votre nom : Jean
    Votre nom est Jean

Cela fonctionne parfaitement.

9. Affichage des fichiers texte

Objectif : Le script propose de visualiser les fichiers texte d’un répertoire.

Commandes utilisées :

    #!/bin/bash
    for fichier in "$1"/*; do
      if file "$fichier" | grep -q "text"; alors
        read -p "Voulez-vous visualiser $fichier ? (y/n) " reponse
        if [ "$reponse" = "y" ]; alors
          more "$fichier"
        fi
      fi
    done

Test et Résultat :
En exécutant ce script dans mon répertoire Documents :

    ./view-text-files.sh ~/Documents

Le script me demande si je veux visualiser certains fichiers :

    Voulez-vous visualiser /Users/moi/Documents/note.txt ? (y/n) y

Cela fonctionne.

10. Script de notation

Objectif : Ce script affiche une appréciation en fonction de la note entrée par l’utilisateur.

Commandes utilisées :

    #!/bin/bash
    while true; do
      read -p "Entrez une note (q pour quitter) : " note
      if [ "$note" = "q" ]; alors
        break
      elif [ "$note" -ge 16 ]; alors
        echo "Très bien"
      elif [ "$note" -ge 14 ]; alors
        echo "Bien"
      elif [ "$note" -ge 12 ]; alors
        echo "Assez bien"
      elif [ "$note" -ge 10 ]; alors
        echo "Moyen"
      else
        echo "Insuffisant"
      fi
    done

Test et Résultat :
En testant ce script :

    ./notation.sh

J’ai entré différentes notes :
    
    Entrez une note (q pour quitter) : 15
    Bien
    Entrez une note (q pour quitter) : 8
    Insuffisant
