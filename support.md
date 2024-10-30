
Introduction


Pour mon usage personnel

Bash est un langage rustique (1989)

Il y a plusieurs facons d'arriver à ses fins

On peut s'amuser avec. Grâce au chainage avec les |pipes|


Plan minimal - 30 mn


1. tac

tac est l'inverse de cat

tac fichier

il commence par la dernière ligne d'un fichier
ou d'une liste

for i in $jours_semaine
  do
    echo $i
  done       | tac


2. file

file scanne l'entête d'un fichier et essaie de déterminer son type

file untruc_enpiecejointe_sanssuffixe

bonus : il analyse l'encodage pour les fichiers textes

3. rename

rename permet de renommer en masse une liste de fichiers

moins ambigu, plus efficace que mv (1 fichier à la fois)

4. tar

tar est un outil d'archivage

il "emballe" les dossiers à sauvegarder dans un seul fichier

travaille de concert avec un compacteur (gzip,bzip)

tar xf projet-006.tar.gz

5. cut

cut est le canif du bash

il coupe toutes les lignes qu'on lui donne

echo "koala lémurien oppossum zèbre" | cut -b-5

echo "" | rev | cut -b-5 | rev

echo "" | cut -f3 -d' '

6. paste

colle deux fichiers texte ligne par ligne

insère un séparateur au milieu (par défaut TAB 0x09)

paste fichier1.txt fichier2.txt > fichier3.txt

ls -la | paste lignage.txt -

7. uniq

uniq supprime toutes les lignes en doublon dans une liste triée

sort liste_de_prenoms.txt | uniq  # sort -u

bonus : uniq -c affiche le dénombrement de la liste

cat fichier-base.csv | cut -f3 | uniq -c

8. diff

diff analyse les différences entre deux fichiers texte

diff lastest_script.sh previous_script.sh

diff lastest_script.sh previous_script.sh | grep '^> ' | cut -b3-

9. xargs récupère la sortie standard poour construire une commande shell

echo "$mois1 $mois2 $mois3" | xargs mkdir -v

ls *.zip | xargs -n 1 unzip -qt

bonus : xargs -I R

10. wget

wget télécharge un fichier avec l'URL en argument

warning : https://tserong.github.io/sudo-wget/
wget -q -O - http://tserong.github.io/sudo-wget/install | sudo bash
cousin : curl

commandes simples (seulement sur support)

basename et dirname

dossier_parent=$(dirname $chemin | xargs basename)

dos2unix et unix2dos

dos2unix script.sh
dos2unix *.txt

Les paramètres :

Commandes avec les options essentielles

grep -o
grep -l
grep -A n
grep -B n
grep -c
grep -m 1

    # cherche le nom de l'utilisateur en début de ligne / renvoie 0 ou 1
    existe=$(grep -c ^"$utilisateur": /etc/passwd)

sort -g
sort -k 2 -t ';'

find ./ -path "*chemin*" ! -name "chemin"
find ./ -type f || find -type d
find ./ -type f -exec less {} \;


You can go there (mais pas tout de suite)

awk
sed

regex !


