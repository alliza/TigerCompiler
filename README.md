# TigerCompiler
A compiler for Tiger Language to MIPS implemented in Java.



## Info.
TigerCompiler is implemented in *Java*, with *Eclipse* IDE.

The main access of the TigerCompiler project is the ``Main.main()`` method.

The input source programs are text files as those ``.tig`` files in folder ``./Testcases``.

The output MIPS programs are saved in the same folder with the same names, except that the filename extension is ``.s``.

[*QtSpim*](http://spimsimulator.sourceforge.net/ "Official Site for QtSpim") can be used as the MIPS simulator on multiple operating systems.


## Example
Sample Input Tiger source program, which solves the 8-queens problem.
```ML
let
    var N := 8

    type intArray = array of int

    var row := intArray [ N ] of 0
    var col := intArray [ N ] of 0
    var diag1 := intArray [N+N-1] of 0
    var diag2 := intArray [N+N-1] of 0

    function printboard() =
       (for i := 0 to N-1
	 do (for j := 0 to N-1 
	      do print(if col[i]=j then " O" else " .");
	     print("\n"));
         print("\n"))

    function try(c:int) = 
( /*  for i:= 0 to c do print("."); print("\n"); flush();*/
     if c=N
     then printboard()
     else for r := 0 to N-1
	   do if row[r]=0 & diag1[r+c]=0 & diag2[r+7-c]=0
	           then (row[r]:=1; diag1[r+c]:=1; diag2[r+7-c]:=1;
		         col[c]:=r;
	                 try(c+1);
			 row[r]:=0; diag1[r+c]:=0; diag2[r+7-c]:=0)

)
 in try(0)
end
	
```

The corresponding executing result of the generated MIPS program running in *QtSpim*.

![Missing Image](https://github.com/WMBao/TigerCompiler/blob/master/image/2.jpg)


## Introduction

Tiger is a simple imperative programming language, with Integers, Strings, Variables, Arrays, Records and Functions.

Tiger programs are composed of expressions.

Tiger programming language is defined in [*Modern Compiler Implementation in Java*](https://www.cs.princeton.edu/~appel/modern/java/ "*Modern Compiler Implementation in Java*") by Andrew Appel. The detailed information of Tiger is specified in [*Tiger Language Reference Manual*] by Prof. Stephen Edwards. 

The main procedure of a Tiger compiler is shown as follows.

![Missing Image](https://github.com/WMBao/TigerCompiler/blob/master/image/1.jpg)


## General Methods and Tools

### Lexical Analysis

The input source program is tokenized with a lexical analyzer, generated by [*JFlex*](http://www.jflex.de/ "Official Site for JFlex"). 

Various related errors are checked during the analyzing process. 

Nested comments are supported within the source code.


### Syntax Analysis
The tokenized source program is then transformed into an Abstract Syntax Tree (AST) with a parser, generated by [*JavaCUP*](http://www2.cs.tum.edu/projects/cup/ "Official Site for JavaCUP"), if it is syntactically correct.


### Semantic Analysis and IR Tree Generation

Recursively, the AST is translated into an IR tree.

During the process, instances of two class ``Frame`` and ``Level`` keeps the dynamic calling states and static link information.

For MIPS processor, the methods within the class ``MipsFrame`` maintain the frames.

``procEntryExit1``, ``procEntryExit2`` and ``procEntryExit3`` allocate the frame space to the registers.

The IR tree is machine-irrelevant and maintained in package ``Tree``.

Three agent classes are designed to bridge and simplify the generation of specific nodes in IR trees.

``Ex`` corresponds to the expressions with return values.
``Nx`` corresponds to those without return values.
``Cx`` corresponds to conditional expressions, which have two exits.

The data and the functions are maintained in form of DataFrags and ProcFrags.

### Canonicalization

The IR tree generated will be canonicalized for the following operations.

### Code Generation

A MIPS semi-program is generated based on the aforementioned IR tree. 

Registers within the semi-program is represented by intermediate variables.

### Register Substitution

The specific registers are allocated by Graph Coloring.


Finally, all the classes are combined together, with error message reporter ``ErrorMsg`` class and the entrance method ``Main.main()``.