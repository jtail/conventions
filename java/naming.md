### Naming of program elements

Other sections of this paper mostly contain rules that are designed to help the developers think less and make many
decisions automatic. This section is different and contains a decent number of rules, that are designed to force
developers to think *more*, at the right time.

Names of all program elements should be:

#### Names are contextual

Do not duplicate information which is already available on context where another developer will be reading the name.
Avoid using "hungarian" notation and similar practices to embed information about type or scope into the name. 

    public class Chicken {
        // AVOID - field and method name are defined in the context of class so the word "chicken" is aready on context
        private long chickenId;
        private long getChickenName() {...}

        // PREFER
        private long id;
        private long getName() {...}
    }

Motto: Just like any other kind of duplication, violating this rule causes maintainability issues. If you later decide
to rename `Chicken` class into `Paultry`, name `getChickenName` becomes misleading. However changing it will affect 
multiple classes that use it and might become a nightmare if it is already part of published API.   


#### Descriptive

Name of the program element should describe **what** it is (or **what does it do** for methods). 


#### Concise

Useless information should not be there. If some fragment of the name is not immediately useful to the developer who 
will read your code, it should not be placed in the name. Even if you english teacher tells you otherwise.

    // AVOID 
    String nameOfTheDragon;
    
    // PREFER
    String dragonName;

Motto: Loss of time spent by brain when parsing extra characters.


#### Use of abbreviations 

##### Prefer introducing abbreviations at package level 

Limit use of abbreviations to high-level concepts that normally map to packages.  

Motto: Abbreviations are only good if they describe well-established, easily recognizable things and human brain can 
keep a limited number of them in "hot" memory. However package names are seen often enough to work around this 
limitation quite a bit.  
 

#####  Abbreviations must be explained in the javadoc comment

Especially if you are using one that is not well-known or explanation is not easily found.
For packages this javadoc should be placed in package.java. 


##### Use abbreviations consistently and document them on top level

Avoid using expanded form of abbreviation in naming of program elements.

Motto: 
If you use both forms, running a full-text search against one of them does not provide results containing the other.


### Packages

Package names must contain only **lowercase english characters**, with consecutive words simply concatenated together (no underscores)
Consider using the "domain-based convention" described in JLS.

Motto: Clearly distinguish package names from other language constructs. 

    // AVOID 
    package com.example.deepSpace; 
    package com.example.deep_space;
    
    // PREFER
    package com.example.deepspace; 

#### Top-level package name

The sole purpose for the top-level package is to easily distinguish your code from the 3rd party code you use.
A common approach is to use something like `com.company.projectname` as a root package, assuming you company owns
the domain `company.com`. This may not great approach for a number of cases, for example:

1. Overly long and difficult to spell company domain. Imagine working for `chickensandbulldogsoftware.com`. 
2. Company domain does not render into a valid package name, for example `222doors.com` -> `com.222doors` = invalid
3. Company name can change because of rebranding. 

In such situation it is better to aquire some short-named 'technical' domain which has no business meaning or value and 
use it for package naming.


#### Prefer noons in package names  

If your package name consists of a single word, it should be a noon.
If you want to use 2 or more words, at least the last word should be a noon.


#### Abbreviations in package names  

If you find that you need 3 or more words, consider using abbreviation.
 
Motto: Later on you might have to use package name as class name prefix (simply because you need to put something for 
disambiguation and don't have anything else), so it should be short enough not to result in excessively long class name.
Imagine you are working for company providing VPS services and decided never to abbreviate: this might end up with class
names like com.mycompany.customerservices.virtualprivateservers.VirtualPrivateServerPasswordResetRequestValidator 


#### Prefer plural for 'logical' package names

And if you are in doubt for name of a 'functional' package, sticking to this rule might save you some time deciding 
between `util` and `utils`. However many 'functional' names describe concepts that do not sound well when pluralized: 
`security`, `validation`, etc.   
    
Motto: This rule has no practical value, aside of helping developers to agree on a specific form quickly. 
It merely attempts to capture the way how most respected open-source Java projects handle the choice between plural and 
singular. If you require some quick-and-hard rule instead, it is recommendend to replace it with list of specific forms
that are considered appropriate for your project.
   

### Classes

The context for a public class is the name of the package where it resides. However when the developer observes usages 
of the class, he will typically not see the package name, as it will be hidden somewhere in the imports. Therefore, it is 
often a good idea to duplicate some info from the package name.
       
    com.example.payments.PaymentSchedule
    
#### Class names are built with generic to specific right to left     
    
#### Avoid giving prefixes and suffixes to classes that describe meaningful abstractions 

This rule applies to all interfaces as they describe abstractions by design.
Classes are exempt from it, only if they *already implement* some "meaningful abstraction" on 1-on-1 basis.

For example, if you do not already have an interface named `Bear`, you should not call call your class `AbstractBear`.

    // AVOID
    class Bar extends AbstractBar

However if you have class `Chicken` and need to extract some functionality into abstract class from it, you should not
call that class `AbstractChicken`. Instead, you have to choose one of the following:

1. Give the abstract class a different name. Maybe it is not that much left in the abstract class that identifies it
as chicken and it should be `Bird` instead.
2. Leave the name `Chicken` with the abstract class and provide a different name for implementation, for example 
`FastChicken`. In the worst case you will end with something like `DefaultChicken`, if no disambiguation basis exists
in particular situation. 

In either cases, most usages of your class should not be affected by the refactoring immediately. Of course, you might 
later justify that some code that works with your `Chicken` can be applied to a `Bird`, but that should be doable in a
separate refactoring commit.

Motto: Incremental, logically simple refactoring operation such as extracting superclass and interface should not have 
a huge blast radius of changing dozens of files. Also, this rule forces the developer to think on the proper name of 
the class at the right time - when it is created and before it is already used in many places that are hard to change. 

#### Test classes are named starting with the name of the class they are testing, and ending with Test

Thus, ending the class name with `Test` explicitly marks it as such. Avoid using `Test` in any place of classes in 
production codebase to prevent confusion. For example: `HashTest` or `HashIntegrationTest`.

Motto: Have a quick and defined way to find tests for a specific class.
 

### Instance fields 

#### Instance fields names use camelCase and contain only alphanumeric characters

First word in lowercase, internal words capitalized. Avoid using underscore (_) and other non-alphanumeric characters. 


#### Keep private field names reasonably short. 

For fields, context is class where they belong and their type. And both of these are typically defined in the same file. 
So if you have something else to say, you can omit any words that are already present in type and class. Example:  

    class PaymentScheduleSyncService {
        // AVOID
        private PaymentScheduleValidationService paymentScheduleValidationService;           
        // PREFER
        private PaymentScheduleValidationService validationService;
    } 

If the field is private and has no accessor methods, abbreviating is also acceptable. Even if abbreviation becomes 
ambiguous in the future, renaming it is a trivial operation that has no effect outside the class.  

    class CriteriaExecutor {
        // PREFER (a very common abreviation)
        private EntityManager em;
        // Acceptable too 
        private CriteriaBuilder cb;
    }

Note: Keep in mind that if you later have to add getter method for such field, a non-abbreviated form should be 
used. For example to expose `em`, you should use `getEntityManager` instead.

Fields that have a function should reflect it in the name. 
    
    public class ConditionalInterceptor implements Interceptor {
        // PREFER 
        /** Used to delegate calls when condition is satisfied. */
        private Interceptor delegate;
    }


### Variables

<!-- TODO -->


### Methods

#### Method names use camelCase and contain only alphanumeric characters.

First word in lowercase, internal words capitalized. Avoid using underscore (_) and other non-alphanumeric characters in 
production code. 

Note: In tests underscore can occassionally be useful to clearly separate name of method under test and test case name, 
for example: `createUser_InvalidName`. And this is one extra reason why you should not use it in production code.


#### Method name should start with verb in present simple

The verb is followed by one or more words that give further details on the action being performed by the method. 
Ensure that from the name it is clear whether method will have side-effects or not.            

    public interface UserService {
        User create(String name, String password);
        void updateName(String id, String name);
    }

Exception: Helper method that builds an instance based on arguments can be named same as class it builds with the first 
letter lowercased:
 
    static Address address(String line1, String line2, String city, String country) {
        Address a = new Address();
        a.setLine1(line1);
        a.setLine2(line2);
        a.setCity(city);
        a.setCountry(country);
        return a;
    }

#### For methods, context is class name and parameter types. 

Therefore they generally should not be duplicated in the method name.
Keep in mind that does not apply to parameter names, that are not part of the method signature.
Information available from method return value type usually should not be duplicated in method name, except for cases when 
multiple methods would end up with a signature conflict otherwise.

Motto: Even with the compiled code, parameter types are available to the developer.

Examples:

    public interface TokenService {
        // Avoid:
        public Batch<Token> getUpdatedTokensBatchBetweenFromAndToDates(Date from, Date to)
        // Prefer:
        public Batch<Token> findUpdatedBetween(Date from, Date to);
        public Batch<Token> findUpdatedBefore(Date date);
        public Batch<Token> findUpdatedAfter(Date date);
        //
        Set<Token> getAll();  
    }


#### Examples of typical method name prefixes

**set** - Simple setter: no return value or builder-style "return this" for call chaining. 

    void setLastName(String value) {...}
    static class Builder {
        Builder setEmail(String value) {...}
    }

**get** - Simple getter or lookup method 

    String getLastName();
    getUser(String name);

**by** - Lookup object(s) with a property somehow matching given argument.   

    public interface UserService { 
        User byId(String id) {...}
        List<User> byNickname(String nickname)
        Stream<User> byRegistrationDateBefore(Date value)
        Stream<User> byNameAndSurname(String name, String surname)
    }
Note that return type is on context so it should not be duplicated in method name.

**find** - Lookup method, retrieving one or more items that match a specific condition. Compared to 'by' prefix, this 
one is used when search criterion in cases when no property exists. 
    
    public interface UserService { 
        Stream<User> findUnverified();
        List<User> findRegisteredAfter(Date value); 
        Batch<User> findUpdatedBetween(Date from, Date to);
    }


