# Code of your exercise

import com.github.javaparser.StaticJavaParser;
import com.github.javaparser.ast.CompilationUnit;
import com.github.javaparser.ast.Modifier;
import com.github.javaparser.ast.body.FieldDeclaration;
import com.github.javaparser.ast.visitor.VoidVisitorAdapter;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

public class CodeAnalyzer {
    public static void main(String[] commandLineArguments) {
        if (commandLineArguments.length != 1) {
            System.out.println("Usage: java CodeAnalyzer <path_to_source_directory>");
            System.exit(1);
        }

        String sourceDirectoryPath = commandLineArguments[0];
        analyzeSourceCode(sourceDirectoryPath);
    }

    private static void analyzeSourceCode(String sourcePath) {
        List<FieldInformation> attributeList = new ArrayList<>();

        try {
            File sourceFolder = new File(sourcePath);
            if (sourceFolder.isDirectory()) {
                for (File file : Objects.requireNonNull(sourceFolder.listFiles())) {
                    if (file.isFile() && file.getName().endsWith(".java")) {
                        CompilationUnit cu = StaticJavaParser.parse(file);

                        cu.accept(new FieldVisitor(), attributeList);
                    }
                }
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        System.out.println("attributeList = " + attributeList);

        generateReport(attributeList, "report.csv");
    }

    private static class FieldVisitor extends VoidVisitorAdapter<List<FieldInformation>> {
        @Override
        public void visit(FieldDeclaration fieldDeclaration, List<FieldInformation> fieldInformationList) {
            super.visit(fieldDeclaration, fieldInformationList);

            // Check if the attribute is private and does not have a public getter
            if (fieldDeclaration.getModifiers().contains(Modifier.privateModifier())
                    && !hasPublicGetter(fieldDeclaration, fieldDeclaration.getVariables().get(0).getNameAsString())) {

                CompilationUnit currentCompilationUnit = fieldDeclaration.findCompilationUnit().get();

                String attributeName = fieldDeclaration.getVariables().get(0).getNameAsString();
                String declaringClass = currentCompilationUnit.getPrimaryTypeName().get();
                String packageName = currentCompilationUnit.getPackageDeclaration().map(pd -> pd.getName().asString()).orElse("");

                fieldInformationList.add(new FieldInformation(attributeName, declaringClass, packageName));
            }
        }

        private boolean hasPublicGetter(FieldDeclaration fieldDeclaration, String attributeName) {
            String getterName = "get" + attributeName.substring(0, 1).toUpperCase() + attributeName.substring(1);
            return fieldDeclaration.findCompilationUnit().get().getClassByName(getterName).isPresent();
        }
    }

    private static void generateReport(List<FieldInformation> fieldInformationList, String outputFile) {
        try (FileWriter writer = new FileWriter(outputFile)) {
            writer.write("Attribute,Class,Package\n");
            for (FieldInformation fieldInformation : fieldInformationList) {
                writer.write(fieldInformation.name + "," + fieldInformation.declaringClass + "," + fieldInformation.packageName + "\n");
            }
            System.out.println("The report is named: " + outputFile);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private record FieldInformation(String name, String declaringClass, String packageName) {

    }
}
