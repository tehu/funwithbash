# Fun with Bash

## Introduction

- Découvrir (ou redécouvrir) de nouvelles commandes
- Basées sur mon usage personnel
- Il y plusieurs manières d'arriver à ses fins
- Bash est un langage rustique (1989), mais très orienté sur les fichiers et les flux

On peut s'amuser avec. Grâce au chaînage avec les pipes.

https://github.com/tehu/funwithbash

__(3 parties - avec pause sur chaque tiers)__
## 10 Commandes qui peuvent rendre service

1. **tac**

tac est l'inverse de cat

Il commence par la dernière ligne d'un fichier.

```bash
tac fichier
```

ou d'une liste

```bash
for i in $jours_semaine
  do
    echo $i
  done       | tac
```


2. **file**

file scanne l'entête d'un fichier et essaie de déterminer son type
```bash
file un_truc_en_piece_jointe_sans_suffixe
```
bonus : il analyse l'encodage des caractères pour les fichiers texte.


3. **prename**

prename permet de renommer en masse une série de fichiers

mv a deux rôles : déplacer des fichiers et renommer __un__ document

prename est moins ambigu, plus efficace que mv
```bash
prename "s/recherche/remplace/" *.md
```
commande non standard - plusieurs versions (p pour PERL)
```bash
sudo apt install rename     # paquet à installer sous ubuntu
alias rename='prename'
```

on peut tester à blanc un renommage avec l'option -n
```bash
prename -n "s/[.]doc/.md/" notes*.doc
```
avant de le visualiser avec -v
```bash
prename -v "s/[.]doc/.md/" notes*.doc
```
bonus : il peut utiliser les méta-caractères comme dans grep : ^ * + . [ ] $


4. **tar**

tar est un outil d'archivage

il "emballe" les dossiers à sauvegarder dans un seul fichier

travaille de concert avec un compacteur (gzip,bzip)

> décompresse et "dé-tar" un fichier 

```bash
tar xf projet-006.tar.gz
```


5. **cut**

cut est le canif du bash

il coupe toutes les lignes qu'on lui donne

2 méthodes :
- par octet (byte) : **-b**
* par champ (field) : -d
* on définit une plage :  [DEBUT-FIN]
```
# extrait les 5 premiers caractères de la
echo "koala kangourou oppossum tapir" | cut -b-5
# extrait les 5 derniers caractères
echo "koala kangourou oppossum tapir" | rev | cut -b-5 | rev
# extrait le 3ème champ, séparé par un espace
echo "koala kangourou oppossum tapir" | cut -f3 -d' '
```
avec un fichier
```
cut -f3 -d; < fichier.csv
```


6. **paste**

paste colle deux fichiers texte ligne par ligne

insère un séparateur au milieu (par défaut TAB 0x09)
```bash
paste fichier1.txt fichier2.txt > fichier3.txt
```

```bash
ls -la | paste lignage.txt -
```



7. **uniq**

uniq supprime toutes les lignes en doublon dans une liste triée

```bash
sort liste_de_prenoms.txt | uniq      # équivaut à : sort -u
```

bonus : **uniq -c** affiche le recensement de la liste

```bash
cat fichier-base.csv | cut -f2 -d; | uniq -c
```


8. **diff**

diff analyse les différences entre deux fichiers texte

```bash
diff script.sh old_script.sh
```
on peut filtrer les blocs qui ont changé avec grep```

``` bash
diff script.sh old_script.sh | grep '^> '
```
puis enlever les chevrons

``` bash
diff script.sh old_script.sh | grep '^> ' | cut -b3-
```

9. **xargs**

xargs récupère la sortie standard pour construire une commande shell

```bash
mois1='mai' ; mois2='juin' ; mois3='juillet'
# xargs insère les arguments après la commande mkdir
echo "$mois1 $mois2 $mois3" | xargs mkdir -v
```
Autre exemple : un client vous envoie des archives sous format zip. Vous voulez testez leur intégrité.

La commande `unzip -[q]t` ne permet de tester qu'un fichier à la fois.
```bash
# avec une boucle for
for i in *.zip
  do
    unzip -qt $i
  done
# avec xargs
ls *.zip | xargs -n 1 unzip -qt
```
bonus :
```bash
xargs -I R commande R autres_parametres
```

10. **wget**

wget télécharge un fichier avec l'URL en argument

wget est récursif : wget -r document.html 
```bash
# warning : https://tserong.github.io/sudo-wget/
wget -q -O - http://tserong.github.io/sudo-wget/install | sudo bash
```
cousin : **curl**

## Commandes simples (uniquement sur le support)

* basename et dirname

basename $ressource
> affiche le nom de la ressource

dirname $ressource
>  affiche le chemin de la ressource

``` bash
# récupère dans une variable le dossier parent d'un document
dossier_parent=$(dirname $chemin | xargs basename)
```
 
 
 * dos2unix et unix2dos

Si vous jonglez souvent entre Windows et Linux, vous devez vérifier vos fichiers textes.

**Historiquement**, Windows code les fins de ligne sur 2 caractères -CRLF-

Linux (Unix) sur 1 seul caractère -LF-.

```
# convertit toutes les fins de ligne CR-LF en LF
dos2unix script.sh
```
Votre IDE préféré fait naturellement la conversion. Mais on n'est jamais  à l'abri d'un glitch. Vérifiez dans ses paramétrages que l'IDE sauvegarde par défaut en -LF-.

| Unicode (ASCII) | CR   | LF   |
| --------------- | ---- | ---- |
| Décimal         | 13   | 10   |
| Hexa            | 0xd  | 0xa  |
| Shell           | "\r" | "\n" |

## Options essentielles (find sort grep)

Ces commandes sont faciles à assimiler.

Mais elles acceptent un nombre conséquent d'options.

``` bash
# cherche les fichiers dont le path comporte chemin
# mais qui ne s'appelle pas chemin (!) est un inverseur
find ./ -path "*chemin*" ! -name "chemin"

# type f (file) ou d (directory)
find ./ -type f
find ./ -type d

# exécute une commande avec chaque fichier trouvé
find ./ -type f -exec less {} \;
```

```bash
# tri par clé numérique
sort -g

# sort par champ (-k) délimités par un caractère (-t)
sort -k 2 -t ';'
```

``` bash
# affiche juste le motif cherché
grep -o
# affiche le numéro de ligne
grep -l

# affiche le contexte en nombre de lignes
grep -A n # AFTER
grep -B n # BEFORE

# affiche le recensement des motifs (0 si vide)
grep -c
# donne une limite maximum des occurences
grep -m 1
```

```bash
# cherche le nom de l'utilisateur en début de ligne / renvoie 0 ou 1
existe=$(grep -c ^"$utilisateur": /etc/passwd)
```

## You can go there (mais pas tout de suite)

Deux commandes qui évoluent dans le même domaine.

**awk**

**sed**

Le formatage de fichiers ou de chaines de caractères.
> recherche/remplacement, filtrage, insertion, suppression
```bash
sed -i.bak "s/octobre/novembre/g" lettre_de_relance.md
```
### regex !
regular expressions  // expressions rationnelles

