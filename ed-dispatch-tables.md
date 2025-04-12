# Elegant Design: Dispatch Tables In C

> “Wouldn’t they be ***physical tables*** then?” -d34d0s

## Function Pointers

In C, **function pointers allow you to craft dynamic function references**, akin to how you’d traditionally point to some data. However, unlike pointers to data which is simply an address in memory for that data, **function pointers point specifically to the functions entry point in memory.**

In C the syntax for function pointers can sometimes look a little ***funky*** to say the least (especially considering you don’t have to name function parameters). Lets take a look at a simple function pointer declaration:

```c
int (*add_fptr)(int num1, int num2);
```

Function pointers can be preceded with the `typedef` keyword just the same as `struct`s, `union`s, and `enum`s. Doing so will allow you to create variables whose type is “of that function”. 

```c
typedef int (*add_fptr)(int num1, int num2);
...
int main() {
	add_fptr add = NULL;    // now we can store a function (reference) in a variable!
	return 0;
}
```

🔹 **Pro Tip**: Always initialize function pointers—even if you're not using them yet, set them to `NULL`.

Another cool feature of function pointers is that they can be passed to and from functions, they are still pointers after all. You could imagine how this would seem like a god-send to a new C programmer, as when I discovered them that’s exactly what it felt like!

## How Do They Work?

Function pointers in C work by **matching function signatures.** What i mean is when you declare a function pointer, the signature of this pointer must match the signature of a function within the same translation unit. Lets look at an example to understand this:

```c
#include <stdio.h>

int add_func(int num1, int num2) {
	return num1 + num2;
}

int main() {
	int (*add_ptr)(int num1, int num2) = add_func;
	printf("5 + 8 = %d\n", add_ptr(5, 8));   // "5 + 8 = 13"
	return 0;
}
```

In the above example, we implement a simple function that adds together two `int` values and returns the result. In the body of our `main` function, we declare a function pointer whose **signature** matches that of our function `add_func`. **The signature of a function consists of its return type, parameter types, and parameter count.** Calling a function this way introduces no extra overhead as stated earlier, function pointers point to the entry point in memory of the function it matches.

## Why use them?

Function pointers unlock several key design strategies:

- **Callbacks**: Pass a function into another function (e.g. `qsort` with a custom comparator).
- **Polymorphism**: Simulate object-oriented interfaces.
- **Runtime configuration**: Switch implementations at runtime based on environment or user input.

This idea—**one name, many faces**—is the cornerstone of scalable, clean C code. For example, imagine defining `draw_shape2D` and `fill_shape2D`, but the actual implementation changes based on the graphics backend selected at runtime (OpenGL, Vulkan, etc.).

## To call… or to call the other one. 
#### Runtime Configuration

Languages like C++ utilize a specific type of structure which relies heavily on function pointers called a **virtual-table** or **vtable** for short. These are essentially arrays of function pointers. In C++, for every class that has virtual methods, exists a table of function pointers, the virtual table. A pointer to this virtual table is stored as a data member in the class to facilitate the lookup of these virtual methods.

In C you can achieve a similar behavior through **dispatch tables,** or **manual virtual tables.** A **dispatch table** can be as simple as a structure comprised of function pointers:

```c
typedef struct Allocator {
	void* (*alloc)(u64 size, u64 align);
	void (*dealloc)(void* ptr);
} Allocator;
```

…or an array of function pointers, letting you “branch” via index instead of conditionals—fast and clean:

```c
int (*op1)(void* data);
int (*op2)(void* data);
int (*op3)(void* data);

int main(void) {
	int (*ops[3])(void*) = { op1, op2, op3 };
	int result2 = ops[1](NULL);
	return 0;
}
```

## In the wild

#### Practical Examples

Runtime configuration through **dispatch tables**, or a similar mechanism is present in many things from libraries, to programming languages. C++ classes store a pointer to a table of function pointers, the **virtual table.** The Lua interpreter makes use of virtual tables, as the language supports inheritance. The Linux Kernel makes use of **dispatch tables** too called in its **syscall dispatcher**. Now that we’re equipped with some more knowledge lets take a look at a simple example of how you could utilize a dispatch table for runtime configuration:

```c
typedef enum Graphics_Backend {
	NO_BACKEND,
	GL_BACKEND,
	DX_BACKEND,
	VK_BACKEND
} Graphics_Backend;

typedef struct Graphics_Api {
	void (*draw_shape2D)(void* data);
	void (*fill_shape2D)(void* data);
} Graphics_Api;

void _gl_draw_shape2D(void* data) {
	// ... gl specific code here
}

void _gl_fill_shape2D(void* data) {
	// ... gl specific code here
}

// ... other backend specific implementations here

void init_graphics_api(Graphics_Backend backend, Graphics_Api* api) {
	if (!api) return;    // remember to check for errors! (even in example code)	
	switch (backend) {
		case GL_BACKEND: {
			api->draw_shape2D = _gl_draw_shape2D;
			api->fill_shape2D = _gl_fill_shape2D;
		} break;
		// ... other cases here
		case NO_BACKEND:    // fall-through to default
		default: break;
	}
}
```

> An example showcasing the flexibility of runtime configuration using **dispatch tables**.
> 

Instead of the common, poor alternative (scattering backend-specific code everywhere), this approach cleanly bundles logic into interchangeable pieces. The interface never changes—just the implementation. And since dispatch tables often resolve to a **single memory read** (especially if already cached), they're efficient. There's no deep branching, no unnecessary complexity. ***Chef’s kiss***.

In the end, C doesn’t give you classes, inheritance, or virtual methods—but it does give you **pointers**. If you’re clever with them, you can build anything because elegance isn’t complexity—it’s simplicity. **Simplicity scales**. Dispatch tables aren’t a workaround either—they’re a design choice that give your code flexibility, and extensibility without sacrificing clarity. That’s what makes them elegant.

## Further Study

Armed with the understanding of dispatch tables you can start to look deeper into more complex implementations and design patterns which rely on this simple yet extremely flexible data structure. 

- The **Linux kernel syscall dispatcher**
- The **Strategy Pattern** (a behavioral design pattern using interchangeable algorithms)
- Bytecode interpreters (e.g. Lua, CPython)

### TL;DR

- **Function pointers** let you point to and call functions dynamically.
- **Dispatch tables** are structs or arrays of function pointers that enable flexible runtime behavior.
- This mimics **polymorphism** and **runtime configuration**, common in OOP and system-level code alike.
- Used properly, they make your code extensible, decreasing rigidity, lending itself to *iterative development*.
