# Le 6502:
Le 6502 est un microprocesseur purement 8 bits et ne dispose que de registres 8 bits(sauf un), à l'exception du pointeur d'instruction, ce qui est très limitant et limité même pour l'époque. De plus, le jeu d'instructions est extrêmement limité, avec tout juste ce qu'il faut pour réaliser l'ensemble des opérations basiques.

Ça ne fait pas beaucoup, les autres microprocesseurs en ont souvent beaucoup plus. Par exemple, le préhistorique 4004 en avait 16, le Z80 aussi ou presque. La famille du 6502 fait donc exception sur ce point.

Si on a besoin de stocker des valeurs, on utilisera donc souvent la mémoire RAM (un peu plus lente) ou la pile (encore plus lente).

## Syntaxe:

En 6502 il y a 4 "espaces" qui ne sont pas tous obligatoire
* Le premier sert de balise pour permettre les jump dans le code (facultatif) c'est le nom de l'ancre qui permettra le saut fini par :
* Le second est l'opcode/instructions (toujours en 3 lettres en 6502)
* Le troisième le ou les paramètres si il y en a
* Le dernier est un commentaire faculatatif mais fortement conseillé commence toujours par ;

Exemple:

* test: LDA $2812 ;transfert une donnée de l'adresse $2812 vers le registre A
* LDX #42
* LDX #42 ;Cette ligne est équivalente à celle au dessus mais avec un commentaire

## Les registres:
Le 6502 compte en tout et pour tout 6 registres assez spécialisés, ce qui permet de tester les données de façon efficace.
### Le registre PC:
PC est Le compteur de programme. Il s'agit du seul registre 16 bits du 6502. Il contient l'adresse de l'instruction (opcode) en cours.

On ne peut évidemment pas utiliser ce registre pour stocker quoi que ce soit, on ne peut d'ailleurs pas directement connaître sa valeur. Il s'incrémente tout seul à chaque instruction exécutée, et ne peut être sinon modifié qu'avec des instructions de saut.

### Le registre S:
S est le pointeur de pile. Le 6502 a une toute petite pile, permettant de stocker les adresses de retour des fonctions ou d'autres données.

La valeur maximale de ce registre est 255 et il est décrémenté à chaque empilage de données (deux fois pour une adresse). On peut changer la valeur de ce registre, mais pas lire sa valeur.
### Le registre P
P est le registre des flags (drapeaux). Ce registre contient l'état actuel du processeur (quelles options sont actives), ainsi que des bits précisant le résultat de certaines opérations.

Certains bits de ce registre peuvent être manipulés à la main : ceux qui concernent les options du processeur. Les autres sont positionnés automatiquement par des instructions de comparaison (mais pas uniquement). On utilise généralement le contenu de ce registre pour effectuer des sauts conditionnels (voir plus bas).

Les bits de ce registre sont les suivants :

* Le bit 0, C, est le bit de retenue, il est utilisé dès que le résultat d'une opération ne tient pas sur 8 bits, ce qui arrive assez souvent. On peut aussi changer la valeur de ce bit, en particulier à l'aide des instructions CLC et SEC. L'état de ce bit est souvent testé avec les opérations de branchement BCC et BCS.
* Le bit 1, Z, est le bit nommé « Zéro ». Il est modifié par beaucoup d'opérations. Il est mis à 1, si le résultat d'une opération est 0 et à 1, sinon. On notera l'humour des concepteurs du 6502 de choisir le bit numéro 1 de ce registre comme « Zéro », dont le but est justement d'être à 1 pour une opération qui renvoie 0 et vice-versa.
* Le bit 2, I est le bit d'interruption. Ce bit permet d'activer ou de désactiver les interruptions, à la main, il n'y a pas d'instruction qui le modifie directement, en dehors de CLI et SEI.
* Le bit 3, D est le bit « Decimal ». Lorsque ce bit est positionné, les additions et soustractions (les instructions ADC et SBC) considèrent que chaque octet est composé de deux quartets (4 bits), dont la valeur ne va que de 0 à 9. Dans ce mode, la valeur représentée par un octet va donc de 0 à 99, ce qui peut être pratique pour certains calculs. Ce n'est toutefois pas souvent utilisé. Ce bit n'existe pas sur la NES.
* Le bit 6, V est le bit « oVerflow » (dépassement). Ce bit n'est modifié que par ADC et SBC. Il est mis à 1 uniquement si le résultat d'une opération signée ne tient pas dans l'intervalle [-128, 127].
* Le bit 7, N est le bit négatif. Il est positionné à 1 si une opération amène à une valeur dont le bit 7 est à un (supérieure à 127).
* Les bits 4 et 5 ne sont pas utilisés.

###Le registre A
C'est l'accumulateur ou l'accu (la partie permettant les opérations). On peut écrire des données dans ce registre, les lire, faire des additions, des soustractions, des opérations bit à bit (ou, et, ou exclusif, décalages) et des comparaisons.

Il s'agit du registre que l'on utilisera le plus.

###Les registres X et Y


Ce sont deux registres d'index qui ont presque le même rôle. On peut stocker et lire des données avec ces registres, mais les seules opérations arithmétiques que l'on peut leur appliquer sont l'incrémentation et la décrémentation.

De plus, on peut les utiliser dans des modes d'adressage spéciaux, pour lire ou écrire des données dans un tableau. Ils sont donc parfaits pour faire des parcours de tableaux, des copies, des recherches, etc.

## Les instructions:
Les instructions ou opcode sont les "opération" en assembleur toujours en 3 lettre pour le 6502 
### Copie de données
Il y a trois classes d'opérations de copie : soit d'un registre à l'autre, soit d'un registre vers la mémoire, soit de la mémoire vers un registre. Le 6502 différencie ces trois classes directement dans le nom de l'instruction, et propose pas moins de 12 instructions de copie (sur les 56 qu'il possède en tout !)
#### Copie/transfert de données entre registres 
Elles commencent toutes par la lettre T (de transfert) même si ce n'est qu'une copie.
La seconde lettre correspond au registre d'origine de la donnée.
La dernière lettre correspond au registre cible pour la donnée.

* TAX copie de A -> X
* TAY copie de A -> Y
* TSX copie de S -> X
* TXA copie de X -> A
* TXS copie de X -> S
* TYA copie de Y -> A

/!\ Pas de copie entre les registres X et Y
/!\ Seul X peut échanger sa valeur avec S

### Copie/sauvegarde de données vers la mémoire 
Elles commencent toutes par les lettres ST (sauvegarde), elles sont au nombre de 3 et proviennent des registres A,X et Y.
	
* STA copie de A -> mémoire
* STX copie de X -> mémoire
* STY copie de Y -> mémoire
	
Exemple: STA $1138 ;stockera la valeur du registre A à l'emplacement mémoire 1138 (en hexadécimal).

### Copie de données vers les registres
Elles commencent toutes par les lettres LD(pas encore de moyen mnémontechnique), elles sont au nombre de 3 et vont aux registres A,X et Y.
En paramètre ont peut mettre soit une adresse mémoire soit une valeur 

* LDA copie de mémoire/valeur -> A
* LDX copie de mémoire/valeur -> X
* LDY copie de mémoire/valeur -> Y

Exemple :
* LDA $2812 ;transfert depuis l'adresse 2812 vers A
* LDX #42   ;transfert la valeur 42 dans X


# La NES:

##Fonctionnement du joypad:


	Sur le Joypad il y a 8 boutons, (A,B,Select, Start, Haut, Bas, Gauche, Droite) pour récupérer l'état des boutons il faut:
		1. mettre 1 puis 0 (le 1 et 0 indique qu'on lance la lecture) à l'adresse 4016 qui correspond à l'espace d'adressage de l'APU( ce qui prend en charge les manettes et les transfert DMA).
		2. A chaque lecture de l'adresse 4016 renverra 1 (si le bouton est pressé) ou 0 sinon
	Donc pour avoir l'état des 8 boutons il faut lire 8 fois à l'adresse 4016, ils n'ont pas mis tous les boutons sur 1 octets pour des questions de cours (c'est + chère en électronique de séparer) et pour une plus grande adaptibilité des manettes
