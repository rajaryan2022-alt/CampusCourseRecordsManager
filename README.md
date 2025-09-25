# CampusCourseRecordsManager
A robust Java SE desktop application designed to streamline course and student record management for academic institutions. Built with modular architecture and best practices, this project enables administrators to efficiently manage campus-wide data with clarity and control

1. Person.java

package edu.ccrm.domain;

import java.time.LocalDate;

public abstract class Person {
    protected String id;
    protected String firstName;
    protected String lastName;
    protected String email;
    protected LocalDate dateOfBirth;
    
    public Person(String id, String firstName, String lastName, String email, LocalDate dateOfBirth) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.dateOfBirth = dateOfBirth;
    }
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }
    public String getFirstName() { return firstName; }
    public void setFirstName(String firstName) { this.firstName = firstName; }
    public String getLastName() { return lastName; }
    public void setLastName(String lastName) { this.lastName = lastName; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public LocalDate getDateOfBirth() { return dateOfBirth; }
    public void setDateOfBirth(LocalDate dateOfBirth) { this.dateOfBirth = dateOfBirth; }
    public abstract String getRole();
    public String getFullName() {
        return firstName + " " + lastName;
    }
    @Override
    public String toString() {
        return String.format("ID: %s, Name: %s, Email: %s, DOB: %s", id, getFullName(), email, dateOfBirth);
    }
}

2. Student.java

package edu.ccrm.domain;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

public class Student extends Person {
    private String major;
    private LocalDate enrollmentDate;
    private List<Enrollment> enrollments;
    private double gpa;
    
    private Student(Builder builder) {
        super(builder.id, builder.firstName, builder.lastName, builder.email, builder.dateOfBirth);
        this.major = builder.major;
        this.enrollmentDate = builder.enrollmentDate;
        this.enrollments = new ArrayList<>();
        this.gpa = 0.0;
    }
    public static class Builder {
        private String id;
        private String firstName;
        private String lastName;
        private String email;
        private LocalDate dateOfBirth;
        private String major;
        private LocalDate enrollmentDate;
        
        public Builder(String id, String firstName, String lastName) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
        }
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        public Builder dateOfBirth(LocalDate dateOfBirth) {
            this.dateOfBirth = dateOfBirth;
            return this;
        }
        public Builder major(String major) {
            this.major = major;
            return this;
        }
        public Builder enrollmentDate(LocalDate enrollmentDate) {
            this.enrollmentDate = enrollmentDate;
            return this;
        }
        public Student build() {
            return new Student(this);
        }
    }
    @Override
    public String getRole() {
        return "Student";
    }
    public String getMajor() { return major; }
    public void setMajor(String major) { this.major = major; }
    public LocalDate getEnrollmentDate() { return enrollmentDate; }
    public void setEnrollmentDate(LocalDate enrollmentDate) { this.enrollmentDate = enrollmentDate; }
    public List<Enrollment> getEnrollments() { return new ArrayList<>(enrollments); }
    public double getGpa() { return gpa; }
    public void setGpa(double gpa) { this.gpa = gpa; }
    public void addEnrollment(Enrollment enrollment) { enrollments.add(enrollment); }
    public void removeEnrollment(Enrollment enrollment) { enrollments.remove(enrollment); }
    @Override
    public String toString() {
        return super.toString() + String.format(", Major: %s, GPA: %.2f", major, gpa);
    }
}

3. Grade.java

package edu.ccrm.domain;

public enum Grade {
    A_PLUS(4.0, "A+"), A(4.0, "A"), A_MINUS(3.7, "A-"), B_PLUS(3.3, "B+"), B(3.0, "B"), B_MINUS(2.7, "B-"),
    C_PLUS(2.3, "C+"), C(2.0, "C"), C_MINUS(1.7, "C-"), D_PLUS(1.3, "D+"), D(1.0, "D"), F(0.0, "F"),
    INCOMPLETE(0.0, "I"), WITHDRAW(0.0, "W");

    private final double gradePoints;
    private final String displayName;
    Grade(double gradePoints, String displayName) {
        this.gradePoints = gradePoints;
        this.displayName = displayName;
    }
    public double getGradePoints() { return gradePoints; }
    public String getDisplayName() { return displayName; }
    @Override
    public String toString() { return displayName; }
}

4. Semester.java

package edu.ccrm.domain;

public enum Semester {
    SPRING("Spring"), SUMMER("Summer"), FALL("Fall"), WINTER("Winter");
    private final String displayName;
    Semester(String displayName) { this.displayName = displayName; }
    public String getDisplayName() { return displayName; }
    @Override
    public String toString() { return displayName; }
}

5. Course.java

package edu.ccrm.domain;

import java.util.ArrayList;
import java.util.List;

public class Course {
    private String courseCode;
    private String courseName;
    private int credits;
    private String description;
    private String instructorId;
    private int maxCapacity;
    private List<String> enrolledStudentIds;
    private Semester semester;
    private int year;
    
    public Course(String courseCode, String courseName, int credits, String description, Semester semester, int year) {
        this.courseCode = courseCode;
        this.courseName = courseName;
        this.credits = credits;
        this.description = description;
        this.semester = semester;
        this.year = year;
        this.enrolledStudentIds = new ArrayList<>();
        this.maxCapacity = 30;
    }
    public boolean isFull() { return enrolledStudentIds.size() >= maxCapacity; }
    public boolean isStudentEnrolled(String studentId) { return enrolledStudentIds.contains(studentId); }
    public boolean addStudent(String studentId) {
        if (!isFull() && !isStudentEnrolled(studentId)) {
            enrolledStudentIds.add(studentId);
            return true;
        }
        return false;
    }
    public boolean removeStudent(String studentId) { return enrolledStudentIds.remove(studentId); }
    public int getCurrentEnrollment() { return enrolledStudentIds.size(); }
    public String getCourseCode() { return courseCode; }
    public void setCourseCode(String courseCode) { this.courseCode = courseCode; }
    public String getCourseName() { return courseName; }
    public void setCourseName(String courseName) { this.courseName = courseName; }
    public int getCredits() { return credits; }
    public void setCredits(int credits) { this.credits = credits; }
    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }
    public String getInstructorId() { return instructorId; }
    public void setInstructorId(String instructorId) { this.instructorId = instructorId; }
    public int getMaxCapacity() { return maxCapacity; }
    public void setMaxCapacity(int maxCapacity) { this.maxCapacity = maxCapacity; }
    public List<String> getEnrolledStudentIds() { return new ArrayList<>(enrolledStudentIds); }
    public Semester getSemester() { return semester; }
    public void setSemester(Semester semester) { this.semester = semester; }
    public int getYear() { return year; }
    public void setYear(int year) { this.year = year; }
    @Override
    public String toString() {
        return String.format("%s - %s (%d credits) [%s %d] - %d/%d enrolled", courseCode, courseName, credits, semester, year, getCurrentEnrollment(), maxCapacity);
    }
}

6. StudentService.java

package edu.ccrm.service;

import edu.ccrm.domain.Student;
import edu.ccrm.util.ValidationUtil;
import java.time.LocalDate;
import java.util.*;
import java.util.stream.Collectors;

public class StudentService {
    private Map<String, Student> students;
    public StudentService() { this.students = new HashMap<>(); }
    public boolean createStudent(String id, String firstName, String lastName, String email, LocalDate dateOfBirth, String major) {
        if (students.containsKey(id)) return false;
        if (!ValidationUtil.isValidEmail(email)) return false;
        Student student = new Student.Builder(id, firstName, lastName).email(email).dateOfBirth(dateOfBirth).major(major).enrollmentDate(LocalDate.now()).build();
        students.put(id, student);
        return true;
    }
    public Optional<Student> findStudentById(String id) { return Optional.ofNullable(students.get(id)); }
    public List<Student> getAllStudents() { return new ArrayList<>(students.values()); }
    public List<Student> searchStudentsByName(String searchTerm) {
        return students.values().stream().filter(student -> student.getFullName().toLowerCase().contains(searchTerm.toLowerCase())).collect(Collectors.toList());
    }
    public List<Student> searchStudentsByMajor(String major) {
        return students.values().stream().filter(student -> student.getMajor().equalsIgnoreCase(major)).collect(Collectors.toList());
    }
    public boolean updateStudent(String id, String firstName, String lastName, String email, String major) {
        Optional<Student> studentOpt = findStudentById(id);
        if (studentOpt.isPresent()) {
            Student student = studentOpt.get();
            if (firstName != null) student.setFirstName(firstName);
            if (lastName != null) student.setLastName(lastName);
            if (email != null && ValidationUtil.isValidEmail(email)) student.setEmail(email);
            if (major != null) student.setMajor(major);
            return true;
        }
        return false;
    }
    public boolean deleteStudent(String id) { return students.remove(id) != null; }
    public int getTotalStudents() { return students.size(); }
    public void displayAllStudents() {
        if (students.isEmpty()) return;
        students.values().stream().sorted(Comparator.comparing(Student::getLastName).thenComparing(Student::getFirstName)).forEach(System.out::println);
    }
}
