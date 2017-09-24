# CommandLine

Framework for building command-line user interfaces. Through menus and options, it allows developpers to quickly develop a back-end user interface.

# How to use it

The framework organises the user dialog between menus and options. It allows the user to navigate through menus. 
In each menu, the user choses an option, the selection of an option triggers an action.

## Menus and options

In the following example, we show how to create a menu, and how to include an option in this menu.
 
```
// Creates a menu with the title Hello
Menu menu = new Menu("Hello Menu");
// Creates an option with the title "Say Hello", the shorcut to select it is "h"
Option sayHelloOption = new Option("Say Hello", "h");
// Adds the option sayHello to the menu.
menu.add(sayHelloOption);
// Adds the option allowing to close the menu.
menu.addQuit("q");
// Creates an action that will be binded to the sayHello option. 
Action sayHelloAction = new Action()
{
	// optionSelected() is triggered each time the "sayHello" option is selected.
	@Override
	public void optionSelected()
	{
		System.out.println("Hello !");
	}
};
// Binds sayHelloAction to the option sayHelloOption 
sayHelloOption.setAction(sayHelloAction);
// Launches the menu
menu.start();
```
  
The previous code displays the following menu.
  
```
Hello Menu

h : Say Hello
q : Exit

Select an option : 
```

Inputing the shorcut "h" selects the option "Say Hello", and then inputing "q" closes the application.
You can notice that selecting the option "Say Hello" automatically triggers the method 'optionSelected()'. 

## Nesting menus

Vous avez aussi la possibilité d'imbriquer un menu dans un menu, 
comme dans l'exemple ci-dessous :

```
// Création du menu racine de l'application.
Menu menuPrincipal = new Menu("Menu Principal");
// Création de deux options
Option calculatrice = new Option("Calculatrice", "c");
Menu direBonjour = new Menu("Menu bonjour", "Bonjour", "b");
// Imbrication des deux options dans le menu
menuPrincipal.ajoute(calculatrice);
// Vous remarquez que comme Menu hérite de Option, on peut mettre un menu dans un menu
menuPrincipal.ajoute(direBonjour);
menuPrincipal.ajouteQuitter("q");
// Définition de l'action pour la calculatrice
calculatrice.setAction(new Action()
{
	// Méthode exécutée lorsque l'option calculatrice est sélectionnée.
	public void optionSelectionnee()
	{
		int a = utilitaires.EntreesSorties.getInt("Saisissez la première opérande : "),
		b = utilitaires.EntreesSorties.getInt("Saisissez la deuxième opérande : ");
		System.out.println("" + a + " + " + b + " = " + (a+b));
	}
});
// Il est possible de passer l'action en paramètre directement dans le constructeur.
direBonjour.ajoute(new Option("Dire bonjour", "b", new Action()
{
	public void optionSelectionnee()
	{
		System.out.println("Bonjour !");
	}
}));
// Ajout d'une option permettant de revenir au menu parent
direBonjour.ajouteRevenir("r");;
// Retour automatique au menu parent si une option est exécutée.
direBonjour.setRetourAuto(true);
// Lancement du menu
menuPrincipal.start();
```

Voici un exemple d'exécution du programme :

```
Menu Principal
c : Calculatrice
b : Bonjour
q : Quitter
c
Saisissez la première opérande : 5
Saisissez la deuxième opérande : 6
5 + 6 = 11
Menu Principal
c : Calculatrice
b : Menu bonjour
q : Quitter
b
Menu bonjour
b : Dire bonjour
r : Revenir
b
Bonjour !
Menu Principal
c : Calculatrice
b : Menu bonjour
q : Quitter
q
```

## Lists

La librarie permet aussi de créer automatiquement un menu en utilisant une 
liste :

```
// Création d'une liste contenant les trois chaînes "Ginette", "Marcel" et "Gisèle"
final ArrayList&lt;String&gt; personnes = new ArrayList<>();
personnes.add("Ginette");
personnes.add("Marcel");
personnes.add("Gisèle");
// Création d'un menu proposant une option par personne
Liste&lt;String&gt; menu = new Liste&lt;String&gt;("Liste des Personnes", 
	new ActionListe&lt;String&gt;()
	{
		// Retourne la liste des personnes formant le menu
		public List&lt;String&gt; getListe()
		{
			return personnes;
		}

		// Exécutée automatiquement lorsqu'un élément de liste est sélectionné
		public void elementSelectionne(int indice, String element)
		{
			System.out.println("Vous avez sélectionné "+ element+ ", qui a l'indice " + indice);
		}
		
		// Retourne l'option que l'on souhaite créer, null si l'on préfère que l'option soit 
		// crée automatiquement.
		public Option getOption(String element)
		{
			return null;
		}

	});
// Ajoute une option quitter à la fin de la liste
menu.ajouteQuitter("q");
// Lancement du menu
menu.start();
```

Voici un exemple d'exécution : 

```
1 : Ginette
2 : Marcel
3 : Gisèle
q : Quitter
2
Vous avez sélectionné Marcel, qui a l'indice 1
```

(Attention, dans java.util.List les indices commencent à 0. Mais ils sont 
affichés en partant de l'indice 1.

## Code organisation

Il est conseillé, pour clarifier le code, de répartir les 
instructions dans des fonctions de la façon suivante :

```
static Menu getMenuPrincipal()
{
	Menu menuPrincipal = new Menu("Menu Principal");
	menuPrincipal.ajoute(getOptionCalculatrice());
	menuPrincipal.ajouteQuitter("q");
	return menuPrincipal;
}

static Option getOptionCalculatrice()
{
	Option calculatrice = new Option("Calculatrice", "c", getActionCalculatrice());
	return calculatrice;
}

static Action getActionCalculatrice()
{
	return new Action()
	{
		public void optionSelectionnee()
		{
			int a = utilitaires.EntreesSorties.getInt("Saisissez la première opérande : "),
				b = utilitaires.EntreesSorties.getInt("Saisissez la deuxième opérande : ");
			System.out.println("" + a + " + " + b + " = " + (a+b));
		}
	};
}
	
static Action getActionDireBonjour()
{
	return new Action()
	{
		public void optionSelectionnee()
		{
			System.out.println("Bonjour !");
		}
	};
}

static Option getOptionDireBonjour()
{
	return new Option("Dire bonjour", "b", getActionDireBonjour());
}
	
static Menu getMenuDireBonjour()
{
	Menu direBonjour = new Menu("Menu bonjour", "bonjour", "b");
	direBonjour.ajoute(getOptionDireBonjour());
	direBonjour.ajouteRevenir("r");;
	direBonjour.setRetourAuto(true);
	return direBonjour;
}
	
public static void main(String[] args)
{
	Menu menu = getMenuPrincipal();
	menu.start();
}
```

## Nesting options in lists

Vous avez aussi la possibilité d'inclure des options dans des listes.

```
public static void main(String[] args)
{
	List<String> personnes = new ArrayList<>();
	personnes.add("Ginette");
	personnes.add("Marcel");
	personnes.add("Gisèle");
	Liste<String> liste = getListePersonne(personnes);
	liste.start();
} 
// Retourne La liste à afficher
private static Liste<String> getListePersonne(final List<String> personnes)
{
	Liste<String> liste = new Liste<>("Choisissez une personne pour l'afficher", 
		getActionListePersonnes(personnes));
	return liste;
}

private static ActionListe<String> getActionListePersonnes(final List<String> personnes)
{
	return new ActionListe<String>()
	{
		// Retourne les éléments affichés dans le menu.
		public List<String> getListe()
		{
			return personnes;
		}

		// Vide, car on souhaite créer manuellement chaque option.
		public void elementSelectionne(int indice, String element){}

		// Retourne l'option associée à element.
		public Option getOption(final String personne)
		{
			// Crée une option, le raccourci est laissé null car il sera écrasé par l'indice
			return new Option("Afficher " + personne, null, new Action()
			{
				// Action exécutée si l'option est sélectionnée.
				public void optionSelectionnee()
				{
					System.out.println("Affichage de " + personne);
				}
			});
		}
	};
}
```

## Lambda expressions

It is strongly advised to use lambda expressions. 
