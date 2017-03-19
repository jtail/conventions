### Java formatting

Terminology Note: block-like construct refers to the body of a class, method or constructor or array initializer. 


#### Braces must be always used with loop and conditional statements  

Braces must always be used with the following statements: `if`, `else`, `for`, `do` and `while`, even when the body is 
empty or contains only a single statement.

Motto: Adding a line to existing method body should be seen as additive change by version control system and humans who
view the diff.


#### Respect JLS order of modifiers 

Class and member modifiers, when present, appear in the order recommended by the Java Language Specification:

`public protected private abstract static final transient volatile synchronized native strictfp`

Motto: the same method header should be written in the same way by different developers. Since this is a rare case where
we have explicit guidance in JLS, let's use it.  


#### Spaces and brackets/braces 

All forms of brackets (round `()`, square `[]` and braces`{}`) obey the following rules: 

##### 1. No line break before the opening bracket

Exceptions: arithmetic brackets in expressions and grouping brackets in multi-dimensional array initializers.

##### 2. Line break *must* be placed after the opening brace and *can* be placed after round and square brackets at will

Exceptions: 
Double braces are allowed in anonymous classes containing only initializer block and array initializers. 
    
##### 3. If line break is present after the opening bracket it is placed before the closing bracket as well (and vice versa)  

Note that in conjunction with 2 this always requires line break before the closing brace. 

##### 4. Line break after the closing brace if it terminates statement, method body or named class 

For example, there is no line break after the brace if it is followed by else or a comma.

Examples for 1-4

    // AVOID 
    int violation1 = 
            [1, 2, 3, 5, 7];

    if (condition()) { return violates2; }

    int violates3 = [
            1, 2, 3, 5, 7]

    int violates3 = [1, 2, 3, 5, 7
            ]

    return new MyClass() {
        @Override public void violates4() {
        }};

    // EXCEPTIONS
    int[][] x = [
        [1, 2],
        [3, 4]
    ]; 
    Map<String, String> map = new HashMap<String, String>() {{
        put("this", "is");
        put("still", "legal");
    }};
    String[][] x = {{"foo"}};

    // PREFER
    int primes = [1, 2, 3, 5, 7] 
    int primes = [
        1, 2, 3, 5, 7
    ] 
    return new MyClass() {
        @Override public void method() {
            if (condition()) {
                try {
                    something();
                } catch (ProblemException e) {
                    recover();
                }
            }
        }
    };

##### 5. (Optional) Place line break after the opening bracket if closing bracket is not on the same line. 

Example:

    // PREFER
    someLongVariableName.getSomeLongFieldValue().someLongMethodCall(
        someLongParameterVariable, someEvelLongerParameterVariable, some * extra * parameter * expression
    );
    // AVOID
    someLongVariableName.getSomeLongFieldValue().someLongMethodCall(someLongParameterVariable, 
        someEvelLongerParameterVariable, some * extra * parameter * expression);

Motto: 
It gives a clear visual difference between the process of "getting to the method" and its parameters.
When only method parameters change, it will be evident that nothing else has changed during review.
Also, when expressions are involved as parameters and some NPE is thrown, 
it is often good to know whether expressions are to blame just from looking at the stacktrace.  


#### Block indentation 4 spaces 

Each time a new block or block-like construct is opened, the indent increases by 4 spaces. When the block ends, the 
indent returns to the previous indent level. The indent level applies to both code and comments throughout the block. 

Motto: Identation code allows humans to distinguish block borders more easily


#### One statement per line 

Each statement is followed by a line-break.

Motto: Removal of statement should be seen as removal by version control systems and humans that view the code.


#### Column limit: \<choose whatever you like\> 

While it is up to a project to choose a specific value, column limit must exist.
Any line that would exceed this limit must be wrapped, except for:

- Lines where obeying the column limit is not possible (a long URL in Javadoc, or a long JSNI method reference).
- `package` and `import` statements. 
- Command lines in a comments that may be copy-pasted into a shell.


#### Line-wrapping 

Terminology Note: When code that might otherwise legally occupy a single line is divided into multiple lines, typically 
to avoid overflowing the column limit, this activity is called line-wrapping.

There is no comprehensive, deterministic formula showing exactly how to line-wrap in every situation. 
Very often there are several valid ways to line-wrap the same piece of code.

The recommended approach:

1. If it is possible to extract variable or method with a meaningfil name, this is the way to go. 

        // AVOID        
        return new GraphNode(Long.parseLong(values.get(0).trim()), values.get(1).trim(),
                Long.parseLong(values.get(2).trim()));
        // PREFER
        Long id = Long.parseLong(values.get(0).trim());
        String name = values.get(1).trim()
        Long parent = Long.parseLong(values.get(2).trim());
        return new GraphNode(id, name, parent);

Motto: 
Meaningful names of the intermediate calues help to understand the logic of calculation. 
Stacktrace will also show more specific location of error.     

**Important**: if you have trouble inventing a name that will communicate some useful information to a reader, such 
extraction might even make the code less readable due to clutter of extra characters in the variable type and name.
Especially bad when class of the extracted variable has a long name. 
Consider the following example:

        // AVOID
        SomeBuilderClassWithLongName builder = new SomeBuilderClassLongName("Something is wrong" with", parameter);
        throw new SomeProcessExecutionException(builder.build("extra parameter"));
        // PREFER
        throw new SomeProcessExecutionException(
                new SomeBuilderClassLongName("Something is wrong" with", parameter).build("extra parameter")
        );

2. If your line contains a lambda expression, place a line break before and after it
        
        return Optional.ofNullable(users.get(userId)).map(
                user -> new UserProfile(user.getFirstName(), user.getLastName()) 
        ).orElse(null)
        
Motto: In a long sequence of chained/nested calls it is often difficult to identify boundaries of a lambda expression 
at a glance.
 
3. Prefer to break at a higher syntactic level. If you have top-leven brackets of any kind, consider placing line break
after opening bracket and before the closing one.
        
        // AVOID        
        notifications.addPendingInfoMessage(client.getClientId(), new NotificationMessageBean(
                NotificationMessageType.PAPER_MAIL)).markOverridden();
        // PREFER
        notifications.addPendingInfoMessage(
            client.getClientId(), new NotificationMessageBean(NotificationMessageType.PAPER_MAIL)
        ).markOverridden();
   
Motto: When reading the code developer usually needs to go from generic to specific. Ability to quickly skip "more 
specific" blocks of code heavily depends on identation as they eye tends to "scroll down" to the next line at the same
level of identation. If brackets are around method call, there is another good reason is to place method name 
(which is usually quite long in such case) and parameters on different lines: longer names, being more specific, 
tend to be changed more frequently and such approach makes it easier to see that parameter values didn't change during 
refactoring.


#####  When a line is broken at a non-assignment operator the break comes before the symbol

This also applies to the following "operator-like" symbols:
 
- the dot separator (.) 
- the ampersand in type bounds (<T extends Foo & Bar>)
- the pipe in catch blocks (catch (FooException | BarException e))

        // AVOID 
        someLongVariableName.someMethodName(someArgumentName).
                anotherMethodName(anotherArgumentName)
        // PREFER 
        someLongVariableName.someMethodName(someArgumentName)
                .anotherMethodName(anotherArgumentName)

Motto: Operator symbol at the beginning of the line clearly indicates that this is a continuation, even if viewport is
small and the end of the line is not visible.

NOTE: When a line is broken at an assignment operator it is recommended to place the break after the symbol, but 
either way is acceptable. This also applies to the colon in an enhanced for ("foreach") statement.   

##### A method or constructor always name stays attached to the open parenthesis (() that follows it.

##### A comma (,) always stays attached to the token that precedes it.

##### Indent continuation lines at least +8 spaces 

When line-wrapping, each line after the first (each continuation line) is indented at least +8 from the original line.

When there are multiple continuation lines, indentation may be varied beyond +8 as desired. In general, two 
continuation lines use the same indentation level if and only if they begin with syntactically parallel elements.


#### Whitespace 

##### Class members are separate by a single blank line 

A single blank line appears:

1. Between consecutive members (or initializers) of a class: 
    fields, constructors, methods, nested classes, static initializers, instance initializers.
Such blank lines are used as needed to create logical groupings of fields.
Exception: A blank line between two trivial field declarations is optional. Field declaration is trivial if has no 
annotations and comments and occupies only one line.

2. Within method bodies, as needed to create logical groupings of statements.

3. Optionally before the first member or after the last member of the class (neither encouraged nor discouraged).

Multiple consecutive blank lines are permitted, but never required (or encouraged).

##### Horizontal whitespace 

Beyond where required by the language or other style rules, and apart from literals, comments and Javadoc, a single 
ASCII space also appears in the following places only.

1. Separating any reserved word, such as if, for or catch, from an open parenthesis (() that follows it on that line
2. Separating any reserved word, such as else or catch, from a closing curly brace (}) that precedes it on that line
3. Before any open curly brace ({), with two exceptions:
    - @SomeAnnotation({a, b}) (no space is used)
    - String[][] x = {{"foo"}}; (no space is required between {{, by item 8 below)
4. On both sides of any binary or ternary operator. This also applies to the following "operator-like" symbols:
    - the ampersand in a conjunctive type bound: <T extends Foo & Bar>
    - the pipe for a catch block that handles multiple exceptions: catch (FooException | BarException e)
    - the colon (:) in an enhanced for ("foreach") statement
5. After comma (,), colon (:), semicolon (;) or the closing parenthesis ()) of a cast
6. On both sides of the double slash (//) that begins an end-of-line comment. Here, multiple spaces are allowed.
7. Between the type and variable of a declaration: List<String> list

Note: This rule never requires or forbids additional space at the start of a line. Only interior space is affected.


##### Avoid using horizontal alignment 

Horizontal alignment is the practice of adding a variable number of additional spaces in your code with the goal of 
making certain tokens appear directly below certain other tokens on previous lines.

    // PREFER
    private int x; // these comments  
    private Chicken chicken; // are not aligned 
    
    // AVOID
    private int     x;       // these comments
    private Chicken chicken; // are aligned

Motto: While horizontal alignment can aid readability, it provokes serious problems for future maintenance. 
Consider a future change that needs to touch just one line. The  person making such change will face a difficult 
choice: either leave the formerly-pleasing formatting mangled or adjust whitespace on nearby lines as well. Using 
the first option immediately defeats the purpose of horizontal alignment, but the second is even worse: it corrupts 
version history information, slows down reviewers and exacerbates merge conflicts.


##### Grouping parentheses: recommended 

Optional grouping parentheses are omitted only when author and reviewer agree that there is no reasonable chance 
the code will be misinterpreted without them, nor would they have made the code easier to read.
 
Motto: It is not reasonable to assume that every reader has the entire Java operator precedence table memorized.


##### In a public enum, a line break must be placed after each comma that follows an enum constant

In a public enum, a line break must be placed after each comma that follows an enum constant.

Motto: Changes to public API should be easy to review, so adding or removing enum constant should be seen as
"pure add" or "pure delete" by version control systems and human reviewers.
Also, even if you do not have a Javadoc at the moment, it should be easy to add it and review such change.

NOTE: Private enums are regulated less strictly and can even be formatted as if it were an array 
initializer, if they have no methods, no documentation and no constructor arguments. 
    
    private enum Suit {CLUBS, HEARTS, SPADES, DIAMONDS}


#### Avoid C-style array declarations 

The square brackets form a part of the type, not the variable: `String[] args`, not `String args[]`.

Motto: Uniform look of code written by different developers. 


#### Array initializers 

Any array initializer may optionally be formatted as if it were a "block-like construct." 
For example, the following are all valid (not an exhaustive list):

    int[] values = new int[] {0, 1, 2, 3}

    int[] values = new int[] {           
        0, 1, 2, 3            
    }                       
    
    // Recommended for arrays that are likely to be changed in the future
    int[] values = new int[] {
        0,
        1,
        2,
        3,
    }

    int[] values = new int[] {             
        0, 1,               
        2, 3
    }                     


#### Switch statements 

Terminology Note: Inside the braces of a switch block are one or more statement groups. Each statement group consists of 
one or more switch labels (either case FOO: or default:), followed by one or more statements.

Example of properly formatted switch statement: 

    switch (input) {
        case 1:
        case 2:
            prepareOneOrTwo();
            // fall through
        case 3:
            handleOneTwoOrThree();
            break;
        default:
            handleLargeNumber(input);
    }


##### Indentation in switch block 

As with any other block, the contents of a switch block are indented +4.

After a switch label, a newline appears, and the indentation level is increased +4, exactly as if a block were being 
opened. The following switch label returns to the previous indentation level, as if a block had been closed.

Motto: Help reader to quickly identify borders of statement group.


##### Fall-through in switch block must be commented 

Within a switch block, each statement group either terminates abruptly (with a break, continue, return or thrown 
exception), or is marked with a comment to indicate that execution will or might continue into the next statement group. 
Any comment that communicates the idea of fall-through is sufficient (typically // fall through). This special comment 
is not required in the last statement group of the switch block.
 
Motto: Missing a break in a switch statement is a very common mistake, so explicitly communicating the intent to the 
reader saves him time spent on thinking whether you did the mistake or actually mean it. 


##### The default case is present in switch statement 

Each switch statement includes a default statement group, even if it contains no code.

Motto: Explicitly communicate to the reader, that no action is performed by default.


#### Annotations 

Annotations applying to a class, method or constructor appear immediately after the documentation block, and each 
annotation is listed on a line of its own (that is, one annotation per line). These line breaks do not constitute 
line-wrapping, so the indentation level is not increased. Example:

    @Override
    @Nullable
    public String getNameIfPresent() { ... }

Annotations applying to a field also appear immediately after the documentation block, but in this case, multiple 
annotations may be listed on the same line. 

    @Partial @Mock 
    protected DataLoader loader;

However using this approach is not recommended for use with parameterized annotations.

There are no specific rules for formatting parameter and local variable annotations.

 
#### Comments
 
##### Avoid trailing comments

    // PREFER
    // This comment is fine
    x = 3;      

    // AVOID
    x = 3;      // This comment is bad

Motto: As the code evolves, the commented line often becomes longer, eventually hitting the length limit. IDE formatters
are not able to automatically deal with this, so this will require manual effort to move the comment in front.
Another issue is that such comments tend to get out of sync with code and when comment is not placed on a separate line, 
it is impossible to use VCS blame command to quickly identify whether comment and line of code were written at the same
time or code was updated later. 


##### Comments that occupy the entire line are always indented at the same level as the surrounding code and  

    void init() {
        // Let's define a special pi value just for Indiana
        // see bill #246 of the 1897  
        pi = 4;
    }
    
Motto: When you temporarily comment out lines of code, you will put // at the beginning of the line, indicating that 
it is temporary change which is not to be committed. And it will be easy for you and reviewers to notice such lines
even if you later forget you didn't want to commit them.


##### Prefer end-of-line style (//...) for non-javadoc comments 

    // This is good inline comment
    /* This comment is not as good */

Motto: Keep multi-line `/* ... */` comments reserved for quick commenting out of large fragments of code without 
affecting the output of `git blame` on such lines. However if fragment being commented using this technic already 
contains a multiline comment it will not work properly.

NOTE: In some extremely rare cases, it might be reasonable to use multiline style to write truly long essays that are 
still subject to be maintained, but somehow you do not want them to be exposed in javadoc. In this case you usually 
already know you are doing something wrong but cannot immediately address it for some reason. Neverthless, if you got 
into this situation, think twice. Code is really not a good place to write essays. Maybe you should, for example, place 
a ticket in bugtracker and put small comment with a link and short summary instead.  
 

#### Long-valued integer literals use an uppercase L suffix

    // PREFER
    // int x = 3000000000L;
    
    // AVOID 
    // int x = 3000000000l;

Motto: Lowercase l is easily confused with the digit 1. 
