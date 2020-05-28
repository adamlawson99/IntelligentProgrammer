---
layout: single
title:  "Dependency Injection W/ Examples In C# (draft)"
date:   2020-05-24 16:30:00 -0400
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

The idea of depending on interfaces or "coding to interfaces" establishes a contract between the calling classes and the objects they need. The interface **guarantees** that any class that implements that interface will support the methods and contain the properties described by the interface. We don't care how the concrete class implements such methods, just that it does. For example, let's imagine a future where we have pizza shops that are run by robots. These robots take in orders for different types of pizzas and prepare them for the customers.
