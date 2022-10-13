[[Python]] [[Software Design Patterns]]

- software systems tend toward chaos
- over time, codebase gathers cruft & edge cases & ends up a confusing morass of manager classes & util modules
- **chaotic software systems are characterized by a *sameness of function***:
	- API handlers that have domain knowledge & send email & perform logging
	- "business logic" classes that perform no calculations but do perform I/O
	- everything coupled to everything else so that changing any part of the system becomes fraught with danger
	- "big ball of mud" antipattern
![[Pasted image 20220911132338.png]]
- 'big ball of mud' is the natural state of software in the same way that wilderness is the natural state of your garden. It takes energy & direction to prevent the collapse

### Encapsulation & abstraction are tools to tame our software "garden"
*Encapuslation*: simplifying behavior & hiding data. We encapulate behavior by identifying a task that needs to be done in our code & giving that task to a well-defined object or function. That object or function is called an *Abstraction*
		"All problems in computer science can be solved by adding another level of indirection"
### Layering
- encapsulation & abstraction hide the details & protect the consistency of our data
- we also need to pay attention to the interactions between our objects & functions
	- when one function, module, or object uses another, we say that one *depends* on the other
	- these dependencies form a graph
	- to prevent the dependency graph from becoming a "big ball of mud", we use 3-layered architecture:
1. **Presentation Layer**
	- web page, API, or command line 
2. **Business Logic**
	- business rules & workflows
3. **Database layer **
	- storing & retrieving data

### Dependency inversion principle
1. **High-level modules should not depend on low-level modules. Both should depend on abstractions**
	- *high-level modules* are the code that your organization really cares about
		- functions, classes, and packages that deal with our real-world concepts
	- *low-level modules* are the code that your organization doesn't care about
		- e.g. network sockets, algorithm implementations
	- why? we want to be able to change them independentnly of each other
		- high-level modules should be easy to change in response to business needs
		- low-level modules are often harder to change
		- we don't want business logic changes to slow down because they are closely coupled to low-level infrastructure details
		- similarly, it's important to *be able* to change your infra details when you need to, without needing to make changes to your business layer
		- adding an abstraction between them allows the two to change more independently of each other
1. **Abstractions should not depend on details. Instead, details should depend on abstractions**

# Domain Modeling
Many devs start designing a new system by building a database schema - this is where it starts to go wrong
-   **Instead, behavior should come first,** and drive our storage requirements
-   customers don’t care about the data model, they care about what the system _does_

4 key design patterns:

1.  **Repository pattern**
2.  **Service Layer**
3.  **Unit of Work pattern**
4.  **Aggregate pattern**

## What is a Domain Model?
_Domain:_ Broadly, the problem you’re trying to solve (i.e. the business)
_Model:_ map of a process or phenomenon that captures a useful property

The domain model is the mental map that business owners have of their businesses.
-   the most important thing about software is that it provides a useful model of a problem
    -   if we get it right, our software delivers value and makes new things possible
    -   if we get the model wrong, it becomes an obstacle to be worked around

-   in the business world, complex ideas & processes get distilled into a single word or phrase (“jargon”)    
    -   when we hear business stakeholders using unfamiliar words, or using terms in a specific way, we should listen to understand the deeper meaning, and encode their hard-won experience into our software
-   Have an initial conversation with the business SMEs and agree on a glossary & some rules for the first minimal version of the domain model
    -   wherever possible, ask for concrete examples to illustrate each rule