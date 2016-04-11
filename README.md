## Introduction

This project was started after yet another architect meeting where 5 people did not agree on a few code style practices.
What was important was not the disagreement itself, but the fact that I have caught myself repeating the same 
argumentation for a third time, to different people. I tried to search for some good example of conventions that are not 
just listed as a set of rules but are also accompanied by explanations why these rules must be followed and what happens 
if they are violated. I was quite surprized that I didn't find anything suitable enough, so I have started this project 
to have a place that can be referred next time.
    
## Generic code values 

This section covers principles on which coding conventions are built. We call them "code values" as every person who is 
dealing with code is expected to share these. If this is not your case, this project is probably not useful for you.    


### Maintainability 

In general, maintainability is property of code that allows to reduce risks when changing it.
In specifics it mostly boils down to a few principles:

1. Ability to change the code consistently. For example most forms of duplication are bad for maintainability, because 
 when changing the code deveper might not update both parts of duplication consistently, causing unpredictable 
 behaviour. 

2. Ability to see the impact of the change. It mostly affects the way how the code is structured. 
 This principle is difficult to formalize and there will not be many rules that refer to it directly, however it is 
 easy to provide examples of what happens when it is violated.  

3. Ability to use automated tools for various aspects of software lifecycle activities, such as building, deploying,
 monitoring, merging between branches. If your software can't be built using CI or can't be deployed without changing 
 the contents of the package produced by build system - this principle is clearly violated.
  
4. Resistance to human errors. What happens if when adding new functionality or making a required change in an existing 
 feature developer changes something that he shouldn't have touched? If your tests break or the program will not start,
 you are probably good. If you will get some obscure runtime exception - likely no. This aspect generally relates to 
 input validations, error handling and having adequate tests.  
  
 
### Readability

The time developers spend reading the code is an order of magnitude greater than time they spend writing it.   
Always think about the person who will be reading your code from the following aspects:

1. Call your code via API without getting into detail how it works.

2. Understand how your code works.

3. Updating your code to add new features.

4. Perform code review.

All above mentioned aspects are equally important.


## Java Conventions

[Source Files](java/sources.md)

[Class Structure](java/class-structure.md)

[Formatting](java/formatting.md)

[Naming](java/naming.md)


## References

This project is largely based on the sources listed below, with some sections shamelessly borrowed from them.

Oracle Java coding convention: 
http://www.oracle.com/technetwork/java/javase/documentation/codeconvtoc-136057.html

Google style guide: 
https://google.github.io/styleguide/javaguide.html 

Checkstyle documentation: 
http://checkstyle.sourceforge.net/checks.html