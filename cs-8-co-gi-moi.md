Introduction
C# 8 comes with a host of changes and language improvements that make it more interesting than ever to write code in the world’s most popular programming language. This article describes the most important new features and enhancements to the language.

Nullable Reference Types

Nullable value types have existed in C# since long. The same functionality has now been made available to reference types. What this essentially means is that you can now mark a reference variable as Nullable, which tells that compiler that it is okay if the variable is assigned a null value. For variables that are not marked as Nullable, the compiler treats them as Non-Nullable Reference Types.

A Nullable Reference Type is declared using the same syntax as a Nullable Value Type, i.e. suffixing it with a ? operator. If the ? is not appended to the type name, then it is treated as a non-nullable reference type. That includes all reference type variables in existing code when you have enabled this feature.

The compiler uses static analysis to determine if a nullable reference is known to be non-null. The compiler warns you if you dereference a nullable reference when it may be null. You can override this behavior by using the null-forgiving operator ! following a variable name. For example, if you know the myAddress variable isn’t null but the compiler issues a warning, you can write the following code to override the compiler’s analysis:

1

class Address {

2

   string StreetName { get; set; }

3

string City { get; set; }

4

string State { get; set; }

5

string ZipCode { get; set; }

6

}

7

​

8

Address? myAddress; //Nullable Reference Type

9

​

10

var state = myAddress.State //Compiler may issue a warning for null reference

11

var city = myAddress!.City //Compiler issues no warning for null reference

12

​

Asynchronous streams

C# 8.0 allows you to create and consume streams asynchronously. To enable your methods to return an asynchronous stream, your method must:

1

public static async IAsyncEnumerable<int> GetRandomSequence(int size, int max) {

2

 for (var i = 0; i < size; i++) {

3

   yield return _random.Next(max - (size - 1));

4

}

5

 await Task.Delay(50);

6

}

be decorated with the async modifier.

return an IAsyncEnumerable<T>.

contain yield return statements to return successive elements in the asynchronous stream.

To enumerate the return value from this method, use:

1

await foreach (var number in GetRandomSequence(1000, 1000000)) {

2

 Console.WriteLine(number);

3

}

Index and Range

The new Index and Range types and the two new associated operators provide a neat and readable syntax while working with sequences.

System.Index represents an index in a sequence.

System.Range represents a sub range within a sequence.

The index from end operator ^, which specifies that an index is relative to the end of the sequence.

The range operator .., which specifies the start and end of a range as its operands.

Consider an array words. The 0 index is the same as words[0]. The ^0 index is the same as words[words.Length]. Note that words [^0] throws an exception, just as words[words.Length] does. For any number n, the index ^n is the same as words.Length - n.

A Range specifies the start and end of a range. The start of the range is inclusive, but the end of the range is exclusive, meaning the start is included in the range but the end is not included in the range. The range [0..^0] represents the entire range, just as [0..words.Length] represents the entire range.

Here are few examples:

1

string[] words = new string[]

2

{

3

                   // index from start   index from end

4

       "She",      // 0                   ^8

5

       "sells",    // 1                   ^7

6

       "sea",      // 2                   ^6

7

       "shells",   // 3                   ^5

8

       "on",       // 4                   ^4

9

       "the",      // 5                   ^3

10

       "sea",      // 6                   ^2

11

       "shore",    // 7                   ^1

12

};                  // 8 (or words.Length) ^0

1

Console.WriteLine($"The last word is {words[^1]}"); // writes "shore"

2

​

3

//The following code creates a subrange with the words "sells", "sea", and "shells".It includes words[1] through words[3]. The element words[4] is not in the range.

4

var sellsSeaShells = words[1..4];

5

​

6

//The following code creates a subrange with "sea" and "shore". It includes words[^2] and words[^1]. The end index words[^0] is not included:

7

var seaShore = words[^2..^0];

8

​

9

var allWords = words[..]; // contains "She" through "shore".

10

var firstPhrase = words[..4]; // contains "She" through "shells"

11

var lastPhrase = words[5..]; // contains "the", "sea" and "shore"

Ranges can also be declared as variables and used in expressions.

1

Range phrase = 1..4;

2

var text = words[phrase];

3

//text contains "sells", "sea", and "shells"

Default Interface Members

Developers who write and distribute components must have gone through the problem with managing versioning of interfaces. In the COM/ActiveX world, it was referred to as “DLL Hell”. That is, once you publish an interface, you cannot add new members without breaking existing classes that implement the interface. Traditional method of handling such cases was to publish a new interface with the new members and ask your component users to start implementing your new interface in future releases of their code.

C# 8.0 allows you to provide a default implementation for your new interface members to ensure that existing classes that implement your interface are not broken. What this essentially means is that your interface methods and properties can now have a body and they can also have static members.

1

//Version 1

2

public interface IDevSpaceControl {

3

 bool WinFormsSupported { get; }

4

}

1

//Version 2

2

public interface IDevSpaceControl {

3

 bool WinFormsSupported { get; }

4

 bool WinFormsSupportedOnDotNetCore {

5

   get {

6

     return false;

7

  }

8

}

9

}

In this example, the IDevSpaceControl interface adds a new member WinFormsSupportedOnDotNetCore without breaking existing clients by providing a default implementation.

To me personally, this is a design problem where the developer can get around by using abstract classes instead of interfaces, but Microsoft has purportedly done this to enable C# to interoperate with Android and Swift languages that already support similar features.

Switch Expressions

The switch statement has been enhanced to return an expression, this is called a switch expression. Here’s an example of a switch statement:

1

enum Importance {

2

 None,

3

 Trivial,

4

 Regular,

5

 Important,

6

 Critical

7

}

8

​

9

//switch statement

10

internal int GetPriority(Importance importance) {

11

 switch (importance) {

12

   case Importance.None:

13

     return 0;

14

   case Importance.Trivial:

15

     return 25;

16

   case Importance.Regular:

17

     return 50;

18

   case Importance.Important:

19

     return 75;

20

   case Importance.Critical:

21

     return 100;

22

   default:

23

     return 50;

24

};

25

}

Here’s equivalent switch expression. Note the brevity of the switch expression and how the code is very easy to read and understand.

1

//switch expression

2

internal int GetPriority(Importance importance) =>

3

 importance switch {

4

     Importance.None => 0,

5

     Importance.Trivial => 25,

6

     Importance.Regular => 50,

7

     Importance.Important => 75,

8

     Importance.Critical => 100,

9

     _ => 50

10

};

Switch Statement vs Switch Expressions

There are several syntax differences between a switch statement and a switch expression:

The variable comes before the switch keyword, which makes it easy to distinguish the switch expression from the switch statement.

The case: is replaced with =>

The default clause is replaced with a _ discard.

The bodies are expressions, not statements.

A switch expression must either return a value or throw an exception. If none of the cases match, the switch expression throws an exception. As with switch statements, the compiler generates a warning if all possible cases are not covered in the switch expression.

Property Patterns

Property pattern allows you to examine the property of an object and take action. Let’s take an example of Income Tax calculation where the tax rates change based on the State. The Address class has a property called State. Look how the State property is elegantly examined and an appropriate expression returned.

1

public static decimal ComputeIncomeTax(Address location, decimal income) =>

2

 location switch

3

{

4

    { State: "TX" } => income * 0.25M, //Property pattern

5

    { State: "MA" } => income * 0.31M,

6

    { State: "NY" } => income * 0.35M,

7

     // other cases removed for brevity...

8

     _ => 0.30M

9

};

Tuple Patterns

Tuple patterns enable you to switch by comparing multiple inputs to values expressed as a tuple. The following code shows an updated version of the switch expression code above:

1

enum CustomerRanking {

2

 Regular,

3

 Gold,

4

 Platinum,

5

 Titanium

6

}

7

​

8

internal int GetPriority(Importance importance, CustomerRanking customerRanking) =>

9

(importance, customerRanking) switch

10

{

11

  (Importance.None, CustomerRanking.Regular) => 0,

12

  (Importance.None, CustomerRanking.Gold) => 5,

13

  (Importance.None, CustomerRanking.Platinum) => 10,

14

  (Importance.None, CustomerRanking.Titanium) => 15,

15

  (Importance.Trivial, CustomerRanking.Regular) => 20,

16

  (Importance.Trivial, CustomerRanking.Gold) => 25,

17

  (Importance.Trivial, CustomerRanking.Platinum) => 30,

18

  (Importance.Trivial, CustomerRanking.Titanium) => 35,

19

  (Importance.Regular, CustomerRanking.Regular) => 45,

20

  (Importance.Regular, CustomerRanking.Gold) => 50,

21

  (Importance.Regular, CustomerRanking.Platinum) => 55,

22

  (Importance.Regular, CustomerRanking.Titanium) => 60,

23

  (Importance.Important, CustomerRanking.Regular) => 65,

24

  (Importance.Important, CustomerRanking.Gold) => 70,

25

  (Importance.Important, CustomerRanking.Platinum) => 75,

26

  (Importance.Important, CustomerRanking.Titanium) => 80,

27

  (Importance.Critical, _) => 100,

28

   _ => 50

29

};

Note that you can ignore an argument in the switch expression by passing the _ discard. In the above example, different combinations of Importance and CustomerRanking are compared to arrive at a priority. However, when the Importance is Critical, priority is always 100 irrespective of the value of CustomerRanking argument.

Compare it to the traditional way of writing multiple if statements or nested switch statements. Neat, isn’t it?

Readonly members in Structs

Any member of a struct can be declared as readonly by specifying the readonly modifier. It is a design time intent to let the compiler know that the member does not undergo state change. It enables the compiler to perform optimizations when necessary to improve performance. It provides more granular control than declaring the entire struct as readonly.

1

public struct Circle

2

  {

3

       public double Radius { get; set; }

4

       public double Diameter => Radius * 2;

5

       public readonly double Circumference => Math.PI * Diameter;

6

       public readonly double Area => Math.PI * Math.Pow(Radius, 2);

7

       public readonly override string ToString() =>

8

           $"Circle: Radius={Radius}, Diameter={Diameter}, Circumference={Circumference}, Area={Area}";

9

  }

Note that Diameter is not readonly and Circumference is declared as readonly and references Diameter. When a readonly member references a non-readonly member, the compiler throws a warning:

1

Warning CS8656 Call to non-readonly member 'Circle.Diameter.get' from a 'readonly' member results in an implicit copy of 'this'

You can decorate the Diameter property with readonly to fix this issue.

Using Declarations

The using declarations can now be scoped to a variable for better code readability. Tired of declaring multiple nested using declarations to efficiently dispose off objects? Try this new syntax:

1

static void WriteToFile(List<string> lines) {

2

   using var file = new System.IO.StreamWriter("sample.txt");

3

   foreach (string line in lines) {

4

       if (!line.Contains("Second")) {

5

           file.WriteLine(line);

6

      }

7

  }

8

//Dispose() called here

9

}

Note that the scope of the using variable is the scope where the variable is defined. On the contrary, in a using statement, the scope would be the end of the using statement itself.

1

static void WriteToFile(List<string> lines) {

2

   using file = new System.IO.StreamWriter("sample.txt") {

3

     foreach (string line in lines) {

4

         if (!line.Contains("Second")) {

5

             file.WriteLine(line);

6

        }

7

    }

8

//Dispose() called here

9

  }

10

}

If not used properly, this could have significant consequences on the working memory of your applications.

Conclusion

C# has been a very exciting language from the beginning and one of the very few languages that keeps getting new features and enhancements very often. C# 8.0 adds more arms to your already-rich arsenal. If you liked this article, feel free to leave a comment and share this article with your friends and colleagues who might be interested.

More Reading

 2  0

Share

 

 

Post navigation

New Features and Enhancements in .NET Core 3.0

Related Articles

￼

New Features and Enhancements in .NET Core 3.0

 Shameel Ahmed

 September 25, 2019 No Comments

￼

Migrating your SQL Server Workloads to PostgreSQL

 Shameel Ahmed

 September 8, 2019 No Comments

Migrating your SQL Server Workloads to PostgreSQL – Part 3

 Shameel Ahmed

 September 8, 2019 No Comments

2 thoughts on “C# 8 New Features”

￼ Tim says:

October 8, 2019 at 2:23 pm

var city = myAddress!.City //Compiler issues no warning for null reference

should be
var city = myAddress?.City //Compiler issues no warning for null reference

Reply

￼ Shameel Ahmed says:

October 10, 2019 at 12:52 am

Hi Tim, the ! is a new operator that tells the compiler not to enforce null check on the variable. The ? is used to define a Nullable type, they’re both different.

Reply

Leave a Reply

Your email address will not be published. Required fields are marked *

Comment 

Name * 

Email * 

Website 

Save my name, email, and website in this browser for the next time I comment.

Categories

.NET Architecture ASP.NET AWS Azure C# Cloud CodeProject Database Data Security Facade Java Mobile Open Source Patterns PostgreSQL Programming Security SQL Server Tools Web Development Windows Phone

Recent Posts

C# 8 New Features October 8, 2019

New Features and Enhancements in .NET Core 3.0 September 25, 2019

Generated Columns in PostgreSQL September 18, 2019

PostgreSQL 12: New Features and Enhancements September 16, 2019

PostgreSQL finally gets Stored Procedures September 12, 2019

Archives

October 2019 (1)

September 2019 (9)

July 2019 (2)

May 2018 (1)

September 2017 (1)

April 2017 (1)

April 2014 (1)

August 2011 (1)

June 2009 (1)

Copyright 2019. The Developer Space | Theme: OMag by LilyTurf Themes

Privacy Overview

This website uses cookies to improve your experience while you navigate through the website. Out of these cookies, the cookies that are categorized as necessary are stored on your browser as they are as essential for the working of basic...

NecessaryAlways Enabled

