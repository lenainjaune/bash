RO en attente de la fin de migration


























































# bash
HowTo bash

Ici seront conservées les versions les plus portables et lisibles pour éviter un maximum un environnement précis et/ou des dépendances.

# Source script
```sh
user@host:~$ echo "Source: "`whoami`"@"`hostname`":"${BASH_SOURCE[0]}
Source: root@host:/my/script/path/script_name.sh
user@host:~$ echo "File: " $( basename -- ${BASH_SOURCE[0]} )
File: script_name.sh
```

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
Nota : cette forme supporte mal les multiples commandes, en particulier si on utilise une variable d'environnement dans un sous bloc. Ainsi A=... ; [[ ... ]] && ( A=... ;  ) fonctione, mais A du sous bloc n'écrasera pas A externe
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
# bashrc
Nota : pour le rendre permanent le mettre à la fin du .bashrc du user tout en s'assurant que le script n'est pas quitté plus tôt
```sh
# Prompt en couleur selon user (les variables couleurs servent de commentaire puisqu'elles sont vides)
PS1="\[\033$( [[ $USER == root ]] && echo $rouge '[0;91m' \
|| ( [[ $USER == lnj ]] && echo $jaune '[01;33m' || echo $vert '[01;32m' ) )\]\u@\h:\w\[\033[00m\]\\\$ "

# Historique
# Ignorer les commentaires de code et les commandes précédées par 1,n espaces
# Ignorer les commandes identiques successives
# Conserver plus de commandes
# Conserver historique quand on ouvre un shell depuis un autre
export HISTIGNORE="# *"
export HISTCONTROL="ignorespaces:ignoredups"
export HISTSIZE=10000
shopt -s histappend
```
# heredoc

TODO : indentation écriture non prise en compte à l'effet (espaces, tabs), conserver en l'état / ne PAS substituer (expander ?)

Dans une variable : [ici](https://stackoverflow.com/questions/1167746/how-to-assign-a-heredoc-value-to-a-variable-in-bash)
