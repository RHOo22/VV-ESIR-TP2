# TCC *vs* LCC

Explain under which circumstances *Tight Class Cohesion* (TCC) and *Loose Class Cohesion* (LCC) metrics produce the same value for a given Java class. Build an example of such as class and include the code below or find one example in an open-source project from Github and include the link to the class below. Could LCC be lower than TCC for any given class? Explain.

A refresher on TCC and LCC is available in the [course notes](https://oscarlvp.github.io/vandv-classes/#cohesion-graph).

## Answer

Le TCC et le LCC d’une classe peuvent avoir une valeur similaire lorsque les membres de la classe ont beaucoup d'interactions entre elles, cela implique de nombreuses interactions directes et également indirectes, ce qui donne une LCC et une TCC qui peut être proche voir égale.
Voici notre classe:

﻿

package TP2_exo1;
public class rectangle { 
  int largeur;
  int longueur; 
  int changement;

  rectangle(int larg, int lon) { largeur = larg;
    longueur = lon;
    changement = 0;
  }
  public int perimetre () { return longueur + largeur; }
  
  public void setlong (int lon) {
    longueur = lon;
    changement ++;
  }
  public void setlarge (int larg)
    largeur = larg;
    changement ++;
  }


Dans cette classe on a trois méthodes qui utilisent chacune deux attribut de la classe, on peut observer trois lien direct ce qui donne un TCC de 1 et également un LCC de 1 puisque les trois méthodes sont de toute façon liée entre elles. 
Le LCC ne peut pas être inférieur au TCC puisque le TCC compte tous les chemin direct et le LCC compte tous les chemins donc les chemin direct et indirect d’ou LCC = TCC + (les chemin indirect) / (le nombre de paire de méthode). Et donc LCC >= TCC.
