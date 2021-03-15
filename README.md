# vscode_cpp_linux_cfg
A basic collection of some tweaks I'm using to develop with C++ on Linux (fedora), using the gcc compiler. This assumes you already have VSCode installed and the gcc compiler (which you most likely have since you are on linux).

The three files that are present in the repository, namely

* c_cpp_properties.json
* launch.json
* tasks.json

have to be copied in the .vscode directory in your workspace. If you are starting a new project, you can just create the .vscode folder and copy-paste them there.

## tasks.json

This file is the one responsible for the compilation phase of your .cpp files. The command key tells what command should run: this is the path of the g++ executable on my system. Probably you will need to modify it.

args indicate what arguments should be passed to the command specified above. Let's take a closer look to what we have:

`-g` 
this is required to be able to debug your code

`${workspaceFolder}/*.cpp`
this is slightly different from the defaul value that vscode puts in (`${file}`). By using `${workspaceFolder}/*.cpp` I've been able to make vscode compile even files with user defined headers, which wasn't possible with the default value. Example:

file add.h

```cpp
#ifndef ADD_H
#define ADD_H

namespace my_functions
{

    int add(const int x, const int y);
}
#endif
```

file add.cpp

```cpp
namespace my_functions
{

    int add(const int x, const int y)
    {
        return x + y;
    }
}
```
file main.cpp

```cpp
#include "maths_functions.h"
#include <iostream>

int main(){
    
    std::cout << my_functions::add(5,4) << '\n';
    return 0;
}
```

By using `${workspaceFolder}/*.cpp` as pointed out, the linker can find the definition for the add function.

`-o ${workspaceFolder}/bin/${fileBasenameNoExtension}`
This tells the compiler where to put the executable file created. Note that I'm telling it to place it in the bin directory inside the current project. The downside is that you have to manually create the bin folder before launching the compiler. Of course, you can choose a different path: this is just something that I changed so that executables aren't mixed with source files.

```
-Wall	-Weffc++ -pedantic-errors -Wextra -Wsign-conversion
 ```
 These flags are telling the compiler what errors (and their level) it should catch. With these settings it catches the majority of them; I chose it because I'm learning the language, so I prefer a stricter check on the code. The -pedantic-errors will treat warnings as errors.
 
 ```json
 "group": {
				"kind": "build",
				"isDefault": true
			}
 ```
 
 This tells VSCode to use this configuration as the default one.
 
 ## launch.json
 
 This file is responsible of launching the executable created after the compiler and linker phase. Most of it is similar to the default one created by VSCode.
 
 ```"targetArchitecture": "x86_64"```
 This line is setting the architecture we want our executable to be compliant with.
 
 ```"preLaunchTask": "C/C++: g++ build active file"```
 This line is simply saying what command or job it should execute before running the code. The value I have used is the same label present in the tasks.json file. If the executable doesn't exist or you have made some changes to the code, it will automatically re-compile the code. It's just a way to save time because we can just hit the Run command instead of compile and then Run.
 
 
