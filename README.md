# vscode_cpp_linux_cfg
A basic collection of some tweaks I'm using to develop with C++ on Linux (fedora), using the gcc compiler. This assumes you already have VSCode, the gcc compiler (which you most likely have since you are on linux) and the C/C++ extension for VSCode installed.

The three files that are present in the repository, namely

* **c_cpp_properties.json**
* **launch.json**
* **tasks.json**

have to be copied in the .vscode directory in your workspace. If you are starting a new project, you can just create the .vscode folder yourself and copy-paste them there.

## tasks.json

This file is the one responsible for the compilation phase of your .cpp files. The command key tells what command should run: this is the path of the g++ executable on my system. Probably you will need to modify it.

args indicate what arguments should be passed to the command specified above. Let's take a closer look to what we have:

`-g` 
this is required to debug your code

`${workspaceFolder}/*.cpp`
this is slightly different from the defaul value that VSCode puts in (`${file}`). By using `${workspaceFolder}/*.cpp` I've been able to make VSCode compile even files with user defined headers, which wasn't possible with the default value. Example:

file **add.h**

```cpp
#ifndef ADD_H
#define ADD_H

namespace my_functions
{

    int add(const int x, const int y);
}
#endif
```

file **add.cpp**

```cpp
namespace my_functions
{

    int add(const int x, const int y)
    {
        return x + y;
    }
}
```
file **main.cpp**

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
-Wall	-Weffc++ -pedantic-errors -Wextra -Wsign-conversion -Werror
 ```
 These flags are telling the compiler what errors (and their level) it should catch. With these settings it catches the majority of them; I chose it because I'm learning the language, so I prefer a stricter check on the code. The `-Werror` will treat warnings as errors.
 
 ```json
 "group": {
		"kind": "build",
		"isDefault": true
	  }
 ```
 
 This tells VSCode to use this configuration as the default one. At the moment the compiler creates an executable only for the current file, although it will link all the dependencies/headers if needed. I suggest using it on your main file. I'll try to make it work better.
 
 ## launch.json
 
 This file is responsible of launching the executable created after the compiler and linker phase. Most of it is similar to the default one created by VSCode.
 
 ```"targetArchitecture": "x86_64"```
 This line is setting the architecture we want our executable to be compliant with.
 
 ```"preLaunchTask": "C/C++: g++ build active file"```
 This line is simply saying what command or job it should execute before running the code. The value I have used is the same label present in the tasks.json file. If the executable doesn't exist or you have made some changes to the code, it will automatically re-compile the code. It's just a way to save time because we can just hit the Run command instead of compile and then Run.
 
 ## c_cpp_properties.json
 
 This file contains the settings for the C/C++ extension from the VSCode marketplace. The `include` path will tell the compiler where to check for headers, while
 
 ```
 "cStandard": "c17",
 "cppStandard": "c++17"
 ```
 
 sets the language standard used for IntelliSense. Lastly, `intelliSenseMode` should be set to match the architecture you are developing on (I'm using fedora 64 bit). You can also change this configs by using a GUI menu: open the command palette, then type 
`C/C++:Edit Configurations (UI)`.
