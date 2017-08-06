### General programming Practices 

#### @Deprecated elements must be documented to direct reader to current api

Deprecated program elements must be documemented using `@deprecated` javadoc tag that explains what should be used 
instead and why the element is deprecated. Example:

    /** 
     * @deprecated use {@link #getRoles()} instead. For users with multiple roles, this method will return the 
     * 'most important' role, however new roles added in the future are not guaranteed to be comparable by importance.
     */
    @Deprecated
    Role getRole(); 

Motto: If you don't do it, good developers will lose some time while trying to find the alternative you meant to be 
used, while bad delevopers might choose to use the deprecated version. Either option is bad.

If it is not possible to use a direct reader to a direct alternative, a really detailed explanation must be written.
See how `java.lang.Thread.stop()` method is documented for a good example.

Note: It is recommended, but not required to use `@Deprecated` annotation in addition to javadoc tag.          
          

#### @Override must be used on non-deprecated methods

A method is marked with the @Override annotation whenever it is legal. This includes a class method overriding a 
superclass method, a class method implementing an interface method, and an interface method respecifying a 
superinterface method.

Motto: if a method was placed in the class with the intent of overriding method from superclass, removing the 
declaration from superclass is highly likely to trigger the need to change the override  


#### @Override on deprecated methods indicates implementation is not safe to remove

When the parent method is @Deprecated, you should not normally have to implement it at all. But things happen, so if you 
are facing this need, a careful choice is required:
- If after removing it from parent the implementation *is guaranteed* remain fully functional, do not put @Override.
- If removing the method from parent there is possible impact on the functionality of subclass class, .

Note: This implies the following deprecation cycle for inherited methods:

1. Implement alternative flow in superclass in a way that allows to rewrite subclasses one by one. A great way to do so
is to provide default implementation in superclass that will assume new flow is used. Put @Deprecated on method 
declaration in superclass.
2. Review subclasses overriding the method, updating them to use the new flow and replacing @Override with @Deprecated 
(or remove the method implementation completely, if you provided a default).
3. Once all subclasses are processed (e.g. have method either removed or deprecated), remove the method from superclass.
4. Clean up the remaining deprecated implementations.
  

#### Avoid ignoring exceptions 

It is very rarely correct to do nothing in response to a caught exception in production code.  (Typical responses are 
to log it, or if it is considered "impossible", rethrow it as a  RuntimeException or AssertionError.)

When it truly is appropriate to take no action whatsoever in a catch block, the reason this is justified is explained in 
a comment.

    try {
        int i = Integer.parseInt(response);
        return handleNumericResponse(i);
    } catch (NumberFormatException ok) {
        // it's not numeric; that's fine, just continue
    }
    return handleTextResponse(response);

Note that is you are using Java 8, you should still try to avoid it, using Optional: 

    return tryParseInt(response).map(this::handleNumericResponse).orElseGet(
        () -> handleTextResponse(response)
    )
    ...
    private Optional<Integer> tryParseInt() {
        try {
            return Optional.of(Integer.parseInt(response));
        } catch (NumberFormatException ok) {
            return Optional.empty();
        }
    }

Exception: In tests, a caught exception may be ignored without comment if it is named expected. The following is a very 
common idiom for ensuring that the method under test does throw an exception of the expected type, so a comment is 
unnecessary here.

    try {
      stack.pop();
      Assert.fail();
    } catch (NoSuchElementException expected) {
    }

Note that if you are testing for a single exception and don't need to run assertions on the exception itself, it is 
often more appropriate to use annotation-level declaration: `@Test(expected = NoSuchElementException.class)` 
which is both more concise and readable. However the situations when you want to test for multiple exceptions as part 
of the same scenario exist as well as you might want to ensure that given exception does not happen earlier in test. 


#### Static members are qualified with the name of that class  

When static member must be quialified (normally when it is referenced outside current class hierarchy), it is qualified 
with that class's name, not with a reference or expression of that class's type. 

    Foo foo = ...;
    // PREFER
    Foo.aStaticMethod(); 
    // AVOID
    foo.aStaticMethod(); // bad
    somethingThatReturnsFoo().aStaticMethod(); // very bad

Motto: When reading code outside of IDE, it is difficult to distinguish static and instance members, however this
information is often very important, especially when hunting for synchronization issues.


#### Prefer positive checks

When using if-else or other code branching constructs with 2 or more alternative branches, prefer logically positive 
condition checks. Note that constructs such as `!= null` and `!list.isEmpty()` qualify as 'logically positive' in most 
cases. 
  
    // PREFER
    if (zombie.hasBrains()) {
        // Do something
    } else {
        // Do something else
    }
    // AVOID
    if (!zombie.hasBrains()) {
        // Do something else
    } else {
        // Do something
    }
    
Motto: Humans have difficulty handling multiple negations when reading.

#### Use checked exceptions for recoverable errors and runtime exceptions for unrecoverable errors 

Use checked expections represent invalid conditions in areas outside the immediate control of the program, such as 
invalid user input, network outage or missing file. Such errors *usually* can be handled by caller in a meaningful way.
A good example is IOException: a caller can retry later or access a different resource, reacting to it.

Use runtime exceptions to represent conditions that reflect errors in your program's logic and cannot be reasonably 
recovered from at runtime. For example, most of the time caller has no meaningful way to react to 
IndexOutOfBoundsException - the programmer failed to calculate the index he is accessing properly or at least check the
incoming value in case caller has no knowledge about the array.

Motto:
Since checked exceptions trigger a compilation error if they are not caught or declared, ensures that programmer using 
the API is aware about possible alternative outcomes and motivates to handle them as early as possible.
Runtime exceptions are typically caught by top-level classes, such as controller or executor with a sole purpose of
logging them.
 
Warning: JDK should not be used as a source of inspiration when deciding on exception type. It is full of both good and
bad examples in this regard, partly because developers had to make some compromises, partly because of historic design
choices that can't be changed without breaking backward compatibility.


#### Avoid catching generic exception classes without rethrowing

Catching and not rethrowing Throwable, Exception and RuntimeException is usually appropriate only when you have nowhere 
to rethrow (e.g. in classes that reside on top of the call stack, such as controller or Runnable executed in a separate 
thread. And it is almost never appropriate to have business-level logic dependent on such generic catch clauses. 

Motto: Many runtime exceptions, such as IllegalArgumentException or IllegalStateException are 'unexpected by 
definition', meaning they can happen pretty much anywhere, anytime. 
Putting business or execution flow control logic in generic catches might result in triggering it under circumstances 
not anticipated by the author.  


#### Use interfaces only when required

Avoid creating interface, when there is no objective reason to split the implementation.
Typical valid reasons:
1. Multiple implementations exist immediately, at least in test code. 
2. Different scope is required for interface and implementation (api is published separately, implementation is 
available only at runtime)  

Motto: Maintainability - interface is just another class to change and to review. With modern IDEs, splitting a class 
to interface and implementation is trivial operation that requires to update only code where it was instantiated 
directly and in a well-designed application such places would be only a few, and you will only have to do it once.
On the other hand, every time you update the class API, including any additive changes, you will have to do double 
changes, updating both the class and interface. While this action is quite cheap and straightforward in implementation,
it still complicates code reviews due to increased number of files.  

Note: This rule should be explicitly treated as 'hard and fast'. If you create a class that you expect to have 
alternative implementations, but none of them are present at the time of pull request creation, interface still should 
not be created. By the time somebody actually writes and tests it, he is quite likely to discover a different API was
required to create a functional alternative.

Historical Note: Creating interfaces for everything was in fact useful back in 2005, when Spring was configured in XMLs 
not all IDE's even had 'extract interface' automated refactoring. 
If you were practicing TDD at that time, it was safe to assume that at some point the need to manually create mock 
implementation for almost every class. So it was pretty practical to create at least an interface to ensure that 
someone who would want to merely mock your class in his test will not have to refactor all code that uses it 
(including Spring configs). However the Java world has changed in a number of ways: 
1. Reflection-based test support frameworks that eliminate the need for manual mocks, such as Mockito and later 
Spock have established themselves. 
2. We got powerful IDE's that allow to do standard refactorings automatically.
3. CDI frameworks, got annotation-based configurations, that are way easier to maintain.   
4. Last, but not the least, git has arrived and allows us to isolate such changes in a separate commit.
The combination of these factors made extracting interface as simple and reliable action, that takes less than a minute
in most cases. Compare this to extra time reviewer spends reading changes to extra interface class on review.


#### Prefer LocalDate and Instant to store and handle dates and times, avoid java.util.Date and java.util.Calendar

When you need to store the precise moment on the timeline when something has happened, prefer UTC (java.time.Instant).
When date has no timezone by definition (such as 'date of issue' as it appears printed in passport), prefer
java.time.LocalDate. 
Avoid any situations in-between. If you need to expose times in different timezones, do the conversion immediately 
before sending the data.

Motto:
The conceptual problem with other representations is that they either requre a timezone context to be explicitly present 
to perform operations (to compare 2 timestamps without timezone, you need to know which timezone it is) or explicit 
business rules (consider what happens when you need to convert "date with timezone" to a different timezone)

Note: 
The situation when external date input comes with timezone is often used as an argument to support storing timestamp 
with timezone. However if timezone is required to be applied somewhere else than converting a specific timestamp, it 
has, strictly speaking, a wider scope than timestamp and should be stored on different record to ensure normalization.
Even if, for whatever reason, you choose to denormalize, ensuring you can switch to normalized representation in the 
future is a must.

Exceptions:
Sometimes, timezone-aware reporting or similar functionality that operates on large volumes of data has to take 
advantage of DB-specific timezone features. However, in such situations at least reading such values back to Java 
should be avoided.