# Utilisation de gdb

## Lancer GDB

Commencer par lancer gdb avec son programme

```sh
gdb programme
```

Puis dans gdb

```gdb
run <paramètres>
```

Un exemple possible de sortie de GDB est :

```gdb
Program received signal SIGSEGV, Segmentation fault.
0x000000000040111c in incrementation (a=0x0) at foo.c:6
6         *a=5;
```

## Une fois que GDB à fini l'execution du programme

On lance une analyse de l'erreur de segmentation

```gdb
backtrace
```

En restant reprenant l'exemple précendent backtrace retourne

```gdb
(gdb) backtrace
#0  0x000000000040111c in incrementation (a=0x0) at foo.c:6
#1  0x0000000000401150 in main () at foo.c:14
```

On regarde le retour de backtrace et ajout d'un breakpoint sur la fonction qui merde, ici c'est la fonction incrementation à la ligne 6 du fichier foo.c, donc :

```gdb
break foo.c:6
```

Les paramètre de break indique le fichier ou faire le breakpoint puis la ligne, dans cette exemple on créer un breakpoint dans le fichier foo.c à la ligne 6.

```gdb
Breakpoint 1 at 0x401118: file foo.c, line 6.
```

GDB nous dit que le breakpoint à bien était placé.

## Puis on relance l'exécution du programme dans GDB

```gdb
run <paramètres>
```

On as un retour comme quoi le programme est déjà lancé, et nous demande si on souhaite le relancé depuis le début. On lui dit que oui ('y').

On obtiens donc quelque-chose comme suit

```gdb
Starting program: /home/benny/Documents/01 - Projets/foo

Breakpoint 1, incrementation (a=0x0) at foo.c:6
6         *a=5;
```

On peut donc voir que lorsque l'on appel la fonction incrementation, le pointeur 'a' pointe sur une adresse nul, et donc est la source de notre erreur de segmentation. Si cela ne se voit pas aussi simplement on peut se balader utiliser la commande :

```gdb
print variable
```

qui permet de nous afficher la valeur de la variable, dans notre exemple `print a` retourne :

```gdb
(gdb) print a
$1 = (int *) 0x0
```

ont voit bien que a est un pointeur qui pointe sur une adresse nul.
Mais on peut aussi demander à GDB de nous afficher le contenue à l'adresse de a, la syntaxe est la même qu'en C

```gdb
(gdb) print *a
Cannot access memory at address 0x0
```

On ajoute '\*' devant à pour lui demander la valeur à l'adresse que pointe a.
On remarque que GDB nous dit qu'il est impossible d'acceder à cette adresse. C'est donc bien le pointeur a qui pose soucis.
Dans le même principe on peut affiché l'adresse du pointeur a avec '&':

```gdb
(gdb) print &a
$2 = (int **) 0x7fffffffd9e8
```

On obtiens donc l'adresse ou est stocké le pointeur a.

## Et voilà

Maintenant il ne reste plus qu'à modifier son code en conséquence pour que a arrête de faire planter le programme.

## Le programme utilisé pour cette exemple :

```c
#include <stdio.h>
#include <stdlib.h>

int incrémentation(int *a)
{
  *a=5;
  return *a;
}

int main()
{
  int *a;
  a=0;
  fonction(a);
  return 0;
}
```

Réponse : ici on affect 0 à 'a' ce qui donne un pointeur a l'adresse 0x0, qui est l'erreur que nous ressort GDB.
Et sinon GOOGLE est ton ami.

Petit récap des commande utile avec GDB :

- run <paramètre>
- backtrace
- break programme.c:ligne
- print variable
