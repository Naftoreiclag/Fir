Oak
===
Dumb "tree-based" programming language. Name subject to change.

Theory
---
Everything in this programming language operates on a giant node-tree.

Variables are declared by *pushing* new nodes onto the tree.  
Variables are acessed by *grabbing* those nodes, and getting their stored values.

    // Declare foo.
    <- foo;

That will *push* a new node called *foo* onto the tree.

    // Get foo's value.
    -> foo -> $val;

That will *grab* the first node called *foo* and return its value.  
But wait a minute! *foo* has no value, so it returns *nil*! We should do this first:

    // Construct foo.
    -> foo[12];

That will set *foo*'s value to be 12.

Making your own trees
---

You can also make your own trees. Just push a new node onto another node!

    // Make a new node.
    <- root;
    
    // Attach some new nodes to this root.
    root <- foo;
    foo[34];
    root <- bar;
    bar[17];
    root <- qux;
    qux[96];

You can also do this:

    // Make a new node.
    <- root;
    
    // Construct
    -> root
    [
        <- foo[34];
        <- bar[17];
        <- qux[96];
    ];

To access the nodes (and their values) in *root*, do this:
    
    // Access their values.
    -> root -> foo -> $val; // Returns 34.
    -> root -> bar -> $val; // Returns 17.
    -> root -> qux -> $val; // Returns 96.

However, you cannot assign a *val* to *root* because it's "value" is its children.

    // This is no good.
    -> root[33];

That will overwrite foo, bar, and qux. In addition, you will no longer be able to reference them or their values, so it'll cause memory leaks!

Deletion / cleaning up
---

To delete something, do this:

    // Push foo into the $trash.
    -> $trash <- foo;

First we *grab* $trash from the global root, then *push* foo into it. That will immediately delete it and any value it has. You can't get it back out of the trash, though! That would be gross.

    // Eww! No way!
    -> $trash -> foo -> $val;

Additionally, all nodes have access to $trash. You don't need to get it from the global root.

"Preprocessor" goodies
---

There are also some shorthand ways of writing this code to make your life easier.

    // This is can be shortened...
    -> foo -> $val;
    
    // ...into this!
    foo;

Every formal declaration and access in this language either begins with with a left- or right- arrow. If you do not specify one, then the compiler assumes you mean a right-arrow. (->)  
Additionally, if you *grab* a node, but do not *push* anything into it, then the compiler assumes you mean to get its value. (it will append "-> $val" to the end of the statement).
    
    // And this can be shortened...
    <- foo;
    -> foo[17];
    
    // ...into this...
    <- foo;
    foo[17];
    
    // ...or this!
    <- foo[17];

Pretty simple; you can combine the declaration and construction statements together into one line.

One more trick: You can also say "within" in a way similar to C++'s "using namespace foo".
It tells the compiler to append whatever directory before every node access. (instead of just plain ->.)

    // Make some kinda root.
    <- root.

    // Push a new table.
    -> root <- foo[
        <- a[1];
        <- b[2];
        <- c[3];
    ]
    
    // Normally, you would have to say:
    root -> foo -> a;
    root -> foo -> b;
    root -> foo -> c;
    
    // But you can shorten it using "within."
    within root -> foo;
    
    // Then do this instead:
    a;
    b;
    c;
    
    // To get out of within, just use this:
    within;

Functions
---

Using the method explained eariler on initializing your own trees, you can also make trees that act as functions.

    // Delcare a new function.
    <- myFunction;
    
    // Construct it to return a + b.
    -> myFunction
    [
        (-> a -> $val) + (-> b -> $val);
    ];
    
    // Wait, are you sure?

It's just like the tree made earlier, but it is constructed with a statement, rather than a node or a value.   However, it is ambigious whether you want the static value of a + b, or if you want the statement to calculate a + b every time "myFunction -> $val" is called.  
To remove this ambiguousness, we need to use curly braces instead.

    // Delcare a new function.
    <- myFunction;
    
    // Declare some variables that it will be handling
    <- a;
    <- b;
    
    // Construct it to return a + b;
    -> myFunction
    {
        return (-> $par -> a -> $val) + (-> $par -> b -> $val);
    };

A little ugly, remember what I said about the compiler's interpretations? You can shorten it to just be:

    // Delcare a new function.
    <- myFunction
    {
        within $par;
        
        return a + b;
    };

Real neato. To use it, just *push* "a" and "b" nodes, then get the value of myFunction!

    // Example function use:
    a[5];
    b[7];
    
    // This will return 12:
    myFunction;

Objects
---

What we did in the "functions" section is pretty much an object on its own, but stored in the global root. All we need to do is move everything to be within an object node.

    // Declare and construct a new object.
    <- rectangle
    [
        // Make some vars.
        <- width;
        <- height;
        
        // The function:
        <- surfaceArea
        {
            within $par;
            
            return width * height;
        }
    ]

It's just like the sample adding function we made above. Except it's in a node. This how you can utilize it:

    // Set the rectangle's properties.
    rectangle -> width[10];
    rectangle -> height[8];
    
    // Get the surface area.
    rectangle -> surfaceArea(); // returns 80.

However, our object is pointless if we can't instiantate (make more of) it somehow. We can clone them by declaring a new node with the object as its constructor.

    // Let's make a new rectangle.
    <- paper[rectangle];
    <- screen[rectangle];
    <- carpet[rectangle];
    
    // Set some cool values.
    
    paper -> width[8.5];
    paper -> height[11];
    
    screen -> width[20];
    screen -> height[15];
    
    carpet -> width[36];
    carpet -> height[24];
    
    // Make some cool calculations
    paper -> surfaceArea(); // 93.5
    screen -> surfaceArea(); // 300
    carpet -> surfaceArea(); // 864

Polymorphism (object inheritence)
---

Polymorphism is way cool, bro! Let's make some diamonds! (Without needing to mine for them.)

    // Let's make a new kind of rectangle. We'll make it a diamond. (paralellogram)
    // Diamond shapes should have some special properties, like their acute angle.
    <- diamond
    [
        // Import everything from rectangle.
        $par -> rectangle;
        
        // But we'll also have "angle."
        <- angle;
    ]

Let's make some instances of diamonds.

    // Like a diamond in the sky.
    <- star[diamond];
    <- kite[diamond];
    
    // Values:
    star -> width[999999999];
    star -> height[999999999];
    star -> angle[89];
    kite -> width[30];
    kite -> height[30];
    kite -> angle[45];

Neat-o.
