# Rapport du projet

Lien du développement du projet : [Jeu de dames · GitLab INSA Lyon](https://gitlab.insa-lyon.fr/kloumida/jeu-de-dames)
Lien du design : [Jeu de dames - Figma](https://www.figma.com/file/m8VwZRUorLVr325wRjFDpI/Jeu-de-dame?node-id=0%3A1)

## Contributeurs

**Clément CHAPARD** : La graphique, le design et la langue et la base des règles.

Clément est expérimenté en pygame. C'est lui qui propose de l'utiliser pour réaliser la graphique et l'interface. Grâce à ça, Jixiang a pu concentrer sur le codage des règles. La graphique facilite vraiment beaucoup le débogage. 

Il a fait presque tous les fonctionnement supplémentaire comme le son, la langue et le record. Hors cela, il participe aussi dans le noyau du programme. Par exemple, c'est lui qui a déterminé l'allure de la fonction juger.

**SUN Jixiang** : Réalisation des règle, Connexion entre le noyau et la graphique.

Le point fort de Jixiang est de reformuler les codes et de les rendre plus structurés. C'est lui qui propose d'utiliser une classe pour emballer tous les fonctions et les variable liées au jeu. Il a décomposé les règles cas par cas et a accompli des parties très importantes comme la fonction `juger` et réalisation du `mode repas`.

## Installation

Ce programme ne comporte qu'un fichier du code, mais l'interface graphique dépend de la libraire `pygame`. Pour l'installer :

```bash
pip install pygame
```

Et pour jouer, il suffit d'exécuter :

```bash
python "Jeu de dame.py"
```

## Structure globale du code

Le programme a trois partie: 

* La définition de la classe **Pion**, où on définit les propriétés d'un pion: sa couleur et s'il est une dame ou pas.
* La définition de la classe **Game**. C'est le noyau du programme, elle contient toutes les variables importantes et les fonctions qui interprètent le règle du jeu.
* **Une boucle while**. C'est le code 'principale' qui entraine le programme et fait les appels aux fonction dans la classe Game et aux fonction de l'interface graphique.

## Fonctions et Variables

La majorité de fonctions sont dans la classe Game.

`Game.__init__(self)` : chaque fois un objet de Game créé, cette fonction fait l'initialisation: initialisation de GUI, de damier , de son......

`Game.affichage(self)` : L'affichage de la console utilisé au début du développement. A ce moment-là on a pas encore l'interface graphique. Elle affiche les pion dans la console. Ici on profite des charactères chinois qui sont carrées. "**黑 白 王 玉**" représentent respectivement le pion noir, blanc et la dame noire et blanche. On peut les voir aujourd'hui quand même dans la console.

`Game.move(self,x:int,y:int,n_x:int,n_y:int)` : Le but de cette fonction est simple : déplacer un pion. Les paramètres sont les coordonnées de pion à déplacer et les nouvelles coordonnées. Cette fonction doit être appelée après la fonction `Game.juger`.

`Game.test_pion_manger(self,x,y)` et `Game.mes_pions_peuvent_manger(self)`: On teste si **un** pion particulier peut manger, et s'il existe un pion parmi **tous** les pion d'un coté peut manger. Ces fonctions servent à la réalisation du règle: Si la case est occupée par un pion adverse et que la case suivante (dans la diagonale) est libre, le pion **doit** capturer ce pion en le “sautant”. 

`Game.juger(self,x:int,y:int,n_x:int,n_y:int)->int` : c'est la fonction la plus importante de ce programme. Elle prend aussi les coordonnées de pion à déplacer et les nouvelles coordonnées, et retourne un entier qui indique si une action est réalisable ou pas:

* -1: L'action n'est pas valide.
* 0: L'action est un déplacement et ce déplacement et possible.
* 1: L'action est une capture et cette capture et possible.

Il y a beaucoup de raisons pour lesquelles une action n'est pas valide. Comme le pion ne suit pas une diagonale ou le joueur essaie de déplacer un pion adverse. 

La fonction `Game.juger` est un peu compliquée. Elle traite d'abord les cas généraux, c'est à dire les règles qui appliquent sur tous les pions et dames(règle de diagonale par exemple). Et elle traite ensuite les cas plus particuliers(C'est un pion ou une dame? Le joueur veut déplacer ou capturer?).

`aller_possible(self,x,y)` : On teste si un pion/une dame a la possibilité de bouger. Cette fonction sert à la détermination de la victoire. Si aucun pion/ aucune dame sur place du joueur ne peut bouger, adversaire gagne.

`Game.new_action(self,x,y,n_x,n_y)` : Elle réalise une nouvelle action. Elle réagit selon de différents cas. Affichage un message en cas l'action impossible, prendre un pion en cas de capturer...... Elle est chargée aussi le control du '**mode repas**'. C'est réalisation du règle: Si la capture place le pion dans une situation de capture, la capture continue.

`affichage_gui(self)` : permet d'afficher la partie : des boucles créent le damier et y placent les pions

`main_menu_gui(self, click)` : affiche le menu principal en affichant les différents boutons et élément de design. Pour les clics, on utilise des collisions avec les différents boutons, ce qui permet d'accéder au reste du programme.

`setting_gui(self, click, event)` : affiche les paramètre du jeu : activer ou désactiver le son, la musique ou l'écran de présentation (ce dernier paramètre nécessite un redémarrage de l'application). Les changements sont inscrits dans un fichier externe (Settings.txt) afin d'être conservé au redémarrage (attention, cela peut prendre quelque seconde, ne pas immédiatement redemarrer le programme après avoir fait un changement, sinon cela risque de ne pas être pris en compte)

`lang_gui(self, click, event)` : permet de changer la langue du programme. Pour cela, on stock toute le texte affiché dans 2 fichier de langue que l'on importe sous forme de liste. Le paramètre change un autre fichier, lang.txt, qui stock la langue à importer au démarrage.

## La boucle principale

On a d'abord la fonction `pygame.display.flip()` qui permet de raffraichir l'affichage.
Ensuite, la boucle `for event in pygame.event.get():` est parcouru chaque fois qu'un évènement est détecté par pygame, et cette évènement sera nommé `event`.
Cela permet ainsi de définir la fermeture du jeu quand on ferme la fenêtre, ainsi que le rôle de la touche Echap (retour au menu principal ou quitter).
La boucle est ensuite découpé en différentes `user_view`, correspondant à la partie du programme dans laquelle nous nous trouvons.
Chacune d'entre elle renvoie aux fonctions d'affichage associés. Dans la `user_view` 1, correspondant à la phase de jeu, on doit également afficher les messages. On utilise un algorithme qui permet un retour à la ligne si un message est trop long. Si il n'y a pas victoire, on détecte les clics et la position de la souris pour permettre la sélection/désélection des pions, et on met un effet blanc quand la souris passe au dessus d'un pion que l'on a la droit de jouer, ainsi que sur le pion sélectionné.

## Point d'améliorations

Afin améliorer le programme, nous pourrions permettre de réinitialiser une partie sans avoir à redémarrer le jeu. Nous pourrions également afficher les différents coups possible. On pourrait également imaginer un mode de jeu contre l'ordinateur, ou encore plus avancé, un mode de jeu en réseau.
