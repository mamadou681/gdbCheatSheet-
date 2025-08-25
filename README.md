# GDB Debugging Guide
This comprehensive guide provides a detailed overview of using the GNU Debugger (GDB) to debug C/C++ programs.
It covers essential aspects from setting up your environment for debugging to advanced techniques for complex scenarios.
Whether you are a beginner or an experienced developer, this document aims to be a valuable resource for mastering GDB and efficiently identifying and resolving issues in your code.


## Setting Up Your Environment (Compilation with Debug Symbols)

### Compiler Flags:
The most crucial step is to compile your C/C++ code with debugging symbols. This is achieved using the `-g` flag with `gcc or g++`.

Example:
```sh
g++ -g my_program.cpp -o my_program
```

### Optimization:
While debugging, you often want to disable optimizations (`-O0`) to ensure the code behaves predictably. Optimizations can reorder instructions and make debugging harder.

Example:
```sh
g++ -g -O0 my_program.cpp -o my_program
```

### Debug Levels (`-g` flags)

gcc or g++ provides multiple levels of debug information via the `-g` flag:

- `-g0`: No debug information.
- `-g1`: Some debug info, but no line numbers.
- `-g2` (default): Line numbers, symbols, and file info. Same as just `-g`.
- `-g3`: Adds even more detail, such as macro definitions.
- `-ggdb`: Debug info tailored for GDB (similar to `-g3`, but GDB-specific).
  - Useful for tough debugging cases.
  - `-ggdb3`: Most detailed GDB-specific info.

#### Tip
Use `info macro <macro_name>` in GDB to inspect macro expansions.

**More Info**: [GCC Debugging Options](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html)


##  Basic GDB Commands

### Starting GDB:
```sh
gdb my_program      # Starts GDB with the executable
gdb --args my_program arg1 arg2   # Starts GDB with command-line arguments
gdb -p <pid>        # Attaches GDB to a running process
```

### Running the Program:
```sh
run or r            # Starts the program
run <args>          # Runs the program with specific command-line arguments
```

### Setting Breakpoints:
```sh
break main or b main                 # Sets a breakpoint at the main function
break <function_name>                 # Sets a breakpoint at a specific function
break <filename>:<line_number>        # Sets a breakpoint at a specific line
break <address>                       # Sets a breakpoint at a specific memory address
break if <condition>                  # Sets a conditional breakpoint
```

### Stepping Through Code:
```sh
next or n     # Executes the current line, stepping over function calls
step or s     # Executes the current line, stepping into function calls
finish        # Executes until the current function returns
until <line_number>  # Executes until a specific line is reached
```

### Inspecting Variables:
```sh
print <variable_name> or p <variable_name>  # Prints the value of a variable
ptype <variable_name>  # Prints the type of a variable
display [format] <variable_name>  # Displays the variable's value after each step
undisplay <display_number>  # Stops displaying a variable
x/<format> <address>  # Examines memory at a specific address
```

Formats:
- `x` (hex)
- `d` (decimal)
- `s` (string)
- `i` (instruction)

### Navigating the Stack:

- ```sh
  backtrace or bt              # Display the stack trace
  ```

- ```sh
  frame <number> or f <number> # Select a frame in the call stack
  ```
- ```sh 
   up or u                     # Move up the call stack
  ```
- ```sh 
  down or d                    # Move down the call stack
  ```
- ```sh
    info args                  # Retrieve argument values of the current stack frame
  ```
- ```sh 
   info frame                  # Retrieve frame information
  ```
- ```sh 
  info locals                  # Retrieve local variables in the current frame
  ```
- ```sh
  finish                       # Continue until we return from the current function in the stack frame.
  ```
### Continuing and Quitting:
```sh
continue or c   # Continues execution until the next breakpoint or program termination
quit or q       # Exits GDB
kill            # Kills the running process within GDB
```

### Listing Source Code:
```sh
list or l                       # Lists source code around the current line
list <function_name>            # Lists source code of a specific function
list <filename>:<line_number>   # Lists source code around a specific line
```

## Debugging Techniques

### Debugging Segmentation Faults:
1. Run the program in GDB.
2. When a segmentation fault occurs, use `backtrace` to see the call stack.
3. Examine variables in the frames to identify the source of the memory error.
4. Use `info locals` to see local variables in the current frame.

### Debugging Memory Leaks:
- Use tools like Valgrind in conjunction with GDB.
- Valgrind detects memory leaks, and GDB helps pinpoint the exact location.

### Debugging Libraries:
- If you're using shared libraries, ensure they were compiled with debug symbols.
- Set breakpoints in library functions.
- Use `info <sharedlibrary>` to view loaded libraries.

### Catching Exceptions:
```sh
catch throw   # Sets a breakpoint when an exception is thrown
catch catch   # Sets a breakpoint when an exception is caught
```

##  Advanced GDB Features

### Watchpoints:
```sh
watch <variable_name>     # Triggers when the variable's value changes
rwatch <variable_name>    # Triggers when the variable is read
awatch <variable_name>    # Triggers when the variable is read or written
info watchpoints          # Lists watchpoints
```

### TUI Mode:
```sh
layout src      # Displays source code
layout regs     # Displays registers
layout split    # Displays source and assembly
Ctrl+x a        # Toggles TUI mode
```

##  C++ Specific Debugging

### Debugging Templates:
- GDB can sometimes struggle with complex template instantiations.
- Use `ptype` to understand the actual types.

### Inspecting Virtual Functions with GDB

You can use GDB to inspect which virtual function was called at runtime — useful when working with polymorphism and inheritance in C++.

- **`whatis object_name`**
  - Shows the declared type of the object.

- **`info vtbl object_name`**
  - Displays the virtual table (vtable) of the object.

### Debugging STL Containers:
- GDB has pretty printers for STL containers, making it easier to inspect their contents.

### Debugging Smart Pointers:
- GDB can print the raw pointer held within smart pointers.

### Name Mangling:
- C++ compilers mangle function names to support overloading and namespaces.
- If you see a mangled name, you can use a `c++filt` command outside of GDB to demangle it.
```sh
 c++filt <mangled_func_name>       # outside of GDB
 shell c++filt <mangled_func_name> # in GDB
```

##  Additional GDB Commands

### Controlling Execution Flow:
```sh
advance <line_number>   # Continues execution until the specified line is reached
until <line_number>     # Runs the program until it reaches the given line
skip <number>          # Skips the specified number of instructions
jump <line_number>     # Jumps to a specific line of code without executing intervening lines
```

### Managing Breakpoints:
```sh
save breakpoints <filename>   # Saves the current breakpoints to a file
source <filename>             # Loads breakpoints from a saved file
enable <breakpoint_number>    # Enables a disabled breakpoint
disable <breakpoint_number>   # Disables a breakpoint without removing it
```

### Function Calls:
```sh
call <function>(args)   # Calls a function with specified arguments from within GDB
```

### Getting Information:
```sh
info functions    # Lists all functions in the program
info display      # Lists all expressions set to display automatically after each step
info catchpoints  # Displays all catchpoints (breakpoints for exceptions)
```



##  Redirecting GDB Output

Often, your program may log lots of information to standard output, cluttering the debugging experience. You can redirect output as follows:

### Redirect with `tty`:
1. Open a second terminal and type:
   ```sh
   tty
   ```
2. This tells you where the terminal is connected for standard input.

3. Within GDB, type:
   ```sh
   tty <location_of_terminal_from_tty_command>
   ```

### Redirect to a file:
Instead of displaying output on the terminal, you can redirect it to a file by running:
   ```sh
   run > outputfilename.txt
   ```

Now you can use GDB more cleanly!

##  Core Dumps (Updating `ulimit`)
A core dump (or core file) is a snapshot of the memory of a program when it crashes. It helps in debugging why the program crashed.

### Enabling Core Dumps:
1. Install the necessary package:
   ```sh
   sudo apt install systemd-coredump
    ```
2. Check the current core dump limit:
   ```sh
   ulimit -c
   ```
   - If the value is 0, change it to unlimited:
   ```sh
   ulimit -c unlimited
   ```
   - This will allow core dumps to be recorded.
   - TO display all current resource limits:
     ```sh
	 ulimit -a
	 ```
   
 3. Compile your C/C++ program with debugging symbols:
    ```sh
	g++ -g my_program.cpp -o my_program
	```
 4. Run the program and observe the core file creation:
    ```sh
	./my_program
	```
 5. Load the last core dump file:
    ```sh
	coredumpctl gdb
    ```
	- You can also inspect where the core file is stored using:
	  ```sh
	  coredumpctl dump
      ```
	- Alternatively, open a specific core file in GDB:
	```sh
	gdb -c corefilename
    ```
	
###	Location of Core Files:
- As of Ubuntu 21.10 and 22.04, core files are stored inside:
  ```sh
  /var/lib/apport/coredump
  ```
 ### Additional Notes:
- You can force a program to terminate and generate a core dump using:
  ```sh
  Ctrl + \
  ```
  
 ### Core Dumps Using gcore
The `gcore` utility generates a core dump of a running process, providing a memory snapshot for post-mortem debugging.
This enables analysis of process state at a specific point in time, aiding in the identification of software defects.

1. Start a process and obtain the process ID.

2. Run ```gcore pid ```  # where pid is the process ID from step 1

    - Optionally run: ```gcore -o <outputfilename> pid ```

3. Run ```gdb ./my_program corefile```

   - This ensures the program symbol names will be loaded.

   - Alternatively: ```gdb -c corefilename or gdb -core corefile ```

   - Manually load symbols: file ./my_program

4. Debug using ```bt``` and ```up``` to navigate the stack frame until you hit your source code, which you can then inspect with ```layout src```

	
 ### Examining Memory with x/nfu

The ```x/nfu``` command in GDB is useful for inspecting raw memory and data structures.

  ```sh 
   x/nfu <address>
  ```

Where:

- ```n``` = number of units to display

- ```f``` = format (e.g., x for hex, d for decimal, s for string, etc.)

- ```u``` = unit size (b for byte, w for word, etc.)

#### Format Specifiers (f)

Common format options:

- ```x``` - hexadecimal

- ```d``` - signed decimal

- ```u``` - unsigned decimal

- ```o``` - octal

- ```t``` - binary

- ```f``` - float

- ```a``` - address

- ```c``` - character

- ```s``` - string

- ```i``` - instruction (disassembly)

#### Unit Sizes (u)

- ```b``` - byte (1 byte)

- ```h``` - halfword (2 bytes)

- ```w``` - word (4 bytes)

- ```g``` - giant word (8 bytes)

Examples:
```sh
x/dw &myInt              # Display as decimal word (4 bytes)
x/xw &myInt              # Display as hexadecimal word
x/s myCharArray          # Display as null-terminated string
x/10cb myCharArray       # Display first 10 characters as bytes
x/4xg &myObject          # Display first 32 bytes (4 giant words) as hex
x/3xw myPtr              # Display 3 words (12 bytes) at pointer address
x/4fw &myFloatArray      # Display 4 floats
x/a *((void**)&myObject) # First entry is usually vtable pointer
x/3a *(void**)&myObject  # Show first 3 vtable entries
p myPtr                  # Print pointer value
x/10x $                  # Examine memory at last printed value ($)
Examining STL containers:
x/3x &myVector           # Often shows start, finish, and end pointers
For arrays:
x/10dw myArray           # Display 10 elements as decimal words
Register Examination:
x/x $rsp                 # Examine stack pointer
x/8x $rsp                # Examine stack (8 words)
x/i $rip                 # Examine current instruction
```

### Tips for C++ Debugging

- Use p (print) for high-level inspection of variables and objects, and x when you need low-level memory examination.

- Remember that class members may have padding between them, so memory layout might not be exactly what you expect.

- For polymorphic objects, the first entry is typically the vtable pointer.

- When examining memory, be aware of endianness on your system.

- Use ```set print object on``` to help with polymorphic type identification.

## Executing Commands Automatically After a Breakpoint Hits

A neat trick in GDB is to execute specific commands automatically when a breakpoint is triggered.
This can save time and automate debugging tasks.

### Steps to Set Up Breakpoint Commands
1. After creating a breakpoint, type:
```sh
   commands
```
2. Then, enter the commands you want to execute when the breakpoint is hit.
3. Type ```end``` to finish defining the commands.

### Example Usage
#### Example 1: Print a Variable and Continue Automatically
```sh
(gdb) break my_function
Breakpoint 1 at 0x123456: file main.cpp, line 42.
(gdb) commands
> print my_variable
> continue
> end
```
In this example, every time ```my_function``` is hit, ```my_variable``` is printed, and execution continues without pausing.

#### Example 2: Log Stack Trace and Stop Execution
```sh
(gdb) break some_function
(gdb) commands
> backtrace
> info locals
> end
```
This setup will automatically show the stack trace and local variables when some_function is hit.

#### Example 3: Conditional Execution Based on a Variable

Sometimes, you only want to execute commands if a variable has a specific value.
```sh
(gdb) break my_function
(gdb) commands
> if my_variable == 42
>     print "Hit breakpoint with my_variable = 42"
>     continue
> end
> end
```
If ```my_variable``` is 42, it prints a message and continues execution automatically. Otherwise, execution stops at the breakpoint.

#### Example 4: Skipping Breakpoints for Specific Conditions

If you want to skip the breakpoint under certain conditions, use ```continue``` within an ```if``` statement.
```sh
(gdb) break process_data
(gdb) commands
> if data_size < 100
>     continue
> end
> print data_size
> backtrace
> end
```
If ```data_size``` is less than 100, the breakpoint is skipped. Otherwise, ```data_size``` is printed and the stack trace is displayed.

#### Example 5: Logging Breakpoint Hits to a File

If you need to log every time a breakpoint is hit, you can use printf and shell commands.
```sh
(gdb) break handle_request
(gdb) commands
> printf "Breakpoint hit at handle_request\n"
> shell echo "Hit at $(date)" >> log.txt
> continue
> end
```
This will log each breakpoint hit to ```log.txt``` with a timestamp.

#### Example 6: Automatically Dump Memory When a Function is Called
```sh
(gdb) break allocate_memory
(gdb) commands
> x/16xw ptr  # Examine 16 words of memory at ptr
> continue
> end
```
Each time ```allocate_memory``` is called, the command will dump 16 words from memory.

## Defining Custom Commands in GDB

GDB allows you to create your own commands to automate repetitive tasks.
The syntax for defining a command is:

```sh
(gdb) define command_name
> <GDB commands>
> end
```

Where:

- ```command_name``` is the name of your custom command.

- ```<GDB commands>``` are the commands that will be executed when you call ```command_name```.

- ```end``` marks the end of the command definition.

### Example: A Custom Command to Print Registers and Backtrace
```sh
(gdb) define reg_and_bt
> print $pc
> print $sp
> bt
> end
```
Now, when you type ```reg_and_bt``` in GDB, it will:

1. Print the program counter (```$pc```).
2. Print the stack pointer (```$sp```).
3. Display the backtrace (```bt```).

## Automating Tasks with ```.gdbinit``` and Script Execution

If you want GDB to execute a series of tasks automatically when it starts, you can use the ```.gdbinit``` file.
This file can be placed in your home directory (```~/.gdbinit```) or the directory where GDB is executed.
Additionally, you can use script files for executing specific tasks.

### Using `.gdbinit` for Persistent Commands
GDB automatically loads ```.gdbinit``` if found in the current directory or the home directory.
This is useful for setting up breakpoints, custom commands, or debugging configurations.

Example ```.gdbinit``` file:
```sh
define my_custom_cmd
    print "Executing custom command!"
    info registers
end
```
When GDB starts, it will apply these settings, and you can run ```my_custom_cmd``` as a custom command.

### Executing GDB Scripts with -x
Instead of modifying ```.gdbinit```, you can execute a specific script using the ```-x``` option.

Example script (```debug_script.gdb```):
```sh
break main
run
info locals
```
Run:
```gdb -x debug_script.gdb ./my_program```

This script will set a breakpoint at main, start the program, and print local variables.

## Executing Shell Commands from GDB

From within GDB, you can execute many commands from the shell just like you normally would.
You can prefix with shell to explicitly do this, and some other commands like make that are common will just execute.

- You can run normal shell commands within GDB if you like:
```shell ls```
- You can additionally pipe the output from GDB to shell commands:
```pipe print x | nl```

Some other common commands like ```make``` will also work within GDB without having to type ```shell```
beforehand (but you can also just do ```shell make```).

## Editing Files Within GDB

On occasion, you may want to make a change within GDB using your editor of choice.

- First make sure that you set your editor in an environment variable prior to using GDB:
```sh
EDITOR=/usr/bin/vim
export EDITOR
```
- Then type ```edit linenumber``` or ```edit function``` to begin editing the line number or corresponding function within GDB.

Load a file with ```file``` to execute a new file if you need to rebuild (```make``` will typically reload the program).
 
### After making changes and saving, type:
```sh
refresh
```
This will update the displayed source code in GDB's TUI mode.

## Using Python in GDB

GDB has a full Python interpreter embedded, allowing for advanced debugging automation.

### Example Commands:
- Run a simple Python command in GDB:
```sh
python print("hi")
```
- Start a Python session and execute commands:
```sh
python
print("hello from Python")
import gdb
end
```
- Use the GDB Python API:
```sh
python gdb.execute('start')
python gdb.execute('next')
python result = gdb.parse_and_eval('p')  # Store variable
```
- Use ```Ctrl+C``` to exit the Python interpreter.
- Enter ```pi``` for interactive Python mode:
```sh
(gdb) pi
>>>
```
- Use ```Ctrl + D``` to exit the interactive Python mode.

- For more details, check the official GDB documentation: [Python API in GDB](https://sourceware.org/gdb/onlinedocs/gdb/Python-API.html)

### Example using Python Script

#### Example 1: Python Debugging Script (array_debug.py):
load the ```array_sum``` executable, sets a breakpoint at the ```arraySum ``` function, runs the program,
inspects and prints the array's size, elements, and the calculated sum, and then continues program execution.

```sh
import gdb

def debug_array_sum():
  gdb.execute("file array_sum")
  gdb.execute("break arraySum")
  gdb.execute("run")

  arr_size = gdb.parse_and_eval("arr.size()")
  print(f"Array size: {arr_size}")

  for i in range(arr_size):
    elem = gdb.parse_and_eval(f"arr[{i}]")
    print(f"arr[{i}] = {elem}")

  sum_val = gdb.parse_and_eval("sum")
  print(f"Sum before return: {sum_val}")

  gdb.execute("continue")

debug_array_sum()

```
How to Use:

Compile: ```g++ --tui --silent -g array_sum.cpp -o array_sum```

Run GDB with Python Script: ```gdb -x array_debug.py```

#### Example 2:  Overriding the stop() virtual method of ```gdb.Breakpoint``` (set up a custom breakpoint)

```sh
import gdb

class MyBreakpoint(gdb.Breakpoint):
  def stop(self):
    print("Breakpoint hit! Inspecting variables ...")
    gdb.execute("info locals")
    return False # Continue

MyBreakpoint("arraySum")
gdb.execute("run")
```
Compile: ```g++ --tui --silent -g array_sum.cpp -o array_sum```

Run GDB with Python Script: ```gdb -x array_debug.py```

#### Example 3:  working with std::vector
When working with ```std::vector<T>``` in GCC's libstdc++, the internal structure is not as simple as an array.
Instead, ```std::vector``` uses a dynamically allocated buffer, and its elements are managed via pointers inside a struct called ```_M_impl```
- ```_M_impl._M_start``` → Points to the first element of the vector.
- ```_M_impl._M_finish``` → Points to one past the last actual element.
- ```_M_impl._M_end_of_storage``` → Points to the end of allocated storage (capacity).

```sh
import gdb

class VectorDebugger:
    def __init__(self, executable):
        gdb.execute(f"file {executable}")
        gdb.execute("break 19") 
        gdb.execute("run")


    def inspect_vector(self, vector_name):
        vector_size = gdb.parse_and_eval(f"{vector_name}.size()")
        print(f"Vector size: {vector_size}")

        if vector_size == 0:
            print("Vector is empty.")
            return

        for i in range(int(vector_size)):
            # Access the i-th element properly
            point = gdb.parse_and_eval(f"{vector_name}._M_impl._M_start[{i}]")
			# Dereference and access member
            x = int(point["x_"])  
            y = int(point["y_"])
            print(f"Point {i}: ({x}, {y})")

    def continue_execution(self):
        gdb.execute("continue")

# Usage
debugger = VectorDebugger("vector_debug")
debugger.inspect_vector("points")
debugger.continue_execution()

```
Compile: ```g++ --tui --silent -g vector_debug.cpp -o vector_debug```

Run GDB with Python Script: ```gdb -x vector_debug.py```


## Setting Variables in GDB

GDB allows you to modify variables during debugging, which can be useful for testing different conditions in your program.

- Set a variable to a specific value:
```sh 
set var i=96
```
- Print the variable value:
```sh
print i
```

## Reverse Debugging in GDB

Reverse debugging may not necessarily reverse the execution exactly, but in some instances, GDB will at least try to do something reasonable.

- ```target record-full``` (Enable full recording)
   - ```next, next, next```
- ```reverse-next``` (Goes backward)

Other reverse execution commands:
- ```reverse-step``` ( Similar to reverse-next, but steps into function calls.)
- ```reverse-continue``` (Continues execution backward until a breakpoint or the beginning of the program.)
- ```reverse-finish```   ( If you step into a function call, reverse finish will take you back to the line after the function call.)
- ```set exec-direction reverse``` (Inverts the direction of next, step, and continue)
- ```set exec-direction forward``` ( Sets the direction of next, step, and continue back to the default (forward)).


## Debugging Multithreaded Programs:

GDB provides tools to manage the complexity of debugging multithreaded programs.  

### Displaying Threads
- `info threads`                              # Lists active threads, their IDs, states, and functions.

### Applying Commands to Threads  
- `break counter thread 1`                    # Sets a breakpoint for thread 1.  
- `thread apply all <command>`                # Applies a command to all threads
    - `thread apply all bt`                   # Displays backtraces for all threads.  
- `info threads 1 10`                         # Shows details for threads 1 and 10.  

### Controlling Threads  
- `thread <thread_id>`                        #  Switches to a specific thread
    - `thread 1`                            # Switch to thread 1. 
- `set scheduler-locking on|off|step`         # Controls thread scheduling 
    - `set scheduler-locking on`              # Freezes other threads to debug one in isolation.  
    - `set scheduler-locking off`             # Resumes normal execution of all threads. 

## Checkpoints (Snapshot Debugging)

Checkpointing allows you to create snapshots of your program's execution state at any point during a debugging session.
You can then return to these snapshots later, eliminating the need to re-run the program from the beginning.

### Commands

- `checkpoint`
   - Creates a checkpoint.
   - GDB assigns a unique checkpoint ID to each checkpoint.
- `delete checkpoint <checkpoint_id>`
   - Deletes a specific checkpoint.
- `info checkpoints`
   - Lists all existing checkpoints.
- `restart <checkpoint_id>`
   - Restores the program's execution state to the specified checkpoint.
   
## Data Display Debugger (DDD)

DDD is a graphical front-end for command-line debuggers like GDB. It provides a visual interface for:

- ### Source Code Display:
    - Shows the source code with line numbers and highlights the current execution point.
- ### Data Visualization:
    - Displays variables, data structures, and memory in a graphical format.
    - Allows you to expand and inspect complex data structures like linked lists, trees, and arrays.
- ### Interactive Debugging:
    - Provides buttons and menus for setting breakpoints, stepping through code, inspecting variables, and other GDB operations.
    - Allows you to interact with the debugger using both mouse clicks and command-line input.
### Key Features

- ### Graphical Representation of Data:
    - DDD can display data structures as graphs, trees, or tables, making it easier to understand their relationships and contents.
- ### Memory Visualization:
    - You can view memory contents in hexadecimal or other formats, which is helpful for debugging memory-related issues.
- ### Expression Evaluation:
    - DDD allows you to enter expressions and view their values graphically.
- ### Multi-Language Support:
    - DDD supports multiple programming languages, including C, C++, Fortran, and others.
	
### Installation
```sh
   sudo apt install ddd # for Debian/Ubuntu/Linux Mint
   sudo dnf install ddd # for Fedora/CentOS/RHEL
   sudo pacman -S ddd   # for Arch Linux/Manjaro
```
### Usage
To start DDD with GDB, run:
```sh
ddd <program_name>
```
## System and Library Call Tracing

`strace` and `ltrace` are very important tools for low-level debugging and performance analysis.
They provide insights into how a program interacts with the operating system and shared libraries, respectively.

### 1. System Call Tracer (`strace`)

`strace` is a powerful diagnostic, debugging, and instructional tool for Linux. It allows you to monitor the system calls made by a process.

System calls are the interface between a user-space program and the kernel.
When a program needs to perform an operation that requires kernel privileges (e.g., file I/O, network communication, memory allocation), it makes a system call.
#### Usage

```sh
strace <program_name> # run the program and print a trace of all system calls made.
strace -c <program_name> # (Counting System Calls) run the program and print a summary of system calls, including the number of calls and the time spent in each.
strace -f <program_name>  # (Following Child Processes) This will trace not only the main process but also any child processes it creates.
strace -e <syscall_name> <program_name> # (Filtering System Calls) trace only the specified system call. For example, strace -e open <program_name> will trace only open system calls.
strace -o <filename> <program_name>      # write the trace output to the specified file.
```

### 2.  Library Call Tracer (```ltrace```)

`ltrace` is a debugging tool that intercepts and records the dynamic library calls made by a process.

Shared libraries provide pre-written functions that programs can use. ltrace allows you to see which library functions a program is calling and what arguments it's passing.
#### Usage

```sh
ltrace <program_name> 
ltrace -c <program_name> 
ltrace -f <program_name>  
ltrace -e <syscall_name> <program_name> 
ltrace -o <filename> <program_name>  
```
## Valgrind for Memory Debugging  

Valgrind is a powerful dynamic analysis tool primarily used to detect memory leaks and other memory-related errors in your programs.
It works by running your program in a special simulated environment. This environment "shadows" all memory allocations and accesses, allowing Valgrind to detect issues like:

* **Memory Leaks:** Memory that was allocated using `new`, `malloc`, etc., but was never freed using `delete`, `free`, etc.
* **Invalid Memory Accesses:** Reading or writing memory that your program doesn't own (e.g., accessing an array out of bounds, using a dangling pointer).
* **Use of Uninitialized Memory:** Using variables that have not been assigned a value.
* **Double Freeing:** Attempting to free the same memory twice.
* **Mismatched `new`/`delete` and `malloc`/`free`:** Using the wrong deallocation function for allocated memory.
* **Overlapping `src` and `dst` in `memcpy`-like functions:** Potential for data corruption.

### How Valgrind Works (Simplified)

* **Instrumentation:** When you run your program under Valgrind, Valgrind doesn't execute your program directly. 
Instead, it instruments your program's machine code. This means it inserts extra code around memory-related operations.
* **Shadow Memory:** Valgrind maintains "shadow memory" that tracks the status of each byte of memory used by your program. 
This shadow memory records whether a block of memory has been allocated, freed, and whether it contains initialized data.
* **Error Detection:** As your instrumented program runs, Valgrind's instrumentation checks the shadow memory before and after every memory-related operation.
If it detects an inconsistency (e.g., writing to unallocated memory, trying to free unfreed memory), it reports an error.

### Using Valgrind
```sh
valgrind ./your_program
```
## Static Analysis with `cppcheck` and Compiler Flags

### `cppcheck`: The Static Code Analyzer

`cppcheck` is a static analysis tool for C/C++ code that examines source code without executing it.

#### What it Detects:
- Memory safety issues (null dereference, leaks, etc.)
- Uninitialized variables
- Logic errors
- Dead code
- Portability issues
- Style violations

🔗 [cppcheck.sourceforge.io](https://cppcheck.sourceforge.io/)

#### Usage:
```bash
cppcheck your_code.cpp
cppcheck src/              # entire directory
cppcheck file1.cpp file2.cpp
```

### 🛠️ Compiler Warnings: `-Wall`, `-Wextra`, `-Weffc++`

Modern compilers like `g++` include built-in static analysis features through warning flags:

- `-Wall`: Enables common and useful warnings (recommended by default)
- `-Wextra`: Enables additional, more thorough warnings
- `-Weffc++`: Warns based on *Effective C++* guidelines by Scott Meyers (covers best practices and common pitfalls)

#### 🔍 Example:
```bash
g++ -g -Weffc++ -Wall -std=c++20 *.cpp -o prog
```

### ⚠️ Limitations of Static Analysis

While powerful, static analysis tools like `cppcheck` and compiler warnings have some limitations:

- **False positives** – May flag correct code as problematic  
- **False negatives** – Might miss actual bugs  
- **No runtime context** – Can't analyze dynamic behavior like memory usage or runtime logic (use tools like **Valgrind** for those cases)

## 🐞 GDB Remote Debugging Guide

This guide shows how to set up and connect to a GDB server for remote debugging — commonly used for embedded systems or remote Linux devices.

### 1. Prerequisites

✅ **On the Target (Remote Device)**
* Operating System (e.g., Linux).
* Network connectivity (TCP/IP) or a serial connection to the host machine.
* `gdbserver` installed. Install using your distribution's package manager:
    * Debian/Ubuntu: `sudo apt-get install gdbserver`
    * Fedora/CentOS: `sudo yum install gdb-server`
* A copy of the program you want to debug. Ideally, this should be the same build as on the host machine.

✅ **On the Host**

* GDB (GNU Debugger) installed.
* A copy of the **unstripped** binary (with debug symbols) of the program you want to debug.

### 2. Setting up

✅ **On the Target (Remote Device)**

Open a terminal on your target machine and run `gdbserver` in one of the following ways:

```sh
gdbserver <host>:<port> <program> [args ...]
<host>: The IP address or hostname that gdbserver will listen on (localhost, the target's IP, or 0.0.0.0).
<port>: The TCP port number for the connection (e.g., 1234).
<program>: The path to the executable on the target machine.
[args ... ]: Optional command-line arguments for the program.
```
Make sure the binary is compiled with debugging symbols using `-g`
🔒 Ensure port is accessible or use SSH tunneling if needed.
**Example:**
```sh
gdbserver localhost:1234 /home/user/my_app arg1 arg2
```
**Attaching to a running process:**
```sh
gdbserver --attach <host>:<port> <pid>

- <host> and <port>: Same as above.
- <pid>: The process ID of the running program (find it using ps aux | grep <program_name>).
```
**Multi-process mode:**
```sh
gdbserver --multi <host>:<port>
<host> and <port>: Same as above. Connect with target extended-remote in GDB and use run or attach within the GDB session.
```
✅ **Connecting GDB on the Host Machine**

Open a terminal on your host machine and follow these steps:

**Start GDB:**
Provide the path to the local **unstripped copy** of the executable:
```sh
gdb /path/to/local/unstripped/my_app
```
**Connect to the remote target:**
Use the `target remote` command in the GDB prompt:
```sh
target remote <target_ip>:<port>

- <target_ip>: The IP address or hostname of your target machine.
- <port>: The port number used when starting gdbserver.
```
**Examples:**
- If `gdbserver` was on `localhost:1234` on the target with IP `192.168.1.100`
```sh
target remote 192.168.1.100:1234
```
- If `gdbserver` was on the same machine (localhost):
```sh
target remote localhost:1234
```
**For multi-process mode:**
Use `target extended-remote`:
```sh
target extended-remote <target_ip>:<port>
```
After connecting, use GDB commands like `run` or `attach <pid>`. You might need `set remote exec-file <path/to/program>` before running.

### Important Considerations
- **Firewalls:** Ensure firewalls on both machines allow traffic on the chosen port.
- **Network Connectivity:** Verify connectivity using `ping <target_ip>` and `telnet <target_ip> <port>`.
- **Symbol Files:** The unstripped binary on the host is crucial for source code and symbol information.
- **sysroot:** If target and host have different root filesystems, use `set sysroot <path/to/target/sysroot>` in GDB.
- **Security:** `gdbserver` has no built-in security. Use SSH tunneling for secure connections:
    - **Host:** `target remote | ssh -T <user>@<target_ip> gdbserver - <program> [args ...]` (for starting)
    - **Host:** `target remote | ssh -T <user>@<target_ip> gdbserver --attach - <pid>` (for attaching)


## GDB-Multiarch  
*A cross-architecture debugger for embedded, reverse engineering, and cross-development.*  

`gdb-multiarch` is a variant of the **GNU Debugger (GDB)** that supports debugging binaries across multiple CPU architectures (ARM, MIPS, x86, RISC-V, etc.).
It is essential for:  
- Embedded systems development  
- Cross-compiled binary debugging  
- Reverse engineering firmware/emulated binaries  

### **Installation**  

#### **Debian/Ubuntu**  
```sh
sudo apt update && sudo apt install gdb-multiarch
```
### Usage
1.  **Run gdbserver on the Target:**
    ```bash
    gdbserver <host>:<port> <program> [args ...]
    # OR
    gdbserver --attach <host>:<port> <pid>
    ```
2.  **Launch gdb-multiarch on the Host:**
    ```bash
    gdb-multiarch /path/to/local/target/binary
    ```
3. **Set the target architecture if needed**
   ```bash
   (gdb) set architecture <arch>
4. **List available architectures with:**
   Type the following and press **Tab twice**
    ```gdb
	(gdb) set architecture 
	```
5.  **Connect to the Remote Target:**
    ```bash
    (gdb) target remote <target_ip>:<port>
	# OR
	(gdb) target extended-remote <target_ip>:<port>
	```

# Secure File Transfer Using `scp`

The `scp` (Secure Copy Protocol) command allows secure file transfers between machines using SSH (Secure Shell).

## Prerequisites

### 1. Install and Enable SSH Server (on destination machine)
```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl enable ssh  # Enable on boot
sudo systemctl start ssh   # Start immediately
sudo systemctl status ssh  # Verify it's running
```
### 2. Configure Firewall (if enabled)
```bash
sudo ufw allow ssh        # Allow SSH traffic (port 22)
sudo ufw enable           # Enable firewall
sudo ufw status           # Verify SSH is allowed
```
## Basic Syntax

```bash
scp [options] <source> <destination>
```
Where:

   - `[options]`: Various options to modify scp behavior (e.g., -r for recursive directory copy)

   - `<source>`: File(s) or directory to copy (local or remote)

   - `<destination>`: Target location (local or remote)
   
**Remote Path Format**
```bash
<username>@<remote_host>:<path/to/file_or_directory>
```

### Common Use Cases
1. **Local → Remote File Transfer**
```bash
scp /path/to/local/file.txt <username>@<remote_ip>:/path/to/remote/directory/
```
2. **Remote → Local File Transfer**
```bash
scp <username>@<remote_ip>:/path/to/remote/file.txt /path/to/local/directory/
```
3. **Local → Remote Directory Transfer (Recursive)**
```bash
scp -r /path/to/local/directory <username>@<remote_ip>:/path/to/remote/destination/
```
4. **Remote → Local Directory Transfer (Recursive)**
```bash
scp -r <username>@<remote_ip>:/path/to/remote/directory /path/to/local/destination/
```

### Common Options

`-r`: Recursive directory copy

`-P <port>`: Specify non-default SSH port

`-v`: Verbose output

`-C`: Enable compression

`-q`: Quiet mode

`-i <identity_file>`: Specify private key for authentication
