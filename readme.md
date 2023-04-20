#### Dynamically linked or Shared Library or Shared Object


Create a header file called mymath.h containing:
```c
int add(int a, int b);
int sub(int a, int b);
int mult(int a, int b);
int divi(int a, int b);
```
There, will be all declarations used in main process, where the implementations of these functions will be defined in the shared object "so" extension.

Create our main process called mathDemo.c containing:
```c
#include <mymath.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, const char* argv[])
{
  int x, y;
  printf("Enter two numbers\n");
  scanf("%d%d",&x,&y);

  printf("\n%d + %d = %d", x, y, add(x, y));
  printf("\n%d - %d = %d", x, y, sub(x, y));
  printf("\n%d * %d = %d", x, y, mult(x, y));

  if(y==0){
    printf("\nDenominator is zero so can't perform division\n");
    exit(0);
  }else{
    printf("\n%d / %d = %d\n", x, y, divi(x, y));
    return 0;
  }
}
```
And on the other hand.
Create a folder named Shared where all the shared library implementation files will be located:

`$ mkdir Shared`

create a file `add.c` containing:
```c
int add(int a, int b){
    return (a+b);
}
```
create a file `sub.c` containing:
```c
int sub(int a, int b){
    return (a-b);
}
```
create a file `mul.c` containing:
```c
int mult(int a, int b){
    return (a*b);
}
```
create a file `div.c` containing:
```c
int divi(int a, int b){
    return (a/b);
}
```
Our main process is called mathDemo 
```bash
$ tree
.
├── mathDemo.c
├── mymath.h
└── Shared
    ├── add.c
    ├── div.c
    ├── mul.c
    └── sub.c
```
Inside the folder called "Shared". Create object files:

`$ gcc -c add.c sub.c mul.c div.c -fpic`

Create a shared library or shared object, called libmymath.so 

`$ gcc *.o -shared -o libmymath.so`

Then remove the object files, as they're no longer required.

`$ rm *.o`

Verify
```bash
$ ls
add.c  div.c  libmymath.so  mul.c  sub.c
```
To create a dynamically linked application:

Inside the main folder

Create an object file called mathDemo.o from mathDemo.c:
If the header file is included between quotes `"headerFile"`:

`$ gcc -c mathDemo.c -o mathDemo.o` 

or if it is included using brackets `<headerFile>` instead:

`$ gcc -I . -c mathDemo.c`

The `-I` option tells GCC to search for header files listed after it. In this case, you're specifying the current directory, represented by a single dot `.`

Following step

Link mathDemo.o with libmymath.so to create the final executable. 
There are two ways to express this to GCC. 
Knowing: -L{path to file containing library} -l${library name}

`$ gcc -o mathDemo mathDemo.o ./Shared/libmymath.so`

or

`$ gcc -o mathDemo -L./Shared/ mathDemo.o -lmymath` 

Then remove the object files, as they're no longer required.

`$ rm *.o`

Copy or export the shared object from the main folder:

`$ sudo cp ./Shared/libmymath.so /usr/lib`

or in case of being in the Shared folder instead:

`$ sudo cp libmymath.so /usr/lib`

Analyzing the result
```bash
$ file mathDemo
mathDemo: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), 
dynamically linked, BuildID[sha1]=a156d30d2ea563a3d6d4a2a1d8cb949e6fc5c810, 
not stripped
```