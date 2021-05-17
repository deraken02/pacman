# Le 6502:
==========
Le 6502 est un microprocesseur purement 8 bits et ne dispose que de registres 8 bits, à l'exception du pointeur d'instruction, ce qui est très peu même pour l'époque. De plus, le jeu d'instructions est extrêmement limité, avec tout juste ce qu'il faut pour réaliser l'ensemble des opérations basiques.
## Les registres:
=================

## Les instructions:
====================


# La NES:
=========
##Fonctionnement du joypad:
===========================

	Sur le Joypad il y a 8 boutons, (A,B,Select, Start, Haut, Bas, Gauche, Droite) pour récupérer l'état des boutons il faut:
		1. mettre 1 puis 0 (le 1 et 0 indique qu'on lance la lecture) à l'adresse 4016 qui correspond à l'espace d'adressage de l'APU( ce qui prend en charge les manettes et les transfert DMA).
		2. A chaque lecture de l'adresse 4016 renverra 1 (si le bouton est pressé) ou 0 sinon
	Donc pour avoir l'état des 8 boutons il faut lire 8 fois à l'adresse 4016, ils n'ont pas mis tous les boutons sur 1 octets pour des questions de cours (c'est + chère en électronique de séparer) et pour une plus grande adaptibilité des manettes
