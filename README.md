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
