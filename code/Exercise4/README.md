import com.github.javaparser.StaticJavaParser;
import com.github.javaparser.ast.CompilationUnit;
import com.github.javaparser.ast.body.ClassOrInterfaceDeclaration;
import com.github.javaparser.ast.body.FieldDeclaration;
import com.github.javaparser.ast.body.MethodDeclaration;

import java.io.File;
import java.io.IOException;
 
public class GetterChecker {

    public static void main(String[] args) {
        // Spécifiez le chemin du fichier source Java que vous souhaitez analyser
        //File file = new File("~/eclipse-workspace/TP_VV/commons-math/common-math-core/src/main/java~/eclipse-workspace/TP_VV/commons-math/commons-math-core/src/main/java/org/apache/commons/math4/core/jdkmath/AccurateMath.java");
        File file = new File("~/eclipse-workspace/TP_VV/commons-math/person.java");

        try {
            // Parse le fichier source Java
            CompilationUnit cu = StaticJavaParser.parse(file);

            // Récupère la déclaration de classe
            ClassOrInterfaceDeclaration classDeclaration = cu.getClassByName("Person").orElse(null);

            if (classDeclaration != null) {
                // Récupère la liste des champs de la classe
                classDeclaration.getFields().forEach(field -> {
                    // Récupère le nom du champ
                    String fieldName = field.getVariable(0).getNameAsString();

                    // Génère le nom du getter attendu
                    String getterName = "get" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);

                    // Vérifie si un getter avec le nom attendu existe
                    boolean hasGetter = classDeclaration.getMethodsByName(getterName).stream()
                            .anyMatch(method -> method.isMethodDeclaration() && method.asMethodDeclaration().getType().isPresent());

                    // Affiche le résultat
                    System.out.println("Le champ '" + fieldName + "' a un getter : " + hasGetter);
                });
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
