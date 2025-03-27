# Dir-functions-in-c++

Directory Listing Tool with File Ownership (C++)
This tool allows you to list files and directories in a specified directory on a Windows system. It provides functionality similar to the dir command with additional features, such as listing files recursively and displaying file ownership information.

Features
List files and directories: Similar to the dir /a command.

List files and directories recursively: Like dir /s.

Show file owner: Displays the file owner similar to dir /q.

Prerequisites
A Windows operating system (uses Windows-specific APIs for file ownership).

A C++17-compatible compiler (such as Microsoft Visual Studio, MinGW, etc.).

C++17 or later is required for std::filesystem.

How to Compile
Using Microsoft Visual Studio:
Create a new C++ Console Application:

Open Microsoft Visual Studio.

Create a new C++ Console Application project.

Copy the code into your main source file (e.g., main.cpp).

Set C++17 or later:

Right-click on your project in the Solution Explorer.

Select Properties.

Under Configuration Properties, go to C/C++ > Language.

Set C++ Language Standard to ISO C++17 or later.

Build and run the project:

Press Ctrl + F5 to build and run.

Using MinGW or other GCC-compatible compilers:
Save the code in a file (e.g., directory_listing.cpp).

Open Command Prompt (or terminal) and navigate to the folder where the file is saved.

Compile the code using the following command:


g++ -std=c++17 directory_listing.cpp -o directory_listing
Run the compiled program:


directory_listing.exe
Usage
When you run the program, it will prompt you for a directory path, and then you can choose how you want to list the files.

Command Options:
Enter directory path: The program will ask for a valid directory path. If the path is invalid, an error will be shown.

Enter option:

/a: List files and directories (non-recursive).

/s: List files and directories recursively.

/q: List files and directories with owner information.

Special Notes:
The program uses Windows-specific APIs like GetFileSecurity and LookupAccountSidW to retrieve file owner information, which means it will only work on Windows systems.

Ensure the directory path is valid and accessible to avoid errors.

When the owner information is requested (/q option), it may take a bit longer to retrieve and display, especially for directories with many files.

Troubleshooting
Program does not compile: Ensure that you're using a C++17 or later compiler, as std::filesystem is required for the program.

Unable to get file owner: Make sure the program is run with appropriate permissions, as retrieving ownership may require administrator rights.

The specified path is not a valid directory: Double-check that the entered path exists and is a directory, not a file.

License
This tool is provided as-is, under the MIT License.
