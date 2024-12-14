# CPP-File-Manager

C++ File Manager allows you to list files in C++, ignore extensions (or just list specified extensions) and write the whole directory structure into a fancy tree-like structure which can be integrated in Markdown files. This is the first release. Please file issues with `[FEATURE REQUEST]` in the title, and we'll be happy to take a look at it.

The C++ File Manager provides the following functions to users:

1. `clear()` - Clears the memory allocated to corePath.
2. `clear(std::string new_path)` - Assigns a new path to the corePath variable.
3. `info()` - Prints the corePath assigned to the FileManager object to the console.
4. `list_files(std::vector<std::string> extensions, bool ignore_extensions)` - Lists the files and directories in corePath. The first argument is *extensions* which is a vector of file extensions to be ignored. These extensions are ignored only when the second argument i.e. `ignore_extensions` is set to `true`.
5. `writeToFile(std::vector<string> ignore_dirs, std::vector<std::string> ignore_extension)` - Writes the tree structure for the files and directories in corePath into a `.txt` file. This functions iterates till the innermost files of all the directories. The first argument `ignore_dirs` contains a vector of directories to be ignored in the tree structures. Similarly, the second argument `ignore_extensions` contains a vector of files extensions to be ignored in the final tree structure.

## Usage 

### Using Binary

The **fmanager** binary has the following options :

```
  -h  --help                        Print usage.
  -p  --path filePath               Input path (to be iterated).
  -l  --list_files                  Call the list files function.
  -t  --tree                        Call the tree function.
  -d --ignore_dirs dir1,dir2        Ignore dirs while creating tree
  -e --ignore_extensions ext1,ext2  Ignore extensions while creating tree
  -s --separator                    Separator you want for your tree output
```

**Listing files in a directory**

Command: `fmanager -p samples`

By default, it will list files in the given directory (here `samples`). Output will look like this:

```bash
Got path: samples
sample.cpp
README.md
libcpp-file-manager.a
CMakeLists.txt
FileManager.hpp
```

**Building tree of the given directory and ignoring directories and extensions**

`./fmanager -p ./ -t -d include,.git -e .cpp`\
_OR_ \
`./fmanager --path ./ --tree --ignore_dirs include,.git --ignore_extensions .cpp`

``` 
Got path: ./
tree.txt
CMakeLists.txt
samples
src
.github
README.md
.git
include
```

The `tree.txt` file stores the following directory structure:

```
|-- tree.txt
|-- CMakeLists.txt
|-- samples
    |-- libcpp-file-manager.a
    |-- CMakeLists.txt
    |-- README.md
    |-- FileManager.hpp
|-- src
|-- .github
    |-- workflows
        |-- build-filemanager.yml
|-- README.md
|-- .git
|-- include
```

In case you want to change the separator from default (`|--`) to something like `-` or `*`, do:

```bash
./fmanager -s '-' -t
./fmanager --separator '-' --tree
```

The generated `tree.txt` will now contain `-` instead of `|--`:

```
- tree.txt
- CMakeLists.txt
...
```

### Using the library in your C++ Code

To be able to use `FileManager` library, head over to the latest release and download `.a` (library file) and `.hpp` (header file). Copy these files in your current folder (or wherever you desire, just remember the path). To compile, use:

```bash
g++ filename.cpp -L . -lcpp-file-manager -o out
```

Here are a few steps on using the library in your code:

1. Create an object of `Filemanager` class and initialize it with a path:

``` 
std::string path = "/home/BuffetCodes/Documents/CPP-File-Manager"; // Change this with your path, either relative or absolute
FileManager file(path);
```

2. The `file.list_files()` function returns a vector containing names of files/directories with additional information. We can iterate through it as follows:

```cpp
// The type returned is a struct, head over to the header file for more details on it
for (auto const& item: file.list_files()) {
    // Use item.rname if you want "just" the name of the file or folder
    // item.name returns absolute path (with respect to the path given)
    // item.is_dir returns true if it's a directory, else false
    std::cout << item.name << "\n";
}
```

The output will be as follows:

```
./tree.txt
./CMakeLists.txt
./samples
./src
./.github
./README.md
./.git
./include
```

3. The `file.writeToFile()` call, creates a text file `tree.txt` representing the directory structure: 

```cpp
std::vector<std::string> ignore_dirs = {".git", ".github", ".vscode", "build"};
std::vector<std::string> ignore_extensions = {".pyc", ".swp"};
file.writeToFile(/*ignore_folders=*/ ignore_dirs, /*ignore_extensions=*/ ignore_extensions);
```

## Build

The current release (1.0) only supports GNU/Linux Systems. Please head to the relevant opened issues to see the progress on Windows and MacOS. Use the following steps to build from source:

```bash
git clone https://github.com/BuffetCodes/CPP-File-Manager.git && cd CPP-File-Manager
mkdir build && cd build
cmake .. && make
```

The library file: `libcpp-file-manager.a` will be generated in `build/` directory. Copy the header file in `include/` and library file from `build/` directory to your folder to use it. Or, just head over to the `samples` folder in this project on how to compile using `CMake` or `g++`.
