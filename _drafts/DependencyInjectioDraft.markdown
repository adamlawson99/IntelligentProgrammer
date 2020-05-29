---
layout: single
title:  "Dependency Injection W/ Examples In C# (draft)"
date:   2020-05-28 16:30:00 -0400
classes: wide
categories: 
- programming
- SOLID
---
## SOLID Principles
The SOLID principles were first introduced in 2000 by Robert C. Martin in his paper titled _Design Principles and Design Patterns_[1](https://web.archive.org/web/20150906155800/http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf). While the acronym wasn't yet coined at the time, what we now know as SOLID serves a general set of concepts and ideas for making clean, maintainable and easy to read code. S.O.L.I.D corresponds to the 5 following principles:

* Single responsibility principle
* Open-closed principle
* Liskov substitution principle
* Interface segregation principle
* Dependency inversion principle

SOLID principles came to be as a remedy to the current issues of software development. Whether it was spaghetti code, depencies running rampant, functions handling multiple tasks or unmaintainable code, the current state of software development was rough. If code had to be changed or functionality altered the reprecussions from changing a single line of code could spread throughout the entire code base, spreading like a virus. Long nights spent rewriting code and the frustration that came along with it meant something had to change, and it did.

Today we are going to specifically be looking at the last principle, the dependency inversion principle. I plan to cover the four remaining principles over the coming week. I'm starting with the dependency inversion principle because I find it the most fascninating and the most fun!

## Dependency Inversion Principle
The dependency inversion tells us that our code modules should depend on interfaces and abstractions rather than concrete classes. It also states that we should "invert" the way we think about depencies. Let's tackle each of these one at a time.

The idea of depending on interfaces or "coding to interfaces" establishes a contract between the calling classes and the objects they need. The interface **guarantees** that any class that implements that interface will support the methods and contain the properties described by the interface. We don't care how the concrete class implements such methods, just that it does. For example, let's imagine a future where we have pizza shops that are run by robots. These robots take in orders for different types of pizzas and prepare them for the customers. Each pizza has different ingredients and needs to be cooked for a specific length. How would we code a method to cook our pizzas? We could code a bunch of methods that each take a different type of pizza, such as the following:
```cs
    cook(Peperroni pizza);
    cook(MeatLover pizza);
    cook(Veggie pizza);
    //etc. etc. etc.
```
This is quite combersome and if our pizzas change then we need to rewrite the methods. Another approach could be numerous if-else satements like this:
```cs
    cook(Pizza pizza){
        if(pizza.type == "Pepperoni"){
            //do something
        }
        else if(pizza.type == "Veggie"){
            //do something
        }
        ///and on and on and on
    }
```
This isn't really much better, there must be another way. In come interfaces to save the day. With interface we can make our cook method take an interface rather than a concrete class. We then know that every pizza implementing the IPizza interface will implement the methods and properties found in that interface. Our properties could be a list of ingredients for each pizza, and a cooking time, like so:
```cs
    interface IPizza
    {
        public string[] Ingredients { get; set; }
        public int CookingTime { get; set; }
    }
```
Each pizza will implement the IPizza interface and will therefore have to have an ingredients list and a cooking time. If you're confused by the syntax just know that "{get; set;}" replaces the need to declare seperate methods for getters and setters. Now when it comes to cook a pizza, we don't have to worry about handling all the different types of pizzas, we can just code a general method for handling all the pizzas. Here is our pizza shop which uses the IPizza interface to cook pizzas:

```cs
    class PizzaShop
    {
        public void takeOrder(Order ord)
        {

        }
        public void Cook(IPizza pizza)
        {
            //prepare the pizza
            foreach(string ingredient in pizza.Ingredients)
            {
                //add the ingredients to the pizza
            }
            //cook the pizza for the specified time
            Thread.Sleep(pizza.CookingTime);
        }
    }
```

Here is how we would implement the interface for each pizza:
```cs
    public class PepperoniPizza : IPizza
    {
        public string[] Ingredients { get; set; }
        public int CookingTime { get; set; }

        public PepperoniPizza()
        {
            Ingredients = new string[] {"sauce","cheese","pepperoni"};
            CookingTime = 20;
        }
    }
```

In reality we wouldn't actually create pizza objects like this as a seperate class. We would most likely either fetch cooking times and ingredients for a database or have some type of master config file that holds the information for each pizza.

The next part of the dependency inversion principle I'd like to address is that of creating objects. By their very nature, interfaces and abstract classes cannot be instantiated, therefore if we wish to use them their concrete implementations have to instantiated somewhere in our code. But that we are then creating dependencies on concrete implementations, I thought we got rid of those with interfaces?! Sort of. The idea of coding to interfaces and hiding concrete implementation behind abstractions is good, it gets us about 3/4 of the way to where we would like to be. Typically, we have some sort of data access layer that provides access to the database and needs to be instantiated by our business logic layer. Meaning somewhere in our main code we have to call new DataAccess() and call the methods on it. There's a problem here, and that problem is that our business logic layer shouldn't have to do that. Even if we code to an interface our business layer still has to create some type of concrete implementation of the interface. Dependency injection (DI) solves that problem for us. By using DI we delegate the responsiblity of instantiating these classes to a seperate entity.

By using DI we request an already instantiated class of a certain type. This comes with numerous advantages. Imagine in our business layer we need to instantiate a concrete class that implements that IDataAccess interface. We can pass that through the constructor when we run our application, without having to create it ourselves. Furthermore, we can also swap out which concrete implementation we are providing it, by changing code in our DI controller, which will propogate down towards the other classes. Another area that DI improves is unit testing. When unit testing we want to avoid messing around with production data and infrastructure. For example, in our business layer we may want to test that our requests to our data layer are working properly. We could do this by storing our connection string in a config, and having different values configured for production and development. This would be great for testing our data access layer but it doesn't matter for our business layer. Our business layer doesn't know and doesn't care if the information coming from the database is correct or not, it simply cares that it sends a request, receives a response and does something with. What if we create a dummy data access class instead? This is a good, idea, except that it means each time we want to unit test the business layer we need to find all the instances of the data access class in our code and swap them for our dummy class, but there's a better.

Mocking classes with mock objects allows us to simulate the behaviour of a certain class by replacing the class with need with the simualted version. Essentially we are testing that the calling class calls the class in question correctly and processes the response as it should. DI places a crucial role here, because it allows us to inject the "mock class" instead of the real class by simply changing the code in one place. In our DI configuration all we have to change is the class that is provided when an implementation of a certain interface is requested, that's it. Easy right? The best way to understand DI is by seeing concrete so let's take a look at some C# code without DI and with DI using the nuget package AutoFac.

## Dependency Injection in C#
All the code for these examples can be found on my github linked below, I'll include the start code and end code. Our demo program for this example will be a simple program for managing a retail store. Our program has a Manager, which depends on an InventoryManager and a ScheduleCreator.
Here is the code for each of the classes:

```cs
    public class Program
    {
        static void Main(string[] args)
        {
            Manager manager = new Manager();
            manager.MakeSchedule();
            manager.ManageInventory();
        }
    }
```
The main program that starts and runs our application.
```cs
    public class Manager
    {
        ScheduleCreator scheduleCreator;
        InventoryManager inventoryManager;
        public Manager()
        {
            scheduleCreator =  new ScheduleCreator();
            inventoryManager = new InventoryManager();
        }

        public void MakeSchedule()
        {
            Console.WriteLine("Beginning schedule creation");
            scheduleCreator.CreateSchedule();
            Console.WriteLine("Schedule creation finished");
        }

        public void ManageInventory()
        {
            Console.WriteLine("Beginning inventory management");
            inventoryManager.TabulateInventory();
            Console.WriteLine("Inventory management finished");
        }
    }
```
The manager class, which creates an instance of ScheduleCreator and InventoryManager.

```cs
    public class ScheduleCreator
    {
        public void CreateSchedule()
        {
            Console.WriteLine("Creating schedule");
            Console.WriteLine("Schedule created");
        }
    }
```
The schedule creator class
```cs
    public class InventoryManager
    {
        public void TabulateInventory()
        {
            Console.WriteLine("Calculating remaing inventory");
            Console.WriteLine("No items needing restocking");
        }
    }
```
The inventory manager class

Here is what the output for our program looks like:

    Beginning schedule creation
    Creating schedule
    Schedule created
    Schedule creation finished
    Beginning inventory management
    Calculating remaing inventory
    No items needing restocking
    Inventory management finished

Let's now look at how we can improve this program. As we discussed earlier, we should be coding to interfaces, not concrete classes. What if I want to swap out the inventory manager for a different inventory manager? I can't guarantee that the new manager will support the methods I need. That's the first change we are going to make, creating interfaces for our scheduleCreator and inventoryManager objects.

We know have two new interfaces:
```cs
    public interface IScheduleCreator
    {
        void CreateSchedule();
    }

    public interface IInventoryManager
    {
        void TabulateInventory();
    }
```

Our ScheduleCreator and InventoryManager classes now look implement the interfaces as so:

```cs
    public class InventoryManager : IInventoryManager
    {
        public void TabulateInventory()
        {
            Console.WriteLine("Calculating remaing inventory");
            Console.WriteLine("No items needing restocking");
        }
    }

    public class ScheduleCreator : IScheduleCreator
    {
        public void CreateSchedule()
        {
            Console.WriteLine("Creating schedule");
            Console.WriteLine("Schedule created");
        }
    }

```

We can now change our Manager class to support the IScheduleCreator and IInventoryManager interfaces, instead of a concrete implementation of them. Let's also make so that our top level class creates these instances for us and passes them into our Manager class:

```cs
    class Program
    {
        static void Main(string[] args)
        {
            ScheduleCreator scheduleCreator = new ScheduleCreator();
            InventoryManager inventoryManager = new InventoryManager();
            Manager manager = new Manager(scheduleCreator, inventoryManager);
            manager.MakeSchedule();
            manager.ManageInventory();
        }
    }


    public class Manager
    {
        IScheduleCreator _scheduleCreator;
        IInventoryManager _inventoryManager;
        public Manager(IScheduleCreator scheduleCreator, IInventoryManager inventoryManager)
        {
            _scheduleCreator = scheduleCreator;
            _inventoryManager = inventoryManager;
        }

        public void MakeSchedule()
        {
            Console.WriteLine("Beginning schedule creation");
            _scheduleCreator.CreateSchedule();
            Console.WriteLine("Schedule creation finished");
        }

        public void ManageInventory()
        {
            Console.WriteLine("Beginning inventory management");
            _inventoryManager.TabulateInventory();
            Console.WriteLine("Inventory management finished");
        }
    }
```

Let's run down what we've done so far:
* Refactored our ScheduleCreator and InventoryManager classes into implementations of their respective interfaces
* Refactored the Manager class to take IScheduleCreator and IInventoryManager in the constructor, instead of instatiating them ourselves
* Refactored the program class to pass down the ScheduleCreator and InventoryManager to the Manager class

We haven't changed a lot, but we've already made big improvements to our code. Since we are now working with interfaces, we can swap out the implementations of our ScheduleCreator and InventoryManager as we please. Our Manager is also no longer responsible for instantiating the interface implementations it requires. We've done all this, yet we still haven't used Dependency Injection (DI), which is the last missing piece of our puzzle. 

DI works by creating a container that handles the requests for the interface implementations. So when we To implement the DI in our program we will be using AutoFac as stated above. The first thing we have to do is create the ContainerConfig class. This class will setup the DI so that whenever a IScheduleCreator or IInventoryManager class is requested, it will be created for us and passed into the constructor. We will set it up as follows:

```cs
    public static class ContainerConfig
    {
        public static IContainer Configure()
        {
            //stores the definitions of the classes we want to instantiate
            var builder = new ContainerBuilder();
            builder.RegisterType<Application>().As<IApplication>();

            //register class schedule creator, whenever an IScheduleCreator item is requested,
            //return an instance of ScheduleCreator
            //when they need this, give them that
            builder.RegisterType<Manager>().As<IManager>();

            builder.RegisterAssemblyTypes(Assembly.Load(nameof(StoreManager)))
                //in the demo library find where the namespace of the object contains the word utilities 
                .Where(item => item.Namespace.Contains("Utilities"))
                //get interface where 
                .As(itemInterface => itemInterface.GetInterfaces().FirstOrDefault(item => item.Name == "I" + itemInterface.Name));
            //builds the container based on our configuration and returns it
            return builder.Build();
        }
    }
```
What we are doing in the ContainerConfig class is registering all the classes we need without DI provider. We have to register them so that our DI provider knows which class to serve when the request for a certain class comes in. The line "builder.RegisterType<Manager>().As<IManager>();" translated to plain english means "when a class asks for a IManager implementation, instantiate the Manager class and give it to them".

We also replace the code in Program.cs with the follwing:

```cs
        static void Main(string[] args)
        {
            //setup our dependency injection container and register dependencies
            var container = ContainerConfig.Configure();

            using (var scope = container.BeginLifetimeScope())
            {
                //saying we need IApplication object
                //shouldn't have to do this often
                //if we do this often it means we have a problem
                var app = scope.Resolve<IApplication>();
                app.Run();
            }

            Console.ReadLine();
        }
```
This handles our container setup for us, creating a new container that begins at Runtime and lasts throughout the lifecycle of our applciation. It's important specify BeginLifetimeScope() here because we want to prevent memory leaks. Memory leaks occur because resources aren't getting released by proccess that no longer need them. Once app.Run() terminates and we exit the using statement, this container gets destroyed, and its resources freed up for use by other applications. This Main() method acts as a bootstrapper, to start the dependency injection service and run our application, let's now take a look at the Application being called by Resolve<IApplication>();

```cs 
    public class Application : IApplication
    {
        IManager _manager;
        public Application(IManager manager)
        {
            _manager = manager;
        }

        public void Run()
        {
            _manager.MakeSchedule();
            _manager.ManageInventory();
        }
    }
```

The Application class implements the IApplication interface and creates the runs the methods on the manager. The Appllication class _requests_ an IManager implementation, which we registered in our ContainerConfiguration class. Our container then fills the request by creating the specified implementation of that interface. What about the Manager class? Doesn't it depend on IScheduleGenerator and IInventoryManager? It does, and thankfully our container handles that for us. Our container looks at the IManager implementation specified and wil also instantiated the dependencies needed for that class as well.

I can't stress enough how much easier that makes our task. We don't have to worry about instantiating classes and passsing them down. All we have to do is register them in our container and it handles the rest. It doesn't have to be limited to Interfaces either, we can inject strings, integers, lists, booleans and anything else we might need. It really is a great solution, that makes programming a lot more fun.

Let us now take a look at how easy it is to swap out a class for another one, I created the class BetterManager which also implements the IManager interface:

```cs
    public class BetterManager : IManager
    {
        IScheduleCreator _scheduleCreator;
        IInventoryManager _inventoryManager;
        public BetterManager(IScheduleCreator scheduleCreator, IInventoryManager inventoryManager)
        {
            _scheduleCreator = scheduleCreator;
            _inventoryManager = inventoryManager;
        }

        public void MakeSchedule()
        {
            Console.WriteLine("Beginning schedule creation");
            Console.WriteLine();
            _scheduleCreator.CreateSchedule();
            Console.WriteLine();
            Console.WriteLine("Giving Employees time off");
            Console.WriteLine("Schedule creation finished");
        }

        public void ManageInventory()
        {
            Console.WriteLine("Beginning inventory management");
            Console.WriteLine();
            _inventoryManager.TabulateInventory();
            Console.WriteLine();
            Console.WriteLine("Inventory management finished");
        }
    }
```

All I have to do to use this new is to go into ContainerConfiguration and change the implementation I want to use:

```cs 
builder.RegisterType<BetterManager>().As<IManager>();
```

We now run our program and:
    Beginning schedule creation

    Creating schedule
    Schedule created

    Giving Employees time off
    Schedule creation finished
    Beginning inventory management

    Calculating remaing inventory
    No items needing restocking

    Inventory management finished

Success! That's all it took, changing 1 line of code to use the new class, no extra finnicking involved. It is truly an awesome way of building complex projects and eliminating the dependency on object instantion.

## Closing Thoughts
If you've made it to the end, congrats! I know this article was a bit of a long one but I really wanted to share my excitement of dependency injection with you. I find it super cool and will be trying to implement it where appropriate. There are also times we shouldn't use dependency injection, such as if know that the implementation we require will never change. Say we require a string builder for our class, and create it in our constructor. It's highly unlikely that our implementation will ever need to change, so there's no real advantage to injecting it into the constructor. Like everything else, there is a time and a place for dependency injection. I hope you enjoyed this article on dependency injection and that you've learned something. I hope to cover the rest of the SOLID principles in upcoming so stay tuned for thos and have a great day! 

