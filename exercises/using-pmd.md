# Using PMD

Pick a Java project from Github (see the [instructions](../sujet.md) for suggestions). Run PMD on its source code using any ruleset. Describe below an issue found by PMD that you think should be solved (true positive) and include below the changes you would add to the source code. Describe below an issue found by PMD that is not worth solving (false positive). Explain why you would not solve this issue.

You can use the default [rule base](https://github.com/pmd/pmd/blob/master/pmd-java/src/main/resources/rulesets/java/quickstart.xml) available on the source repository of PMD.

## Answer

Exo 2: Using PMD
On a utilisé PMD sur apache/commons-maths, l’output comporte de nombreuses erreurs.
une erreur true positif : 
/home/theo/eclipse-workspace/TP_VV/commons-math/commons-math-core/src/main/java/org/apache/commons/math4/core/jdkmath/AccurateMath.java:4070:    CloseResource:    Ensure that resources like this PrintStream object are closed after use


public static void main(String[] a) 
  PrintStream out = System.out; 
  AccurateMathCalc.printarray(out," 
  AccurateMathCalc.printarray(out,,

Le printstream utilisé ici est ouvert mais n’est pas fermé après la fin de son utilisation. Ce problème ne donne pas de problème d'exécution mais peut poser des problèmes de mémoire ou lors d’une nouvelle ouverture.
une erreur false positive : /home/theo/eclipse-workspace/TP_VV/commons-math/commons-math-core/src/main/java/org/apache/commons/math4/core/jdkmath/AccurateMath.java:396:    UselessParentheses:    Useless parentheses.
return (0.5 * t) * t; -> return 0.5 * t * t;
On a donc des parenthèses inutiles qu’il suffirait d’enlever mais la présence de cette parenthèse n’affecte pas la bonne exécution du code, il n’est donc pas nécessaire de résoudre ce problème. De plus, la présence ou l’absence des parenthèses ne rend pas le code plus lisible.
