# University Exam Grading System Project (JAVA)

###  Introduction

The University Exam Grading System is a Java-based console application designed to automate the process of recording student marks, computing grades, and persisting academic records in a PostgreSQL database. The project demonstrates core Object-Oriented Programming (OOP) principles and enterprise-level practices, including abstraction, inheritance, polymorphism, encapsulation, database integration (JDBC), input validation and robust error handling. 


```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.InputMismatchException;
import java.util.Scanner;

/*
 * UNIVERSITY EXAM GRADING SYSTEM
 * Demonstrates:
 * - Data types & variables
 * - Classes & methods
 * - Encapsulation & inheritance
 * - Abstraction
 * - Polymorphism
 * - PostgreSQL (JDBC) integration
 * - Input validation
 * - Error handling
 */

// ================= ABSTRACTION =================
abstract class Person {
    protected String id;  // String ID now
    protected String name;

    public Person(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public abstract void displayInfo();
}

// ================= INHERITANCE + ENCAPSULATION =================
class Student extends Person {
    private String course;
    private double marks;

    public Student(String id, String name, String course, double marks) {
        super(id, name);
        this.course = course;
        setMarks(marks);
    }

    public double getMarks() {
        return marks;
    }

    public void setMarks(double marks) {
        if (marks < 0 || marks > 100) {
            throw new IllegalArgumentException("Marks must be between 0 and 100.");
        }
        this.marks = marks;
    }

    public String getCourse() {
        return course;
    }

    @Override
    public void displayInfo() {
        System.out.println("Student ID : " + id);
        System.out.println("Name       : " + name);
        System.out.println("Course     : " + course);
        System.out.println("Marks      : " + marks);
    }
}

// ================= POLYMORPHISM =================
interface GradingPolicy {
    String calculateGrade(double marks);
}

class StandardGrading implements GradingPolicy {

    @Override
    public String calculateGrade(double marks) {
        if (marks >= 70) return "A";
        else if (marks >= 60) return "B";
        else if (marks >= 50) return "C";
        else if (marks >= 40) return "D";
        else return "E";
    }
}

// ================= DATABASE INTEGRATION =================
class DatabaseManager {

    private static final String URL =
            "jdbc:postgresql://localhost:5432/UniversityExamGradingSystem";
    private static final String USER = "postgres";
    private static final String PASSWORD = "Phabiansharish@254";

    public static void saveStudent(Student student, String grade, String department, String school) {

        String sql = "INSERT INTO students(id, name, course, marks, grade, department, school) " +
                     "VALUES (?, ?, ?, ?, ?, ?, ?)";

        try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, student.id);
            stmt.setString(2, student.name);
            stmt.setString(3, student.getCourse());
            stmt.setDouble(4, student.getMarks());
            stmt.setString(5, grade);
            stmt.setString(6, department);
            stmt.setString(7, school);

            stmt.executeUpdate();
            System.out.println("✔ Student record saved to database.");

        } catch (SQLException e) {
            System.err.println("✖ Database Error: " + e.getMessage());
        }
    }
}

// ================= MAIN APPLICATION =================
public class UniversityExamGradingSystem {

    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);
        GradingPolicy gradingPolicy = new StandardGrading();

        try {
            System.out.println("=== UNIVERSITY EXAM GRADING SYSTEM ===");

            // --- Read Student ID as String
            System.out.print("Enter Student ID: ");
            String id = scanner.nextLine();

            // Determine course and department automatically
            String course;
            String department;
            char firstChar = Character.toUpperCase(id.charAt(0));

            switch (firstChar) {
                case 'J':
                    course = "Computer Science";
                    department = "Computer Science Department";
                    break;
                case 'I':
                    course = "Mathematics";
                    department = "Mathematics Department";
                    break;
                case 'E':
                    course = "Education";
                    department = "Education Department";
                    break;
                default:
                    System.out.print("Enter Course (manual): ");
                    course = scanner.nextLine();
                    System.out.print("Enter Department (manual): ");
                    department = scanner.nextLine();
                    break;
            }

            // Determine school based on course
            String school;
            if (course.equals("Mathematics") || course.equals("Computer Science")) {
                school = "School of Pure and Applied Science";
            } else {
                school = "School of Education";
            }

            System.out.print("Enter Student Name: ");
            String name = scanner.nextLine();

            System.out.print("Enter Marks: ");
            double marks = scanner.nextDouble();

            Student student = new Student(id, name, course, marks);
            String grade = gradingPolicy.calculateGrade(marks);

            System.out.println("\n--- STUDENT DETAILS ---");
            student.displayInfo();
            System.out.println("Grade      : " + grade);
            System.out.println("Department : " + department);
            System.out.println("School     : " + school);

            // Save to database including department and school
            DatabaseManager.saveStudent(student, grade, department, school);

        } catch (InputMismatchException e) {
            System.err.println("✖ Invalid input type. Please enter correct values.");
        } catch (IllegalArgumentException e) {
            System.err.println("✖ Validation Error: " + e.getMessage());
        } catch (Exception e) {
            System.err.println("✖ Application Error: " + e.getMessage());
        } finally {
            scanner.close();
        }
    }
}
// javac -cp ".;lib/postgresql-42.7.8.jar" UniversityExamGradingSystem.java (declare the terminal bash)
// java -cp ".;lib/postgresql-42.7.8.jar" UniversityExamGradingSystem (Run the java code)

```

