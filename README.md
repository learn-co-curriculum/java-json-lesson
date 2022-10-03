# JSON

## Learning Goals

- Understand the JSON format.
- Work with the JSON format in Java.

## Introduction

CSV files are helpful, but they have one fundamental limitation, which is that
each line in a CSV file represents a full object. This means that if an object
has anything but primitive types, it will be difficult to represent in a CSV
file.

Consider a `Student` class with an additional field for that student's classes:

```java
public class Student {
  private String firstName;
  private String lastName;
  private char grade;
  private List<String> classes;

  // ...
}
```

Since we don't know how many classes a particular student has, we cannot come up
with a fixed data format for the CSV file to properly capture all the
information about each class. We need a file format that supports a
hierarchical object structure.

**JSON** stands for **JavaScript Object Notation** and is a file format that
offers more flexibility than CSV files and still allows us to map the data back
to an object in Java. Consider the following to show the format of a JSON file:

```json
[
  {
    "firstName": "Suzie",
    "lastName": "Bingham",
    "grade": "A",
    "classes": [
      "Computer Science"
    ]
  },
  {
    "firstName": "Dustin",
    "lastName": "Henderson",
    "grade": "B",
    "classes": [
      "Latin",
      "Physics",
      "Biology"
    ]
  },
  {
    "firstName": "Mike",
    "lastName": "Wheeler",
    "grade": "C",
    "classes": [
      "Chemistry",
      "Physics"]
  }
]
```

A JSON has the following format:

- A collection (array, list, ...) starts with an opening bracket `[` followed by
  a list of elements, separated by a `,` and it ends with a closing bracket `]`.
- A single object starts with an opening curly brace `{` followed by a list of
  name/value pairs, separated by a `,` and it sends with a closing curly brace
  `}`.
- Each name/value pair consists of the name of the property in quotes followed
  by a colon `:`, followed by the actual value.
- The format we see in the JSON above is usually given the term "pretty printed".
  This format is the preferred format as it is the most readable compared to
  the data being printed on a single line.

> Note: when we open a JSON file in IntelliJ, we can ask IntelliJ to format it
> so that it's not in a single line and is in the pretty format as shown above.
> With the file open, use the keyboard shortcut, "Cmd-Option-L", or go to the
> "Code" menu and select the "Reformat Code" option.

Since JSON is a more complex data format than a simple CSV file, we will be
making use of an open-source JSON parser called "Jackson". It is a library with
its own set of classes we can use to parse through a JSON file.

## Adding the Jackson Library

We need to add the Jackson library to our project in IntelliJ. First, go to your
project structure by right-clicking on your project root and selecting the "Open
Module Settings" option:

![open-module-settings.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/open-module-settings.png)

From the "Project Structure" screen select, the "Libraries" option under
"Project Settings":

![project-settings-libraries.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/project-settings-libraries.png)

From there, click on the "+" sign to add a new library and choose to add a
library from Maven:

![add-library-from-maven.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/add-library-from-maven.png)

> Maven is a dependency management tool, which can be used to help manage
> complex dependencies on large projects.

Enter the following in the search box: "jackson-databind", pull down the menu
and select the latest available version:

![jackson-databind.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/jackson-databind.png)

Confirm your selection:

![confirm-jackson.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/confirm-jackson.png)

And finally confirm that you want to add this library to your project:

![choose-module.png](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/choose-module.png)

## Working with JSON Files

Before we start working with the JSON file, let's modify a few things in our
`Student` class:

```java
import java.util.List;

public class Student {
    private String firstName;
    private String lastName;
    private char grade;
    private List<String> classes;

    // Add the list of classes to the constructor
    public Student(String firstName, String lastName, char grade, List<String> classes) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.grade = grade;
        this.classes = classes;
    }

    public Student(String studentCSV) {
        String[] studentProperties = studentCSV.split(",");
        this.firstName = studentProperties[0];
        this.lastName = studentProperties[1];
        this.grade = studentProperties[2].charAt(0);
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public char getGrade() {
        return grade;
    }

    public void setGrade(char grade) {
        this.grade = grade;
    }

    // Add a getter method for the list of classes
    public List<String> getClasses() {
        return classes;
    }

    // Add a setter method for the list of classes
    public void setClasses(List<String> classes) {
        this.classes = classes;
    }
    
   // Modify the toString() method to include the list of classes
  @Override
  public String toString() {
    String studentString = String.format("%s %s has the letter grade %s",
            firstName,
            lastName,
            grade);
    studentString = studentString + " and is in the following classes: ";
    for (String c : classes) {
      studentString = studentString.concat(c).concat(" ");
    }
    return studentString;
  }

    public String formatAsCSV() {
        return String.format("%s,%s,%s", firstName, lastName, grade);
    }
}
```

### Writing to a JSON

Now let's write a `Student` object to a JSON file:

```java
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class FileIOMain {
    public static void main(String[] args) {
        List<String> classes = new ArrayList<String>(Arrays.asList("Computer Science"));
        Student suzie = new Student("Suzie", "Bingham", 'A', classes);
        File file = new File("student.json");
        ObjectMapper objectMapper = new ObjectMapper();
        try {
            objectMapper.writerWithDefaultPrettyPrinter().writeValue(file, suzie);
        } catch(IOException ioException) {
            ioException.printStackTrace();
        }
    }
}
```

In the above code, we are using an `ObjectMapper` object. This class is part
of the Jackson library we just added to our project! The `ObjectMapper` class
provides functionality for reading and writing JSONs. The first method we see
that we call is the `writerWithDefaultPrettyPrinter()`. When we write to a JSON
file, it will sometimes be formatted all on one line; which is not always the
easiest to read. By using the `writerWithDefaultPrettyPrinter()` method, we can
tell the `objectMapper` that we want to write the JSON file using the format we
saw above where everything was on new lines and indented. We can the call the
`writeValue()` method. The `writeValue()` method takes in the file we want to
write to along with the object that should be converted to a JSON file. As we
have seen before with file IO, the `writeValue()` could potentially throw an
`IOException`, so it is best to wrap the statement in a `try-catch`.

If we run the code above, it will create a `student.json` file with the
following content:

![create-json-one-student](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/create-json1.png)

Let's see if we can write out more students to our `student.json` file to make
it more interesting!

```java
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class FileIOMain {
    public static void main(String[] args) {
        Student suzie = new Student("Suzie",
                "Bingham",
                'A',
                new ArrayList<String>(Arrays.asList("Computer Science")));
        Student dustin = new Student("Dustin",
                "Henderson",
                'B',
                new ArrayList<>(Arrays.asList("Latin", "Physics", "Biology")));
        
        Student[] students = {suzie, dustin};
        
        File file = new File("student.json");
        ObjectMapper objectMapper = new ObjectMapper();
        try {
            objectMapper.writerWithDefaultPrettyPrinter().writeValue(file, students);
        } catch (IOException ioException) {
            ioException.printStackTrace();
        }
    }
}
```

Notice in the above code, we have two students now that we want to write to a
JSON. We'll add them to a `Student[]` and pass that to the `writeValue()`
method. Check out our new `student.json` file:

![create-json-multiple-students](https://curriculum-content.s3.amazonaws.com/java-mod-3/json/create-json2.png)

### Reading from a JSON

Now that we have written out to a JSON file, let's see if we can read a JSON
back in.

When we are reading from a JSON file, we'll want to use the Jackson annotations
to tell the parser what constructor to use and what the properties are called
in our JSON file. Consider the following modifications to the `Student` class:

```java
import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

import java.util.List;

public class Student {
  private String firstName;
  private String lastName;
  private char grade;
  private List<String> classes;

  @JsonCreator
  public Student(@JsonProperty("firstName") String firstName,
                 @JsonProperty("lastName") String lastName,
                 @JsonProperty("grade") char grade,
                 @JsonProperty("classes") List<String> classes) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.grade = grade;
    this.classes = classes;
  }

  //...
}
```

In the code above, we have added a couple of annotations: `@JsonCreater` and
`@JsonProperty`. The `@JsonCreator` tells Jackson that we'd like to use this
constructor when mapping the JSON data back to our `Student` object. This
annotation is placed directly before the constructor definition. As for each
argument, we'll add the `@JsonProperty` annotation before each parameter that
the constructor takes in. We'll also specify what the property is in the JSON
file. For example, the first name of the student will appear as "firstName" in
the JSON file, so we'll specify that property name in the parameter list as
appropriate.

Adding these annotations are necessary if we do not specify a default
constructor. By default, Jackson will try to use the default (empty) constructor
along with the accessor and mutator methods. If we didn't want to add the above
annotations, we could have also just added a constructor like this:

```java
public Student() {}
```

Now that we have either added a constructor that does nothing or the annotations
we specified, we can use the `ObjectMapper` to read in the JSON and map it to
our `Student` class to create `Student` objects.

```java
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.io.IOException;

public class FileIOMain {
    public static void main(String[] args) {
        File file = new File("student.json");
        ObjectMapper objectMapper = new ObjectMapper();
        try {
            Student[] students = objectMapper.readValue(file, Student[].class);
            for (Student student : students) {
                System.out.println(student);
            }
        } catch (IOException ioException) {
            ioException.printStackTrace();
        }
    }
}
```

This time, we are using our `objectMapper` to call the `readValue()` method.
This will take in the JSON file we want to read from and the class we want to
map the JSON objects back to. Since our current `student.json` file consists of
two students, we'll read in a `Student[]`. Note that if we know we only have one
`Student` object in the JSON, we could just specify the `Student.class` instead.
Once we have read in the `Student` objects, we'll print them out to the console.

The following is the expected output of the code above:

```plaintext
Suzie Bingham has the letter grade A and is in the following classes: Computer Science 
Dustin Henderson has the letter grade B and is in the following classes: Latin Physics Biology 
```

JSON has several advantages over other object notations, including CSV:

- It's very readable because every property is formatted as a name/value pair,
  so as long as properties are well named, the file will be easy to read.
- It's a lightweight, compact format - there aren't many characters that aren't
  part of the object structure itself.
- It's a very portable format - the vast majority of programming languages and
  platforms have libraries that can handle JSON objects.
- It can represent object structures of arbitrary complexity and depth.

## Resources

- [Jackson-Databind ObjectMapper](https://fasterxml.github.io/jackson-databind/javadoc/2.7/com/fasterxml/jackson/databind/ObjectMapper.html)