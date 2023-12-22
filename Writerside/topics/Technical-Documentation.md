# Technical Documentation

## FAQ

Is there Function Overloading?
* No. Maybe in the future, but given that there are only so many data types, it is not worth the effort. This means that functions must have unique names, which should make it easier for external calling of functions (since there is no name mangling).

## Considerations

Since we are compiling down to a language that will not run on a physical machine, but rather on a Minecraft instance, there are a few advantages that can be leveraged:

1. Since we are not limited by CPU architecture, we can use an arbitrary number of registers, as long as the number 
   is fixed at compile time.

## File Formats

### Source File (.ing, Ingot)

The source code in Ingot format. The preprocessed source code is also in this file format.

### Header File (.ingh, Ingot Header)

The header file in NAME format.

This is analogous to a C header file, and is used to declare function signatures and global variables.

This can also be used to declare macros, which are expanded at compile time.

This file can also be written for libraries that are not written in BlastFurnace, but rather in mcfunction. This allows the BlastFurnace compiler to link the library to the compiled code.

### Compiled Code (.hmasm, Hanmin's Minecraft ASM)

The mcfunction analogue to an assembly (.s) file. Each source code file gets compiled down into this hmasm format.

Note that unlike normal machine instructions, "jumps" in mcfunction will always return back to the caller.

This format heavily resembles a normal .mcfunction file but with a few differences:

1. Multiple functions can be present in one file. While the mcfunction format requires each function to be in a separate file, several functions can be present in this one file for representational compactness.
   * Each function had a line containing a label. The label starts with a `/` and is followed by the function name. Alternatively, a path and name can be specified, such as `/path/to/function_name`, and the function will be named `function_name` and placed in the `hmasm_file_name/path/to` folder. 
   * Functions in the current file should be called without the namespace, and the compiler will automatically add the namespace. Additionally, all functions generated will implicitly have an `hmasm_file_name/` prefix.

[//]: # (2. Functions have parameters and &#40;proper&#41; return values. While the mcfunction format does not accept input parameters and only support one output return value, this format allows functions to accept input parameters and return multiple values.)

Since this format is similar enough to the mcfunction format, datapack makers could theoretically write this format
by hand to leverage the advantages of writing multiple functions in one file.

Additionally, since this format is generic enough, other compilers could theoretically compile down to this format, and then use the Blastfurnace linker to link the compiled code together.

#### Sample hmasm file

```
/hello/world
say Hello World!

/main
function hello/world
```

This would generate the following mcfunction files in the datapack under the folder `hmasm_file_name`:

##### hello/world.mcfunction:
```
say Hello World!
```

##### main.mcfunction:
```
function namespace:hmasm_file_name/hello/world
```





## Compilation

The compilation happens in multiple stages:

