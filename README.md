[![Build Status](https://travis-ci.org/berkeley-abc/ext-hello-abc.svg?branch=master)](https://travis-ci.org/berkeley-abc/ext-hello-abc)

# Adding an extension module to ABC without modifying ABC

This repository contains an example on how to add a module to ABC without modifying ABC itself. This has several advantages:

* You can easily update to newer version of ABC.
* You can put this in a separate source control repository.
* You can easily combine multiple such projects without any additional modification to ABC.

This examples add a command `@hello`, that prints out a greeting and its arguments.

## Instructions

1. Clone the ABC repository

```bash
git clone https://github.com/berkeley-abc/abc.git
```

2. Extract this repository under `src/`

```bash
cd abc/src
git clone https://github.com/berkeley-abc/ext-hello-abc
```

3. Build ABC

```bash
make
```


4. Run the new command

```
$ ./abc
UC Berkeley, ABC 1.01 (compiled Mar 11 2018 00:55:29)
abc 01> @hello 1 2 3
Hello world!
  argv[0]: @hello
  argv[1]: 1
  argv[2]: 2
  argv[3]: 3
abc 01>
```

## Some Details

### `.gitignore`

ABC's `.gitignore` file also ignores `src/ext*`, to prevent accidentally adding your extensions to ABC. 

### Makefile

The ABC Makefile is based on the classic paper [Recursive Make Considered Harmful](http://aegis.sourceforge.net/auug97.pdf).

The Makefile has a variable named `MODULES` that contains a list of subdirectories where it looks for files named `module.make`. The `module.make` file should add the module's source files to the variable `SRC`.

The `MODULES` varialbe also contains a line
```Makefile
$(wildcard src/ext*) 
```
which instructs it to look for `module.make` files under any directory under `src/` whose name starts with `ext`.


### Adding Commands to ABC

To run your code as part of ABC, you need to register commands. To do that, we provide an way of registering functions to be called on ABC startup and termination. This is demonstrated in `init.cpp`.

1. Write an `init()` and `destroy()` functions. 

2. Create a global initializer object:

```
Abc_FrameInitializer_t frame_initializer = { init, destroy };
```

3. Register the initializer object:

```
Abc_FrameAddInitializer(&frame_initializer);
```

This is best done before `main()` is called. The simplest way to do that is in C++,  by using a constructor of a global obejct, as constructors of global objects are called before `main()`.
