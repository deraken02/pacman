	;; L'entête pour les émulateurs
	DC.B "NES", $1a 	;L'entête doit toujours commencer ainsi
	DC.B 1			;Le nombre de boitiers de 16 ko de ROM CPU (1 ou 2)
	DC.B 1			;Le nombre de boitiers de 8 ko de ROM PPU
	DC.B 0			;Direction du scrolling et type de cartouche ici le type le plus simple donc 0 et c'est un scrolling vertical donc 0 aussi
	DS.B 9, $00		;9 zéro pour faire 16 en tout

	;; Le joypad va controler manuellement le scrolling on automatisera après.
	;; On vérifie si les boutons Haut ou Bas sont pressés
	;; Plus de détails dans le fichier notice.md
	JOYPAD1 EQU $4016 	;On récupère l'état des huit boutons séquentiellement à l'adresse 4016
	LDA #1			;On réinitialise la lecture du Joypad
	STA JOYPAD1		;En écrivant un 1
	LDA #0			; suivi d'un 0
	STA JOYPAD1		;  dans cette adresse

	;; Maintenant on peut lire l'état de chaque bouton du joypad:
	LDA JOYPAD1		;Lecture du bouton A
