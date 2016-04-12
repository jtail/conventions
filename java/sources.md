### Java source file conventions 
 
#### Source files are encoded in UTF-8

This applies to all Java source files without exception. This also applies to most text resources, unless there is a 
specific reason not to do so. If such reason exists, it must be documented at the point where resources are processed.

Motto: Any character can appear in Java source file, but most of the characters are ASCII. UTF-8 is well-suited for 
such combination and has become a true industry standard in Java world.   


#### Avoid Non-ASCII whitespace characters

Aside from the line terminator sequence, the ASCII horizontal space character (0x20) is the only whitespace character 
that appears anywhere in a source file. This implies that all other whitespace characters in string and character 
literals are escaped, including tabs.

Motto: Human reading the code will not be able to distinguish one whitespace character from another.  


#### Favor special escape sequences over octal or Unicode escape  

For any character that has a special escape sequence (\b, \t, \n, \f, \r, \", \' and \\\\) that sequence must be used 
rather than the corresponding Unicode (e.g. \u000a) escape.

Motto: Most developers don't remember numeric 


#### Non-ASCII characters are represented directly or using Unicode escape  

For the remaining non-ASCII characters, either the actual Unicode character (e.g. ∞) or the equivalent Unicode escape 
(e.g. \u221e) is used, depending only on which makes the code easier to read and understand. Add an explanatory comment 
if it will be very helpful.


#### The package statement is not line-wrapped. 

The column limit does not apply to package statements.


#### Import statements are not line-wrapped. 

The column limit does not apply to import statements.


#### Avoid static imports in production code 

Using static imports should be limited to tests where they provide a noticeable benefit to expressiveness an 
readability. They should never appear in production code. 

Motto: 

1. Readability. When reading class outside of IDE during code review, it is impossible to justify from which method
is invoked. Consider seeing the following example during the code review:

    writeResponse(of(x, y)

At the time I have encountered this, I was pretty sure it is Stream.of() method being called. However it proved to be 
ImmutableMap.of() later on.

2. Static method imports provoke tricky merging issues. The worst part of it about it is that it can happen even if 
commits are not on the same file. Consider the following example:

In branch X, someone has added the following code to the class B:

    import static org.apache.commons.lang3.StringUtils.join;
    public class B extends A {
        public void doSomething() {
            String join = join("a", "b");
        }
    }

In branch Y, someone has added the following code to the superclass A:

    protected String join(String x, String y){
        return x + "," + y;
    }

Once you merge it, the code even compiles, however do you know which method is called now from class B?


#### Avoid wildcard imports

No wildcard imports should appear anywhere in code.

Motto: The only benefit from wildcard imports is conciseness, however noone reads imports until they need it. But when 
such need arises it is usually some troubleshooting session, when exact information on what is being used is and it is
hidden by wildcard. There is also a bunch of maintainablility problems known to be caused by wildcard imports:

- Significantly increased chances that code will not compile after a trivial merge. And these chances grow 
  exponentially increase as the team grows.
- When multiple wildcard imports are present, it is not possible to understand which class is invoked without 
  looking at imported classes (or having IDE doing that for you, which is not the case for web-based code reviews)
- Changing between wildcard imports and normal imports easily cause merge conflicts.   


#### Have a strict import order enforced 

For any given set of imports only one way of ordering them should be allowed. It is usually a good idea to use static 
analysis tools like checkstyle to enforce it.  

Motto: When imports are added and removed in different branches, enforcing a strict order greatly decreases chances of 
getting add/add conflicts and also aids in resolving them quickly. 

A sample of ordering rules:

    Import statements are divided into the following groups, in this order:    
    - All static imports in a single group
    - javax imports
    - java imports
    - All other imports
    Groups are separated by a single blank line:
    Within a group there are no blank lines, and the imported names appear in ASCII sort order.
 
 
#### Avoid unused imports 

Motto: Unused imports interfere with full-text search. They are also bad for merging. 
Consider the following sample scenario:

  - In master repo an import was left unused. 
  - In branch Y unused import is removed. 
  - In branch Z the class specified in the import is used again, so it is not unused anymore.
  - Branches Y and Z are merged. There are no conflict, however the code will not compile. 

 
#### Exactly one top-level class must be declared per file and class name must match file name 

Motto: Ensures no class duplication ever occurs. Make it easy to find any class, even without IDE. 
This is especially useful, if you are dealing with remote deployment and you really want a quick 
`unzip -l | grep ClassName` to work rather than trying to recall how to combine `unzip` and `find`.


#### Avoid static initializers in named classes  

Use constructor delegation and static method calls from constructors instead.
If there some code that should be shared by all constructors, it is more expressive to have them explicitly call
base constructor or static method. Some instrumentation tools will get crazy on instance initializers too.


#### Class member order 
 
Members in class must be arranged in the following order:

1. Static final fields 
2. Other static fields (really, avoid these)
3. Static initializer (static {...}) - avoid these too 
4. Instance fields
5. Constructors and static constructor-like methods that return instance of this class 
6. Other methods

Within each section some logical order must be present. There is no single correct recipe, however maintainer should be
able to document it if asked. "Chronological by date added" is not a logical ordering, and especially a bad one.
A good rule of thumb is to group public, externally usable members on top, followed by the rest of them.
 
Motto: The ordering of the members of a class can have a great effect on learnability. It also helps to avoid add/add 
conflicts as different methods will are added into different parts of the class. If new methods are just habitually 
added to the end of the class, merge conflicts are guaranteed when additions are done concurrently in different 
branches.

**Sample method order** 

    1. If method A calls method B then A appears *above* B (except for cross-method recursion)
    2. When there are multiple constructors, or multiple methods with the same name, these appear sequentially.
    3. Public instance members that form extrernally usable API are grouped together below constructors

Such order allows reader to go "from generic to specific" when reading class from top to bottom and ensures typical
refactoring operations such as "extract method" or "extract parameter - delegate via overloading method" have a clearly 
defined place where new method is placed.