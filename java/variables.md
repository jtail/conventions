### Variables

This section covers all variable-like language constructs: 
fields, method parameters, local variables and lambda expression parameters.

#### One variable per line declaration 

Every variable declaration (field or local) declares only one variable.

    // AVOID
    private int width, height;
     // PREFER
    private int width; 
    private int height; 

Motto: When a variable is added or removed, such change should be easily recognized as pure addition or deletion
by version control systems and human reviewers.

#### Avoid declaring variables without initializing them 

Local variables are not habitually declared at the start of their containing block or block-like construct. 
Instead, local variables are declared at the point where they are assigned. 
    
    // AVOID
    int x;
    prepareCanvas();
    x = findRandomFreePoint(0, width);
    
    // PREFER
    prepareCanvas();
    int x = findRandomFreePoint(0, width);

    // There are a few cases, when declaration with initialization is not possible. 
    // In such case, declaration and assignment should be placed as close as possible. Example:    
    int value;
    // avoid placing any code here
    do {
        value = inputStream.read();
    } while (value > 0 && value != LINE_MARKER);

Motto: Code is more concise, variable scope is minimized and variable type is visible immediately, even if the method 
name does not clearly indicate it.
  
Note: Some people claim that "predeclaring" variables that are only inside the loop in front of it is some kind of 
performance optimization. They are wrong, Java compiler is smart enought to do this kind of optimization automatically. 
You can easily verify this if you check the resulting bytecode.  
 
#### Prefer effectively final local variables  

Avoid reusing local variables. If such reusing is inevitable, make sure to minimize the affected scope by isolating it 
in a method. 

    // AVOID
    int damage = weapon.getDamage();
    Gem gem = weapon.getGem();
    if (gem != null) {
        damage = calcDamage(damage, gem); 
    }

    // PREFER
    int damageBase = weapon.getDamage();
    int damage = Optional.ofNullable(weapon.getGem()).map(
        gem -> calcDamage(damageBase, gem)
    ).orElse(damageBase); 

An obvious exception to this rule is variable modified in the loop. 

Motto:

1. Automated refactoring to extract a method does not result in nice method signatures if variable is changed inside the 
fragment being extracted, but defined outside of it. Manual efforts to resolve this are prone to introducing subtle 
mistakes. 
2. It is difficult to follow the logic of the program when multiple variables have their value conditionally changed as 
the reader has to iterate over all possible combinations       
3. Reusing variable often indicates a failure to name new values properly.
4. When rearranging lines during refactoring it is easy to alter the result of method execution without noticing it.

#### Avoid explicitly declaring variables and method parameters final

Motto: If you are consistently practicing "Prefer effectively final local variables" section listed above, then all of 
your variables will be effectively final and can be declared as such. Therefore, to be consistent you have 2 options:
declare all variables final or completely avoid doing so. Second option is more concise while choosing the first one 
brings no benefits as discovering a mutable variable 'rings the bell' that something is wrong already. Static analysis 
tools can be configured to catch such situations at compile time. Note that JDK 8+ compiler identifies effectively final
variables and treats them as such, so there will be no difference in compiled code.