# bash
HowTo bash

Ici seront conservées les versions les plus portables et lisibles pour éviter un maximum un environnement précis et/ou des dépendances.

# HH:MM:SS en S
source : https://stackoverflow.com/questions/2181712/simple-way-to-convert-hhmmss-hoursminutesseconds-split-seconds-to-seconds/48032950#48032950
```sh
function hms2s () {
	echo $1 | awk -F\: '{ for(k=NF;k>0;k--) sum+=($k*(60^(NF-k))); print sum }'
}
echo $( hms2s "05:10" )
echo $( hms2s "02:05:10" )
echo $( hms2s "00:05:10" )
```
# test if ... elif ... else ... fi
Remarque : seules les clauses `if` sont obligatoires
## Forme non nommée iniligne
```sh
# if ... then ... fi
#  chaines
[[ $( command ) ]] && echo "seulement si la commande renvoie quelque chose"
r=$( command ) ; [[ $r ]] && echo "seulement si la commande renvoie quelque chose"
[[ $( command ) == "" ]] && echo "seulement si la commande ne renvoie RIEN"
#  numériques
[[ $( command_return_number ) -gt val ]] && echo "OK : > val"
# nota : -eq (equal), -ne (not eq), -gt (greater than), -lt (lesser than), -ge (greater eq), -le (lesser eq)
# if ... then ... else ... fi
[[ $( command ) ]] && echo "si true" || echo "si false"
```
## Forme nommée

```sh
# if ... then ... fi
if [[ $( command1 ) ]] ; then
 echo if match
elif [[ $command2 ) ]] ; then
 echo elif match
else
 echo else match
fi
```
Nota : la clause elif peut être réitéré autant qu'on veut
### iniligne
```sh
if [[ $( command ) ]] ; then echo true ; else echo false ; fi
```
# Exécuter script durant exécution d'une commande (sortie stdout)
Par exemple pour exécuter un script intégré durant l'exécution d'un chroot, ssh ou su

Forme générale :
```sh
cat << EOF | commande \
             qui peut être multilignes
 commande
 # commentaire $var_pre_expanded \$var_post_expanded
 #             \char_pre_escaped \\char_post_escaped
EOF
# Attention : EOF DOIT commencer une nouvelle ligne et DOIT être suivi par un saut de ligne
```
C'est une manière pratique d'écrire un script à exécuter durant l'exécution d'une commande

Exemple pour **chroot**
```sh
o_lsblk=$( lsblk ) chroot /mnt/dev/sdb1 bash -c "echo \"\$o_lsblk\" ; lsblk"
# équivalent en PLUS lisible et pratique
cat << EOF | o_lsblk=$( lsblk ) \
             chroot /mnt/dev/sdb1
 echo "\$o_lsblk"
 lsblk
EOF
```
Exemple pour **ssh**
```sh
ssh root@buster-glpi.local bash -c "blkid ; ip ad"
# vs
cat << EOF | ssh user@host.local bash
 blkid
 ip ad
EOF
```
# Détecter si une commande échoue même si elle est piped
```sh
root@host:~# cat backup.sh
# Permet de prendre en compte la première erreur d'une commande pipée
# if ! ( a | b ) ; then echo KO ; exit ; fi
# si a ou b échoue => KO (sans, seul b serait pris en compte)
# voir aussi : https://coderwall.com/p/fkfaqq/safer-bash-scripts-with-set-euxo-pipefail
set -o pipefail
...
if ! ( a | b ) ; then
echo KO
exit 1
fi
```
