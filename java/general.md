<!-- TODO Exceptions: recoverable and unrecoverable -->

<!-- TODO Avoid useless abstractions -->

<!-- TODO Use of interfaces only when required -->

<!-- TODO Keep decisions separate from imperative logic -->

<!-- TODO Use immutable objects when possible -->

<!-- TODO Avoid premature optimization -->

<!-- TODO Avoid public fields -->

<!-- TODO Avoid misleading -->


### General programming Practices 

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
