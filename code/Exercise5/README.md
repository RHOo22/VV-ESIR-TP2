# Code of your exercise

package fr.istic.vv;

import com.github.javaparser.JavaParser;
import com.github.javaparser.ParseException;
import com.github.javaparser.ParseResult;
import com.github.javaparser.ast.CompilationUnit;
import com.github.javaparser.ast.body.MethodDeclaration;
import com.github.javaparser.ast.visitor.VoidVisitorAdapter;

import java.io.FileInputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;



public class CyclomaticComplexityCalculator {

  public static void main(String[] args) {
    if (args.length != 1) {
        System.out.println("Usage: java CyclomaticComplexityCalculator ~/Documents/Cours/TP_VV/VV-ESIR-TP2/exemple");
        System.exit(1);
    }

  String projectPath = args[0];
    processJavaProject(projectPath);
    // showHistogram();
    }

  private static void processJavaProject(String projectPath) {
        try {
            // Iterate through all .java files in the project directory
            Files.walk(Paths.get(projectPath))
                    .filter(path -> path.toString().endsWith(".java"))
                    .forEach(path -> processSourceCode(path.toString()));

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

  private static void processSourceCode(String sourcePath) {
        try (FileInputStream in = new FileInputStream(sourcePath)) {
            // Create an instance of JavaParser
            JavaParser javaParser = new JavaParser();

            // Parse the Java source code and get a ParseResult<CompilationUnit>
            ParseResult<CompilationUnit> parseResult = javaParser.parse(in);

            // Extract the CompilationUnit from the ParseResult
            CompilationUnit cu = parseResult.getResult().orElseThrow();

            // Now you can work with the CompilationUnit object
            // For example, you can traverse the AST (Abstract Syntax Tree) using visitors

            // Create a visitor and visit the CompilationUnit
            CyclomaticComplexityVisitor visitor = new CyclomaticComplexityVisitor();
            cu.accept(visitor, null);

            // Print the results or save them to a file
            visitor.printResults();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}

class CyclomaticComplexityVisitor extends VoidVisitorAdapter<Void> {
    private int totalCC = 0;
    private String currentClassName;

    @Override
    public void visit(com.github.javaparser.ast.body.ClassOrInterfaceDeclaration n, Void arg) {
        currentClassName = n.getNameAsString();
        System.out.println("Class: " + currentClassName + ":");
        super.visit(n, arg);
    }

    @Override
    public void visit(MethodDeclaration n, Void arg) {
        // Compute Cyclomatic Complexity for each method
        int cc = computeCyclomaticComplexity(n);
        
        // Print or save the information as needed
        System.out.println("Method: " + n.getDeclarationAsString() + ", CC: " + cc);

        // Add to the total CC for the project
        totalCC += cc;

        super.visit(n, arg);
    }

    private int computeCyclomaticComplexity(MethodDeclaration method) {
        // Simple example: count the number of if, for, while, case statements
        // You might need a more sophisticated algorithm for accurate CC calculation
        int cc = 1;  // Default value for a method

        cc += method.findAll(com.github.javaparser.ast.stmt.IfStmt.class).size();
        cc += method.findAll(com.github.javaparser.ast.stmt.ForStmt.class).size();
        cc += method.findAll(com.github.javaparser.ast.stmt.WhileStmt.class).size();
        cc += method.findAll(com.github.javaparser.ast.stmt.SwitchStmt.class).size();

        return cc;
    }

    public void printResults() {
        System.out.println("Total Cyclomatic Complexity for the project: " + totalCC);
        // You can include more information or save the results to a file
    }
}

