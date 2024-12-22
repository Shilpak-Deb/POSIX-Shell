**Author:** &nbsp; Shilpak Deb

Implementation of a POSIX Shell via a set of C++ programs.

---

## Instructions for Execution:

The following are the steps for how to open the shell terminal:

### How to Compile:

```bash
make
```

### How to Run:

```bash
./runterminal
```

---

## Folder Contents:

**( Folder Name**: POSIX Shell **)**

### makefile:

- Compiles the entire set of .cpp files in one go and create the '**runterminal**' executable file

### header file:

- **shell_commands.h**: &nbsp; Allows the various functions and external global variables to be accessed from any of the .cpp files

### .cpp files:

- **main.cpp**: &nbsp; Runs an infinite loop through which shell is implemented
- **misc.cpp**: &nbsp; Has some miscellaneous functions that are used multiple times throughout the shell implementation
- **shell.cpp**: &nbsp; Implements the various functionalaties of the shell terminal (eg: displaying prompts, accepting user input, parsing and executing commands, etc.)
- **home.cpp**: &nbsp; Keeps track of the home directory from which the shell is initially executed
- **signals.cpp**: &nbsp; implements the **Ctrl -Z** and **Ctrl -C** signals
- **cd_command.cpp**: &nbsp; implements the user-made **cd** command
- **ls_command.cpp**: &nbsp; implements the user-made **ls** command
- **echo_command.cpp**: &nbsp; implements the user-made **echo** command
- **pinfo_command.cpp**: &nbsp; implements the user-made **pinfo** command
- **search_command.cpp**: &nbsp; implements the user-made **search** command
- **history_command.cpp**: &nbsp; implements the user-made **history** command along with **up** and **down arrow keys**, and the **autocomplete** feature

### .txt files:

- **history.txt**: &nbsp; Keeps track of last 20 unique commands entered by user across multiple shell sessions (automatically created the first time the shell is run)

### executable files:

- **runterminal**: &nbsp; Executable file to run the shell

---

## Functionalities Implemented:

### Commands:

**cd** &nbsp; -- &nbsp; _utilized to change the current working directory based on path provided_

- Implemented in: &nbsp; **cd_command.cpp**
- Uses **~** to refer to the home directory of the terminal instead of the actual home directory

---

**pwd** &nbsp; -- &nbsp; _utilized to show the present working directory_

- Implemented in: &nbsp; **shell.cpp**
- Ignores any additional arguments provided to it (similar to its actual working in the bash shell)

---

**echo** &nbsp; -- &nbsp; _utilized to display whatever arguments are passed to it_

- Implemented in: &nbsp; **echo_command.cpp**
- Cannot handle multi-line strings
- Tried to implement handling of **quotes** - works in most cases, however some edge cases may still persist

---

**ls** &nbsp; -- &nbsp; _utilized to display the contents of the directory (based on the flags provided to it)_

- Implemented in: &nbsp; **ls_command.cpp**
- Can only implement the **-l** and **-a** flags

---

**pinfo** &nbsp; -- &nbsp; _utilized to display various information related to a particular process_

- Implemented in: &nbsp; **pinfo_command.cpp**
- Accesses the following in order to read data for a particular process (based on its **pid**)
  - proc/[pid]/stat
  - proc/[pid]/statm
  - proc/[pid]/exe
- Cannot access the data of processes that require special permissions

---

**search** &nbsp; -- &nbsp; _utilized to recursively search for a particular file or folder in the current directory_

- Implemented in: &nbsp; **search_command.cpp**
- Searches for both hidden and non-hidden files/directories

---

**history** &nbsp; -- &nbsp; _utilized to display the previous commands executed by the user_

- Implemented in: &nbsp; **history_command.cpp**
- Can store a maximum of 20 previous commands - if a new command is entered, the data for the earliest command is overwritten
- By default, it shows only the last 10 commands
- Utilizes the **history.txt** file to store the last 20 commands and preserve the data between multiple runs of the shell terminal

---

**exit** &nbsp; -- &nbsp; _utilized to exit the terminal_

- Implemented in: &nbsp; **shell.cpp**
- Extra user-made command that serves the same purpose as **Ctrl -D**

---

**( &nbsp; _other commands:_** &nbsp; &nbsp; Implemented via **execvp()** **)**

---

### Signals:

(Implemented in: &nbsp; **signals.cpp**) &nbsp; (for **Ctrl -Z** and **Ctrl -C**)

(Implemented in: &nbsp; **shell.cpp**) &nbsp; (for **Ctrl -D**)

- **Ctrl -Z** &nbsp; -- &nbsp; **(** Stop Signal **)** &nbsp; -- &nbsp; Implemented via signal handlers in the main function
- **Ctrl -C** &nbsp; -- &nbsp; **(** Interrupt Signal **)** &nbsp; -- &nbsp; Implemented via signal handlers in the main function
- **Ctrl -D** &nbsp; -- &nbsp; **(** Terminal Exit Signal **)** &nbsp; -- &nbsp; Implemented via checking for '**\004**' character during **raw mode** data input - can only exit from the terminal when it is not executing any foreground processes

---

### Foreground/Background Processes:

(Implemented in: &nbsp; **shell.cpp**)

- Implemented for only non- user-made commands
- Backgorund processes display their **process ID** when their commands are first executed

---

### Pipeline:

(Implemented in: &nbsp; **shell.cpp**)

- Implemented for both user-made and non- user-made commands and any combination thereof
- Works for chains of multiple **pipes**
- Works in combination with **<**, **>**, **>>** operators

---

### I/O Redirection:

(Implemented in: &nbsp; **shell.cpp**)

- Implemented for both user-made and non- user-made commands and any combination thereof
- Works for chains of multiple **<**, **>**, **>>** operators
- Works in combination with **pipes**

---

### Up/Down Arrow Keys:

(Implemented **Down Arrow Key** in addition to **Up Arrow Key**)

(Implemented in: &nbsp; **history_command.cpp**)

- Accesses the **history.txt** file to read the previous entries made to the shell
- Utilizes **raw mode** data input to immediately read when arrow keys are pressed without waiting for **ENTER** to also be pressed
- **Up Arrow Key**: &nbsp; &nbsp; shows the previous 20 commands (starting from the most recent one) - and stops on reaching the earliest command (further presses keeps the display at the earliest command)
- **Down Arrow Key**: &nbsp; &nbsp; moves down the list of previous commands (from earlier to later) after the **Up Arrow Key** has been pressed - and stops on reaching the command being currently typed (further presses keeps the display at the command being typed)

---

### Autocomplete:

(Done slightly differently than what was mentioned in assignment - utilizes both single and double TAB keys)

(Implemented in: &nbsp; **history_command.cpp**)

- **Single TAB**: &nbsp; &nbsp; Implemented for both commands and files/directories - replaces the incomplete portion with the longest prefix of all the matching commands/files/directories
- **Double TAB**: &nbsp; &nbsp; Implemented for both commands and files/directories - displays all the matching commands/files/directories in a new line
- Commands are matched against the names of files present in
  - /bin
  - /usr/bin
  - /sbin
  - /usr/sbin
- The **man** page of a particular command is accessed to check if it contains **"directory"** and/or **"file"** strings and the files and directories are matched accordingly (eg. **cd** only take directories as input)
- The file/directory names are matched against the contents of the current working directory of the shell

---

## Constraints:

- This program works only for linux-based operating systems (due to using linux-specific system calls)
- The program creates can only implement limited functionality of the user-made commands when compared to the linux shell
- This program cannot implement side arrow keys while typing in the shell terminal

---
