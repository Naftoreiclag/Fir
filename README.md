Yggdrasil
===
Dumb "tree-based" programming language. Name subject to change.

Theory
===
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

To delete something, do this:

    // Push foo into the _trash.
    -> $trash <- foo;

First we *grab* $trash from the global tree, then *push* foo into it. That will immediately delete it and any value it has. You can't get it back out of the trash, though! That would be gross.

    // Eww! No way!
    -> $trash -> foo -> $val;

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
    
    // Construct it to return a + b;
    -> myFunction
    {
        (-> a -> $val) + (-> b -> $val);
    };

A little ugly, remember what I said about the compiler's interpretations? You can shorten it to just be:

    // Delcare a new function.
    <- myFunction
    {
        a + b;
    };

Real neato. To use it, just *push* "a" and "b" nodes, then get the value of myFunction!

    // Example function use:
    myFunction <- a[5];
    myFunction <- b[7];
    
    // This will return 12:
    myFunction;

Here's an object. I'll explain it later.

    <- TwoDeeVector
    [
        <- x;
        <- y;
        <- z;
        
        <- getSum
        {
            ($par -> x) + ($par -> y) + ($par -> z);
        };
    ];

a

    <- TwoDeeVector
    [
        <- x;
        <- y;
        <- z;
        
        <- getSum
        {
            @ $par;
            
            x + y + z;
        };
    ];
