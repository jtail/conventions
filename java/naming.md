### Naming of program elements

Other sections of this paper mostly contain rules that are designed to help the developers think less and make many
decisions automatic. This section is different and contains a decent number of rules, that are designed to force
developers to think *more*, at the right time.

Names of all program elements should be:

#### Contextual

Do not duplicate information which is already available on context where another developer will be reading the name.
Avoid using "hungarian" notation and similar practices to embed information about type or scope into the name.
 
For class members, such as fields, methods and inner classes, context is class name. 
For variables and parameters, context is class name + method name. 

    public class Chicken {
        // AVOID - field and method name are defined in the context of class so the word "chicken" is aready on context
        private long chickenId;
        private long getChickenName() {...}
        private static class ChickenType {...}

        // PREFER
        private long id;
        private long getName() {...}
        private static class Type {...}
    }

Motto: Just like any other kind of duplication, violating this rule causes maintainability issues. If you later decide
to rename `Chicken` class into `Paultry`, name `getChickenName` becomes misleading. However changing it will affect 
multiple classes that use it and might become a nightmare if it is already part of published API.   


#### Descriptive

Identifier should describe **what it is** (or **what does it do** for methods). Especially, it should not mislead 
reader into an assumption that is not guaranteed to be true. If such situation appears as a result of refactoring, it 
must be either fixed as part of the same refactoring commit or deprecated, providing properly named alternative.
    
    // AVOID
    Set<Bar> listBars(Criteria<Bar> filter);
    Cat dog = new Cat();
    

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
 

##### Abbreviations must be explained in the javadoc comment

Especially if you are using one that is not well-known or explanation is not easily found.
The javadoc comment must appear at the level where abbreviation is introduced.
For packages this it should be placed in package.java. 


##### Use abbreviations consistently

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


#### Camel case: defined 

Most of the identifiers in java programs (except packages and constants) use "camel case". Sometimes there is more than 
one reasonable way to convert an English phrase into camel case, such as when acronyms or unusual constructs like "IPv6" 
or "iOS" are present. To improve predictability, it is recommended to use the following (nearly) deterministic scheme 
(proposed by Google Style).

Beginning with the prose form of the name:

1. Convert the phrase to plain ASCII and remove any apostrophes. For example, "Müller's algorithm" might become 
  "Muellers algorithm".
2. Divide this result into words, splitting on spaces and any remaining punctuation (typically hyphens).
  - Recommended: if any word already has a conventional camel-case appearance in common usage, split this into its 
  constituent parts (e.g., "AdWords" becomes "ad words"). Note that a word such as "iOS" is not really in camel case 
  per se; it defies any convention, so this recommendation does not apply.
3. Now lowdercase everything (including acronyms), then uppercase only the first character of:
   - ... each word, to yield upper camel case, or
   - ... each word except the first, to yield lower camel case
4. Finally, join all the words into a single identifier.

Note that the casing of the original words is almost entirely disregarded. Examples:

| Prose form                | Prefer            | Avoid             |
| ------------------------- | ----------------- | ----------------- |
| "XML HTTP request"        | XmlHttpRequest    | XMLHTTPRequest    |
| "new customer ID"         | newCustomerId     | newCustomerID     |
| "inner stopwatch"         | innerStopwatch    | innerStopWatch    |
| "supports IPv6 on iOS?"   | supportsIpv6OnIos | supportsIPv6OnIOS |
| "YouTube importer"        | YouTubeImporter YoutubeImporter* |    | 

*Acceptable, but not recommended.

Some words are ambiguously hyphenated in the English language: for example "nonempty" and "non-empty" are both correct. 
For better deteminism, prefer hyphenated version as "prose form" for such cases.
   

### Classes

The context for a public class is the name of the package where it resides. However when the developer observes usages 
of the class, he will typically not see the package name, as it will be hidden somewhere in the imports. Therefore, it is 
often a good idea to duplicate some info from the package name.
       
    com.example.payments.PaymentSchedule
    
#### Class names
     
Class names are typically nouns or noun phrases. For example, `Character` or `ImmutableList`. Interface names may also be 
nouns or noun phrases (for example, List), but may sometimes be adjectives or adjective phrases instead (for example, 
Readable).

When building class hierarchy, if words that describe subclass specifics are added to superclass name, they must be 
added at the beginning:
 
    // PREFER
    public class LinkedHashMap extends HashMap  
    // AVOID
    public class StudentHomeProjectValidator extends StudentProjectValidator
    
This might occassionaly result in excessively long class names. When this happens, consider dropping words in the middle
of the name that carry least information. For example, in most cases `HomeStudentProjectValidator` can be shortened to
`HomeProjectValidator`, assuming your system does not hame to track teacher home projects already.    
A total of 4 words should ring a bell and 5 are a clear sign that you are doing something wrong.

Also, avoid copying superclass name when subclass name describes instance of superclass by definition.  
    
    // PREFER 
    class Falcon extends Bird
    // AVOID - falcon is a bird already, so this is duplication  
    class FalconBird extends Bird
    
Note: This convention enforces no specific rules for naming annotation types. 
    

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

*Terminology note:* The word 'variables' is used in the broad sense here, including parameters of methods and lambda 
expressions (but not fields).  

#### Prefer short, minimally descriptive variable and parameter names

Variables are highly contextual. Class name, method name and variable type are all on context so there are a lot of 
cases when you will have very little to say in the variable name. They are also very local, so even if you later change 
the method causing the variable name to become ambiguous, refactoring is safe and easy. 
 
    // AVOID
    public class UserService {
        Stream<User> findAllBlocked() {
            Stream<User> eternallyBlockedUsers = findBlockedEternally();
            Stream<User> temporarilyBlockedNotOverriddenUsers = findBlockedTemporarily().filter(u -> !hasAccessOverride(u));
            return Stream.concat(eternallyBlockedUsers, temporarilyBlockedNotOverriddenUsers).distinct();
        }
    }     

    // PREFER  
    public class UserService {
        Stream<User> findAllBlocked() {
            Stream<User> eternally = findBlockedEternally();
            Stream<User> temporarily = findBlockedTemporarily().filter(u -> !hasAccessOverride(u));
            return Stream.concat(eternally, temporarily).distinct();
        }
    }     
    
Motto: 
Reading the exact same words that are already on context multiple times does not help to understand the code better.
In fact, they often make it worse, as the important words that make the difference become hidden in the forest of those 
repeated words so the reader has to spend more time finding them.

Note:
In many cases, everything that needs to be said about the variable is already known on context. In this case, choose one 
word to duplicate from context which is most specific. Note that this choice depends on other variables on context as
well:
    
    void remoteLogin(LocalUser local, RemoteUser remote)
    void updateProfile(LocalUser user, LocalProfile profile)

If you absolutely have nothing more to say about the method argument or variable, than is already known from context, 
giving it a meaningful name might be problematic. In such case, it is just fine to give it a short, even single-letter 
name. At least, it will not to become misleading later. A common example of such situation is arithmetic methods, where 
arguments are just arguments.  

    // Acceptable
    public static double max(double a, double b)

However do not be easily caught into the trap of missing the important information, using this exception as an excuse.
  
    // AVOID
    public static double pow(double a, double b)
    // PREFER
    public static double pow(double base, double exponent)


### Methods

*Terminology note*

**Imperative** method has observable side-effects (such as mutation of mutable objects or output to devices) and is 
intended to be called to achieve those. Examples: PrintStream.println(), List.add(), StringBuilder.append(). 

**Pure method has no observable side-effects and always returns the logically same value given the same arguments. 
The result value cannot depend on any hidden information or state that may change while program execution proceeds or 
between different executions of the program, nor can it depend on any external input from I/O devices. 
Examples: Math.max(), String.length().

**Dirty** methods, belong to neither of these categories. Examples include methods that are invoked to external state 
(`UserRepository.byId(String userId)`) or achieve some state (`UserService.ensureBlocked(String userId)`)


#### Method names use lower camelCase and contain only alphanumeric characters.

Avoid using underscore (_) and other non-alphanumeric characters in production code. 

Note: In tests underscore can occassionally be useful to clearly separate name of method under test and test case name, 
for example: `createUser_InvalidName`. And this is one extra reason why you should not use it in production code.


#### Imperative method names start with verb

Names of imperative methods should start with verb in infinitive form, optionally followed by one or more words that 
give further details on the effects of the method.            

    public interface UserService {
        User create(String name, String password);
        void updateName(String id, String name);
    }

Motto: Explicitly communicate to the reader that method has side-effects and what exactly they are.
 

#### Avoid starting pure method names should with verb in infinitive

Motto: Allow reader to distunguish that method is called for its value and not for side 
Since pure methods have no side effects, the whose essense of them is defined by specifics of the returned object  

There are no explicit limitations on naming 'dirty' methods. They can start from the verb, a noun or even a 
preposition. All of the following examples are legal: 

    class EmailService { List<Email> findUnconfirmed() ... }
    class Files { Stream<String> lines() ... } 
    class AddressServiceTest { Address address(String line1, String line2, String city, String country) ... }   

#### Recommendations on method naming

If the noun name of what is being returned is not on context, include it in the method name:

Use singular form when method returns one item: `java.nio.file.Files.lines()`. Those are typically helper methods 
that build an instance based on arguments, or same as class it builds with the first letter lowercased. Prefer names 
that start with adjective or gerund communicating the specifics. However, there will be times, when no such information
exists - especially in test code when what you need is just some object in context of the test.

    // PREFER
    private static Address homeAddress(String line1, String line2, String city, String country) {
        Address a = new Address();
        a.setType = Address.Type.HOME; 
        // populate fields from arguments ...
        return a;
    }

    // ACCEPTABLE
    private static Address Address(String line1, String line2, String city, String country) {
        Address a = new Address();
        // populate fields from arguments ...
        return a;
    }

Use plural form when method returns multiple items. For getter-style methods returning stream, avoid using 'get' 
prefixes (Stream is mutable object created per call, such method is idiomatically not a getter!).

    public class User {
        ...
        Stream<Phone> phones() {...}
        List<Phone> getActivePhones() {...}
    }


#### Avoid duplicating information from class name and parameter types in the method name

Motto: Even with the compiled code, parameter types are available to the developer and therefore, are part of the name 
context. Duplicating information from them can cause all the issues mentioned in the "Naming of program elements / 
Contextual" section of this paper.  

Keep in mind that does not apply to parameter names, that are not part of the method signature.

Examples:

    public interface TokenService {
        // AVOID
        public Batch<Token> getUpdatedTokensBatchBetweenFromAndToDates(Date from, Date to)
        
        // PREFER
        public Batch<Token> findUpdatedBetween(Date from, Date to);
        public Batch<Token> findUpdatedBefore(Date date);
        public Batch<Token> findUpdatedAfter(Date date);
    }

Information available from method return value type usually should not be duplicated in method name, except for cases 
when multiple methods would end up with a signature conflict otherwise. Arrival of Stream API in JDK8 made this part 
even more messy - the need to return both list and set is relatively rare, however the need to access the same 
collection both as Stream and as List is somewhat more common. The raw number of such cases is still as low as 1-2%, 
unfortunately those are typically core classes, heavily used throughout the application.
Because of such disposition, establishing a generic rule to follow when naming all methods that return collections is an
overkill, however a conventional way to handle the disambiguation still needs to be defined.
The recommended approach is to use unprefixed names for methods that return stream (JDK8 has established this sort of 
naming convention with the methods like `java.nio.file.Files.lines()`) and use naming without any disambiguation, until 
necessary. alternative methods appear. 

    public interface User {
        Stream<Token> phones();
        List<Token> getPhones();
        Set<Token> getPhonesSet();  
    }

    public interface TokenService {
        Stream<Token> active();
        List<Token> getActiveList();
        Set<Token> getActiveSet();  
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
        User byId(String id);
        List<User> byNickname(String nickname);
        Stream<User> byRegistrationDateBefore(Date value);
        Stream<User> byNameAndSurname(String name, String surname);
    }

Note that return type is on context so it should not be duplicated in method name.

**is** - Simple boolean getter (no-args) or predicate method.     

    public class User {
        boolean isSmart() {...}
    }
    public interface UserService {
        boolean isLoginPermitted(User u);
    }

**find** - Lookup method, retrieving one or more items that match a specific condition. Compared to 'by' prefix, this 
one is used when search criterion in cases when no property exists. 
    
    public interface UserService { 
        Stream<User> findUnverified();
        List<User> findRegisteredAfter(Date value); 
        Batch<User> findUpdatedBetween(Date from, Date to);
    }

**to** - Convert to a different form.    
    
    public interface TokenService {
        String toJson(Token token);
        byte[] toByteArray(Token token);  
    }
    
**from** - Obtain an object (with the target class known from context) from a different form. Note that if information 
about the type of source object is already on context, you are good to go with this single word:
    
    public class Instant {
        public static Instant from(TemporalAccessor temporal) {...}
    }

However if source object type is not enough to understand what is happening, additional information must be communicated 
in the method name:    
    
    public interface TokenService {
        // "String" parameter type on context does not communicate that it is json and fields are base64 encoded 
        Token fromJsonBase64(String json);
    }

Note: another common prefix for this kind of conversion is `parse`. Hovewer using it as a generic prefix might turn
misleading, as not every conversion involves parsing. It is not recommended to use it, unless you explicitly want to
communicate that parsing is involved.

