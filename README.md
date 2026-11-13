// FINAL PROJECT ADA TEAM 4 Gradebook
//Team: Daniel Pérez, Maximiliano Tussie, Aitor González, José Antonio Martínez, Álvaro Vargas
//Date: November 12, 2026
// Version 1.0


 // Ask the user for the group name and store it.
Console.Write("Group Name?: ");
string groupName = ReadRequiredText();

// Create a list to store all registered students.
List<Student> students = new();

// Initialize the variable that will control the menu selection.
int option;

// Repeat the menu until the user chooses to exit.
do
{
    Console.WriteLine("\n=== FINAL PROJECT: Grade book ===");
    Console.WriteLine("1) Register 2) List 3) Summary 4) Exit");
    Console.Write("Option: ");

    // Read the user's option and validate that it is an integer.
    if (!int.TryParse(Console.ReadLine(), out option))
    {
        option = 0;
    }

    // Execute the corresponding process depending on the selected option.
    if (option == 1)
    {
        Register(students);
    }
    else if (option == 2)
    {
        List(students);
    }
    else if (option == 3)
    {
        Summary(students, groupName);
    }
    else if (option != 4)
    {
        Console.WriteLine("Invalid option");
    }
} while (option != 4);

// Register a new student by requesting and validating their information.
static void Register(List<Student> students)
{
    Console.WriteLine("--- New Student ---");
    Console.Write("Name: ");
    string name = ReadRequiredText();

    // Validate the student's ID as an integer and their average as a numeric value.
    ValidateInt("ID: ", out int id);
    ValidateDouble("Average: ", out double average);

    // Create a new Student object and add it to the list.
    students.Add(new Student { Name = name, Average = average, Id = id });
    Console.WriteLine("Student registered.");
}

// Display all registered students and allow the user to update an average.
static void List(List<Student> students)
{
    // Check if the list is empty before displaying its contents.
    if (students.Count == 0)
    {
        Console.WriteLine("No students registered.");
        return;
    }

    Console.WriteLine("--- STUDENTS ---");

    // Traverse the list and display each student's information.
    for (int i = 0; i < students.Count; i++)
    {
        Console.Write($"{i + 1}) ");
        Show(students[i]);
    }

    // Ask which student's average should be modified, or return to the menu.
    Console.Write("Student number to edit the average (press Enter to return): ");
    while (true)
    {
        string input = Console.ReadLine() ?? string.Empty;

        // Return to the menu if the user presses Enter without entering a value.
        if (string.IsNullOrWhiteSpace(input))
        {
            return;
        }

        // Verify that the selected student number is an integer within the valid list range.
        if (int.TryParse(input, out int studentNumber) &&
            studentNumber >= 1 &&
            studentNumber <= students.Count)
        {
            // Retrieve the selected student using their position in the list.
            Student student = students[studentNumber - 1];

            // Request and validate the student's new average.
            ValidateDouble($"New average for {student.Name}: ", out double newAverage);

            // Update the student's average and save the modified object in the list.
            student.Average = newAverage;
            students[studentNumber - 1] = student;

            Console.WriteLine("Average updated.");
            return;
        }

        // Request another selection if the entered number is invalid.
        Console.Write($"Enter a number from 1 to {students.Count}, or press Enter to return: ");
    }
}

// Generate a grade report showing all students, the group average, and the top three students.
static void Summary(List<Student> students, string groupName)
{
    Console.WriteLine($"--- GRADE BOOK REPORT {groupName} ---");

    // Check whether there are registered students before performing calculations.
    if (students.Count == 0)
    {
        Console.WriteLine("No students registered.");
        return;
    }

    // Initialize the variable that will accumulate all student averages.
    double sum = 0;

    // Traverse the list, display each student, and accumulate their averages.
    foreach (Student student in students)
    {
        Show(student);
        sum += student.Average;
    }

    // Calculate the group average by dividing the total sum by the number of students.
    Console.WriteLine($"Group average: {sum / students.Count:F2}");
    Console.WriteLine("--- TOP 3 STUDENTS ---");

    // Sort students from highest to lowest average and select the first three.
    List<Student> topStudents = students
        .OrderByDescending(student => student.Average)
        .Take(3)
        .ToList();

    // Display the selected students in their ranking order.
    for (int i = 0; i < topStudents.Count; i++)
    {
        Student student = topStudents[i];
        Console.WriteLine($"{i + 1}. [{student.Id}] {student.Name} - Average: {student.Average:F2}");
    }
}

// Display a student's information and determine whether they passed or failed.
static void Show(Student student)
{
    // Compare the student's average with the minimum passing grade.
    string result = student.Average >= 6.0 ? "Pass!" : "Fail!";
    Console.WriteLine($"[{student.Id}] {student.Name} - Average: {student.Average:F1} {result}");
}

// Read a text value and repeat the request until a non-empty value is entered.
static string ReadRequiredText()
{
    string value = Console.ReadLine() ?? string.Empty;

    // Validate that the entered text is not empty or composed only of whitespace.
    while (string.IsNullOrWhiteSpace(value))
    {
        Console.Write("Value cannot be empty. Try again: ");
        value = Console.ReadLine() ?? string.Empty;
    }

    // Return the validated text.
    return value;
}

// Validate that the entered ID is an integer.
static void ValidateInt(string message, out int result)
{
    Console.Write(message);

    // Repeat the request until the user enters a valid integer.
    while (!int.TryParse(Console.ReadLine(), out result))
    {
        Console.WriteLine("Wrong input try again. You must enter an integer number.");
        Console.WriteLine("");
        Console.Write(message);
    }
}

// Validate that the entered average is a numeric value.
static void ValidateDouble(string message, out double result)
{
    Console.Write(message);

    // Repeat the request until the user enters a valid numeric value.
    while (!double.TryParse(Console.ReadLine(), out result))
    {
        Console.WriteLine("Wrong input try again. You must enter a number.");
        Console.WriteLine("");
        Console.Write(message);
    }
}

// Define the structure used to store each student's information.
struct Student
{
    public string Name;
    public double Average;
    public int Id;
}
