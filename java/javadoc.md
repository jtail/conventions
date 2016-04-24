### Javadoc 

#### Javadoc General form 

The basic formatting of Javadoc blocks is as seen in this example:

    /**
     * Multiple lines of Javadoc text are written here,
     * wrapped normally...
     */
    public int method(String p1) { ... }

... or in this single-line example:

    /** An especially short bit of Javadoc. */

The basic form is always acceptable. The single-line form may be substituted when there are no at-clauses present, and 
the entirety of the Javadoc block (including comment markers) can fit on a single line.


##### Paragraphs 

One blank line—that is, a line containing only the aligned leading asterisk (*)—appears between paragraphs, and before 
the group of "at-clauses" if present. Each paragraph but the first has <p> immediately before the first word, with no 
space after.


##### At-clauses 

Any of the standard "at-clauses" that are used appear in the order `@param`, `@return`, `@throws`, `@deprecated`, and 
these four types never appear with an empty description. 


##### The summary fragment 

The Javadoc for each class and member begins with a brief summary fragment. 

This is a fragment—a noun phrase or verb phrase, not a complete sentence. It does not begin with A {@code Foo} is a..., 
or This method returns..., nor does it form a complete imperative sentence like Save the record.. However, the fragment 
is capitalized and punctuated as if it were a complete sentence.

Tip: A common mistake is to write simple Javadoc in the form `/** @return the customer ID */`. 
This is incorrect, and should be changed to `/** Returns the customer ID. */`.

Motto: This fragment it is the only part of the text that appears in certain contexts such as class and method indexes.


#### Prefer Javadoc over implementation comments

Whenever an implementation comment would be used to define the overall purpose or behavior of a class, method or field, 
that comment is written as Javadoc instead. If you need implementation comment to describe a block of code, consider
extracting it into a method and writing Javadoc. It's more uniform, and more tool-friendly.


#### Javadoc is mandatory for public API's 

At the minimum, Javadoc is present for every public class, and every public or protected member of such a class, with a 
few exceptions noted below. Other classes and members have Javadoc as needed.  

#### Avoid trivial Javadoc 

Javadoc is optional for "simple, obvious" methods like `getFoo`, in cases where there really and truly is nothing else 
worthwhile to say but "Returns the foo". Quite often there is nothing to say in the javadoc of `UserService` either.

Important: it is not appropriate to cite this exception to justify omitting relevant information that a typical reader 
might need to know. For example, for a method named getCanonicalName, don't omit its documentation (with the rationale 
that it would say only /** Returns the canonical name. */) if a typical reader may have no idea what the term 
"canonical name" means!

#### Avoid duplicating sizable Javadoc fragments, especially between different classes 
 
It is explicitly forbidden to duplicate javadocs from superclass. With good abstraction structure methods are typically 
called from the superclass or interface where they appear first, so the reader will simply not see what you written in 
superclass.

Motto: In many cases, it is not possible to completely eliminate duplication of javadoc without dramatically sacrificing
readability. However maintaining duplicate javadoc across multiple class files is a nightmare.

 
