# Elegant Design: Tagged Unions In C

> “Type inference is like knowing that you don’t know.” -d34d0s
> 

## Variable Data

There are many scenarios in programming where you’ll find yourself needing various **types** of data, but the processes or functionality operating on this data expects a known type. A simple example of this could be requesting a user’s age—they could enter 23 as an integer, or “23” as a string—which we would like to process as an integer. Solving this very problem is the heart of dynamically typed languages like python and javascript.

### How Can We Solve This?

In a dynamically typed language like python for instance, a solution to **dynamic typing/type inference** involves a program that runs “on top” of your code called **the interpreter**. To keep things simple, **the interpreter** is what allows you to assign just about any type you want to a variable by “scanning” your code, deducing just what type should that variable be. In C we can take a much simpler and efficient approach utilizing a powerful **data structure** called the `union`.

## Unions

The `union` **data structure** is a flexible structure, that may be comprised of multiple **fields,** of which **only one may be assigned a value at any given time**. What this implies is that, much like a typical `struct`, multiple **fields** of different **types** may exist together, allowing us to encapsulate more than one **data type** under a single **type** (preceding a `union` structure with `typedef` declares it as a **type**). Let’s take a look at a `union` just for reference:

```c
typedef union Variant {
	int i;
	char c;
} Variant;
```

Now, as i mentioned above, **a `union` may only have one of its fields assigned a value at any given time**.  This means that should there exist a function that accepts our `Variant` type as a parameter, it can then operate on either the `char` field data, or the `int` field data depending on the encapsulated logic.

## Why use them?

So far, it doesn’t really seem like the `union` **data structure** has much to offer. If anything the fact only one data field may be populated seems rather limiting, no? The example given above doesn’t seem to do the `union` much justice either since any function operating on an instance of `Variant` needs to somehow know which field to access. Why not just use a good o’l `struct`? ***Smirk.***

## Tagged Unions

**The Augmented `struct`**

Building on the ideas expressed so far, the `union` **data structure** is quite like a regular structure, except for the limiting factor of only being able to assign a single field at a time… but what if I told you that was the best part? The problem of our functions not “knowing which field to access” can be solved in two simple steps—wrap our union in a structure, and give that structure a field to indicate type (a tag). Let's visualize those steps in code to really drive it home:

```c
typedef enum Variant_Tag {
	VARIANT_NONE,
	VARIANT_INT,
	VARIANT_CHAR
} Variant_Tag;

typedef union Variant_Data {
	int i;
	char c;
} Variant_Data;

typedef struct Variant {
	Variant_Data data;
	Variant_Tag tag;
} Variant;

void print_variant(Variant v) {
    switch (v.tag) {
        case VARIANT_INT:  printf("int: %d\n", v.data.i);  break;
        case VARIANT_CHAR: printf("char: %c\n", v.data.c); break;
        case VARIANT_NONE: // fall through to default
        default: printf("Unknown type\n"); break;
    }
}
```

> The `print_variant` function demonstrates simulated dynamic typing using a `switch` `case` expression.
> 

The first step was to “wrap our union in a structure”, and this step is important as it frees us from the limitation of only being able to populate a single field. The second step was the addition of the tag field within our enclosing structure. This unique combination of a `union` and `struct` where the enclosing `struct` contains a field denoting which of the `union` fields to access is called a **tagged union**—a beautifully simple, and **type safe** method of simulating **dynamic typing.** After seeing the `print_variant` function, you can see how this is especially useful in systems like parsers, interpreters, or any system where an entity may carry different **types** of data but be processed uniformly.

### Seems *Too* Simple…

**The Features Of `union`**

Well, it’s ***almost type safe.*** You see, as the developer you could totally just ignore the tag field, accessing the union on a whim. In C++, that bird won’t fly, as such an access results in **undefined behavior**. In C however, you may get to cover some ground in a few cases thanks to a feature known as **type punning,** which interprets the raw memory of the assigned field *as if* it were the type of the accessed field—no casting, just reinterpreting bits. Sometimes useful, sometimes a footgun. With that said, even with the “guard rails” of **type punning** in place, it’s still a responsibility as the developer to make sure we understand how to work with the structures in place within our code.

In the end, a huge part of writing great software is crafting meaningful interfaces capable of not only capturing programmatic value but serving as an intermediary to more meaningful interactions. The **tagged union** does just that—in code, we have the ability to *unify* a plethora of **data types** under a single interface, with which comes almost no obscurity—leaving control in the hands of the developer even in the face of the uncertainty that is *runtime.* That’s what makes them elegant.

## Further Study:

Now equipped with the armor of invariance, you can look into more design patterns and paradigms that utilize the `union` data structure in some interesting ways:

- Self Referential/Recursive Unions
- Nested Unions (Shared Memory)

## TL;DR

- Unions are used to solve problems commonly involving type variation.
- Tagged unions are a way to strongly-type variable data.
- When used properly, in C, tagged unions can simulate type inference and dynamic typing features found in other languages.
- In C, unions have a few features that set them apart from the typical `struct` . There are two main ones you should be aware of:
    - Any instance of a `union` structure is always the size of its largest field.
        - In our example, `Variant_Data` should be 4 bytes in size as the `int` field is its largest.
    - When the unassigned field of a `union` is accessed, any other field is assigned, the compiler will return the value of the assigned field, *interpreted* the same type as the accessed field.