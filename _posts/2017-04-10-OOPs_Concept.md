---
comments: true
date: 2017-04-10 00:00:00+00:00
layout: post
slug: Basic-OOP-Concept
title: Basic C# OOP Concept
categories:
- general
tags:
- C#
- OOP
- Object-oriented programming
- Class
- method
---

> There are many explanations that we can find in internet about C# OOP, but here in my article I will give a very simple example. In this article, I will use a “House (like the houses we live in) “as a real-time example for easy understanding of OOP Concept in C#.

1. Class

**Class** is a like a Blueprint.
What is a blueprint?
Blueprint is outline drawing of our actual plan. For example if we plan to build our new home ,The Engineer will explain our new house plan with a blue print as shown in the image below. Once we finalized the plan the engineer will start building the house with same plan.
Same like blueprint class is an outline of program. Using the class we can write our own method and declare the variables and using the objects we can access the class Method and Variables. The class will be complete with Variables, Methods and Objects.

<figure class='center '>
  <a href="{{site.url}}/assets/Blueprint.jpg"><img src="{{site.url}}/assets/Blueprint.jpg" alt=""></a>
</figure>

<!--more-->

For more easy understanding of OOP with real world example here I have explained a class with House. We can imagine a House as an example for a Class. In a house, we will have rooms like living room, bedroom, kitchen and items like TV, fridge etc. House owner can access and use all the rooms and rooms' items. Same like this in a Class will have a group of **Methods and Variables**. Rooms and Rooms' Items are example for Methods and Variables. So now, we have a complete house with all rooms and rooms' items. House owner can access and use all the rooms and Rooms' Items. To access and use a Class, methods and variables here we use **Objects**. Objects are instance of a class. We will see details about objects in the next section.
What will happen if there are no rooms and items in a House?  It will be empty and no one can use the house until it has all the rooms and Items. See the below image as an example for the empty house.

EmptyHouse.jpg

Now this empty house is a Class .So what is the use of a Class without Methods and variable.
Now let’s see an example for a Complete House with Rooms and items.

CompleteHouse.jpg

So here, we have a complete house. Similarly, the Class will be complete with group of Variables, Methods and Objects. We can see details of all this in next sections of this article.
Class and objects are the base concept of OOP – Object Oriented Programming.
Here is an Example of Class -> Class should be started with the Keyword class and next we give the name for our class we can give any meaning full name as Class Name, next we will have the Open and close brackets.

{% highlight csharp %}
using System;
 
public class ClassA
{
}
{% endhighlight %}

2. Object
As we have already seen that, House Owner will access and use all the Rooms of the House and its Items. Similarly, to access all Class Method and Variable we use Objects. We can create one or many object for a same Class. Example we can say for a house there might be one or many owners.
Here is an example of Object -> Here “objHouseOwner” is the Object for a class which will be used to access all variable and Method of a class.

{% highlight csharp %}
namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            Program pro = new Program();
        }
    }
}
{% endhighlight %}

3. Variable
Variables are used to store our value. Our value can be numeric or characters for example to store a Phone no we can use `int` type variable and to store our name we can use `string` type variable with name for each variable.
Variables can be local or Global. For Example, we can say if we buy a new TV , TV Service man will come and setup the TV in our home .He will give his contact number for future contacts .Usually what we do is take a memo paper and write his contact number and keep it in a common place or in a wallet of ours. If we keep the memo in a Commonplace everyone who is visiting our house can see that contact number. Global or public variables are similar to this. If we declared the variable as Global, All the Methods inside the class can access the variable. If we store the memo Only in our wallet, we can see the contact number. Local or private variables are similar to this.

Syntax for variable:
`Access-Modifiers Data-Type Variable-Name`
By default the Access-Modifiers are by private, we can also use public to variable.
Example of Variable:

{% highlight csharp %}
namespace ConsoleApplication1
{
    // Example Program 
    class ShanuHouseClass
    {
        public int noOfTV = 0;
        private Boolean doYouHaveTV = true;
        protected String yourTVName = "SAMSUNG";
        static void Main(string[] args)
        {
            ShanuHouseClass objHouseOwner = new ShanuHouseClass();
            Console.WriteLine("You Have total " + objHouseOwner.noOfTV + " no of TV :");
            Console.WriteLine("Your TV Name is :" + objHouseOwner.yourTVName);
            Console.ReadLine();
        }
    }
}
{% endhighlight %}

basic-c-oop-concept_1.png

In Above example program I have declared two variables inside a class. In main method I have created object for class. Here we can see using the object we can access the variable of a class and display the output.
Main Method is the default Method of C#, where every console and windows application will start the program execution, In the Main Method, we can declare the Object for the class and use the object, and we can access all variables and Methods of a Class. For example, we can say there will be entrance gate for every house. Using the entrance gate we can enter inside our house. Similarly, to run our program there should be some default program execution starting Method. Main method will be useful in this program execution starting point. Whenever we run our C# Console or windows application, first the Main method will be executed .From the main method we can create an object for our other classes and call their Methods.

4. Method or Functions:
Method is a group of code statement .Now here we can see the above example program with method.
{% highlight csharp %}
class ShanuHouseClass
{
    public int noOfTV = 2;
    private Boolean doYouHaveTV = true;
    protected String yourTVName = "SAMSUNG";
 
    public void myFirstMethod()
    {
        Console.WriteLine("You Have total " + noOfTV + "no of TV :");
        Console.WriteLine("Your TV Name is :" + yourTVName);
        Console.ReadLine();
    }
}
class a
{
    static void Main(string[] args)
    {
        ShanuHouseClass a = new ShanuHouseClass();
        a.myFirstMethod();
    }
}
{% endhighlight %}

basic-c-oop-concept_2.png

> Most of developers were wondering about what is the difference between the Method and Function, both Methods and Functions are the same. Here in my article, I will use Method instead of functions. However, there is one difference in Methods and Functions, In OOP Languages like `C#, Java` etc. We use the term Method while the non-OOP programming like `C` etc. we use the term Function.

**What is the use of Methods?**
Another real-time example let’s take our mobile phone, we can say as we have Mobile phone and store many Songs in it. However, we always like to listen to the selected songs. It will be boring and hard for us to select our favorite song every time and play it. Instead of doing the same work repeatedly, we use the playlist. In playlist, we can add all-favorite songs of ours. Just click on the playlist of our collections and listen to the music. This will make our work easier and we don’t need to do the same thing again and again. Methods are used like a playlist where we can write all our repeated code in one Method and just call the method wherever we needed.
Here we can see more details about the Method.
In a house, there might be one big room or multiple rooms but each room will have different facilities, similarly in a class we can write one or multiple Methods. In a house, there might be two or three Bedrooms. Here the room name is Bedroom, but each bedroom can be different by size, color etc. This means same Rooms with different type. Similarly, in a class we can create more than one method with the same name but different parameter. In OOP it’s called as **Polymorphism** we can see details about Polymorphism later on in this article.

*Syntax for the Functions*
`Access-Modifiers Return-Type Method-Name (Parameter-List)`

  * Access-Modifiers: We will see more details about this Topic later on.
  * Return-Type: If our Method returns any value then we use the return Type with any Data Type as String, int etc., if our Method does not return any value then we use the type “Void”.
  * Method-Name: Here we give our Name for every Method, which we create
  * Parameter-List: Parameter-List or Arguments, which we pass to the function.

_**Here is an example of Method:**_
  * **Method with Void Type**: Void is a keyword which will not return any data from the Method, for example we can see the below Method with void Type, here in this method we display all our output using the `Console.WriteLine` and have used the `Console.ReadLine());` to get the Input. This Method has all Input and Output Operation but this method don’t return any value.
  
{% highlight csharp %}
// Function with void and no parameter  -- here void means no return type        
	public void veranda()
    {
        Console.WriteLine("Welcome to Veranda");
        Console.WriteLine("How Many Chairs Do you have in your Veranda");
        NoofChair = Convert.ToInt32(Console.ReadLine());
        Console.WriteLine("I have total " + NoofChair + " Chairs in my Veranda");
    }
{% endhighlight %}

  * **Method with Return Type**: Method with return type will return some result, which can be used in our program, for example, here we have Method TVNAME with return Type as `String`. We can say in our home we might have a TV in our LivingROOM and in the parent’s bedroom and also in kids bedroom .We might have different TV brand in each room, suppose if we want to know each room TV Brand Name then we need to enter the same code 3 times. Instead of writing the same code again, we can use a method with Return Type.
  
{% highlight csharp %}
// Function with Return type as String        
    public string TVNAME()
    {
        String YOURTVName;
        Console.WriteLine("Enter Your TV Brand NAME");
        YOURTVName = Console.ReadLine(); 
        return YOURTVName;
    }
{% endhighlight %}

 * **Method with Parameter-List**: So far, we have seen methods without arguments. Arguments are used to pass some data to the Method to do our process in a better way. For example, we can say we want to do a painting, to our bedrooms. We need to get the opinions of all the member of the house in order to know there choices of color for each bedroom, we can pass the member Name and their favorite color as parameter to a Method.

 {% highlight csharp %}
	//Function with parameter        
    public void BedRoom(String nameandColor)
    {
        Console.WriteLine(nameandColor);
    }
{% endhighlight %}

Same Method name with different arguments are called as Method over loading, here we can see below .Both Method has the same name but it has different arguments.

{% highlight csharp %}
// Same Function Name with Different Paramenter        
    public void BedRoom(String MemberName, String Color)
    {
        Console.WriteLine(MemberName + " Like " + Color + "Color");
    }
{% endhighlight %}

The Complete Class with Main Method Example:

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
 
// Example Program
class ShanuHouseClass
{
    public int noOfTV = 2;
    private Boolean doYouHaveTV = true;
    protected String yourTVName = "SAMSUNG";
    public int NoofChair;
 
    public void myFirstMethod()
    {
        Console.WriteLine("You Have total " + noOfTV + "no of TV :");
        Console.WriteLine("Your TV Name is :" + yourTVName);
        Console.ReadLine();
    }
    // Function with void and no parameter  -- here void means no return type       
    public void veranda()
    {
        Console.WriteLine("Welcome to Veranda");
        Console.WriteLine("How Many Chairs Do you have in your Veranda");
        NoofChair = Convert.ToInt32(Console.ReadLine());
        Console.WriteLine("I have total " + NoofChair + " Chairs in my Veranda");
    }
    // Function with Return type as String       
    public string TVNAME()
    {
        String YOURTVName;
        Console.WriteLine("Enter Your TV Brand NAME");
        YOURTVName = Console.ReadLine();
        return YOURTVName;
    }
    //Function with parameter        
    public void BedRoom(String nameandColor)
    {
        Console.WriteLine(nameandColor);
    }
    // Same Function Name with Different Paramenter       
    public void BedRoom(String MemberName, String Color)
    {
        Console.WriteLine(MemberName + " Like " + Color + "Color");
    }
    static void Main(string[] args)
    {
        ShanuHouseClass objHouseOwner = new ShanuHouseClass();
        objHouseOwner.veranda();
        String returnvalue = objHouseOwner.TVNAME();
        Console.WriteLine("Your TV BRAND NAME IS: " + returnvalue);
        objHouseOwner.BedRoom("My Name is Shanu I like Lavender color");
        objHouseOwner.BedRoom("My Name is Afraz I like Light Blue color");
        objHouseOwner.BedRoom("SHANU", "Lavender");
        Console.ReadLine();
    }
}
{% endhighlight %}
