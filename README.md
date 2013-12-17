Yggdrasil
=========
Dumb "tree-based" programming language

Theory
===
Everything in this programming language operates on a giant node-tree.

Variables are declared by *pushing* new nodes onto the tree.  
Variables are acessed by *grabbing* those nodes, and getting their stored values.

    <- foo;

That will *push* a new node called *foo* onto the tree.

    -> foo $ val;

That will *grab* the first node called *foo* and return its value.  
But wait a minute! *foo* has no value, so it returns *nil*! We should do this first:

    -> foo[12];

That will set *foo*'s value to be 12.  
There are some things that the "preprocessor" understands that will make your life easier, at the expense of informal coding.

    // This is the same...
    foo;
    
    // ...as this.
    -> foo $ val;

If you do not do anything with foo, then the compiler assumes that you mean to get its value.  
The preprocessor will also put -> before every node access. This is because you are getting the node from the *global tree* which is accessed by *grabbing* a value from nothing. (i.e., you try to get the value from the whitespace to the left of the arrow.)  
However, for the sake of simplicity, I will be ommitting preprocessor goodies in the code below.

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
    
    // Access their values.
    -> root -> foo $ val; // Returns 34.
    -> root -> bar $ val; // Returns 17.
    -> root -> qux $ val; // Returns 96.

However, you cannot assign a *val* to *root* because it's "value" is its children.

    // This is no good.
    -> root[33];

That will overwrite foo, bar, and qux.
