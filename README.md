# Lesson Title

## Learning Goals

- Learning Goal 1
- Learning Goal 2

## Introduction

CSV files are helpful, but they have one fundamental limitation, which is that
each line in a CSV file represents a full object. This means that if an object
has anything but primitive types, it will be difficult to represent in a CSV
file.

Consider a `Person` class with an additional field for that person's friends:

```java
public class Person {
    private String firstName;
    private String lastName;
    private int birthYear;
    private int birthMonth;
    private int birthDay;
    private List<Person> friends;

    // ...
```

Since we don't know how many friends a particular person has, we cannot come up
with a fixed data format for the CSV file to properly capture all the
information about each friend. We need a file format that supports a
hierarchical object structure.

JSON stands for JavaScript Object Notation, but it is an object notation that is
exclusive to Javascript. It's an object notation that is very well supported
across most platforms, including here in Java.

JSON is a more complex data format than simple CSV, so writing our own parser
and serializer would be beyond the scope of this course. We are going to use an
existing open-source JSON parser called "Jackson". It is a library with its own
set of classes we can use for our JSON needs.

> Serialization is the process of taking an object and converting it to a format
> that can be saved to file. Our `writeToFile()` method is a rudimentary
> "serializer" that knows how to convert our Person object to a format that can
> be saved to a CSV file

## Working with JSON

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

Let's have our first look at what a JSON document looks like by simply
formatting our list of Person objects from our last exercise. You can add a
simple `printPersonListAsJSON()` method to your `FileIORunner` class:

```java
    static void printPersonListAsJSON(List<Person> personList) throws Exception {
        String json = new ObjectMapper().writeValueAsString(personList);
        System.out.println(json);
    }
```

And call it from the `savePersonList()` method:

```java
    static void savePersonList(List<Person> personList) throws IOException, Exception {
        StringBuffer allPersonsAsCSV = new StringBuffer();
        personList.forEach((person) -> {
            String personString = person.formatAsCSV();
            allPersonsAsCSV.append(personString + "\n");
        });
        writeToFile(PERSON_LIST_FILENAME, allPersonsAsCSV.toString());
        printPersonListAsJSON(personList);
    }
```

This will produce a single line of text that can be formatted as follows:

```javascript
[
  {
    firstName: "James",
    lastName: "Brown",
    birthYear: 1988,
    birthMonth: 1,
    birthDay: 1,
  },
  {
    firstName: "Natalie",
    lastName: "Silver",
    birthYear: 2001,
    birthMonth: 2,
    birthDay: 2,
  },
  {
    firstName: "Peter",
    lastName: "Bourn",
    birthYear: 1971,
    birthMonth: 3,
    birthDay: 3,
  },
  {
    firstName: "Jamie",
    lastName: "Saragan",
    birthYear: 2001,
    birthMonth: 4,
    birthDay: 4,
  },
];
```

A JSON document has the following format:

- A collection (array, list, ...) starts with an opening bracket `[` followed by
  a list of elements, separated by a `,` and it ends with a closing bracket `]`
- A single object starts with an opening curly brace `{` followed by a list of
  name/value pairs, separated by a `,` and it sends with a closing curly brace
  `}`
- Each name/value pair consists of the name of the property in quotes followed
  by a colon `:`, followed by the actual value
- If the value is a primitive, then it's in line with the name. If the value is
  a complex type, then it follows the element structure outlined above, starting
  with an opening curly brace `{`

> Note: when you open a JSON file in IntelliJ, you can ask IntelliJ to format it
> so that it's not in a single line and is much easier to read, in a format
> similar to the format above. With your file open, use the keyboard shortcut
> "Cmd-Option-L", or go to the "Code" menu and select the "Reformat Code" option

JSON has several advantages over other object notations, including CSV:

- It's very readable because every property is formatted as a name/value pair,
  so as long as properties are well named, the file will be easy to read
- It's a lightweight, compact format - there aren't many characters that aren't
  part of the object structure itself
- It's a very portable format - the vast majority of programming languages and
  platforms have libraries that can handle JSON objects
- It can represent object structures of arbitrary complexity and depth
