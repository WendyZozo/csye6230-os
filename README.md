# x86 Operating System

This is a simple 32-bit operating system designed for a CSYE6230 final project. The OS boots from a custom bootloader, enters Protected Mode, and provides a basic command-line shell for user interaction. It aims to demonstrate core operating system concepts, including interrupt handling, low-level drivers, and an in-memory simulated file system.

## Building and Running

This project was developed and tested on macOS using the Homebrew package manager.

### 1. Environment Setup

Run the following commands in your terminal to install the required dependencies:

```bash
# Install the QEMU emulator and NASM assembler
brew install nasm qemu

# Install the x86 cross-compiler toolchain (gcc, binutils, gdb)
# Note: We use x86_64-elf-* as this is the current standard package in Homebrew
brew install x86_64-elf-binutils x86_64-elf-gcc x86_64-elf-gdb
```

**Important:** Ensure that the `CC`, `LD`, and `GDB` variables in the root `Makefile` are correctly set to `x86_64-elf-gcc`, `x86_64-elf-ld`, and `x86_64-elf-gdb`, respectively.

### 2. Compilation and Execution

After navigating to the project's root directory, execute the following command:

```bash
# To compile and run the OS
make run
```

If you modify any source code, it's recommended to clean the build artifacts first:

```bash
make clean && make run
```

---

## Features & Commands

The operating system provides a basic command-line shell. The command parser is case-insensitive for commands (e.g., `list` works the same as `LIST`), but case-sensitive for arguments (e.g., filenames).

#### `LIST`
* **Purpose**: Lists all files and their sizes within the in-memory simulated file system.
* **Implementation**: Iterates through a global array of file entries and prints the name and size of each existing file.
* **Code Location**: `kernel/kernel.c` - `cmd_list()`

#### `RENAME <old_name> <new_name>`
* **Purpose**: Renames a file.
* **Implementation**: Finds `<old_name>` in the in-memory file table and updates its name to `<new_name>`. The search is case-insensitive to accommodate the keyboard driver's behavior.
* **Code Location**: `kernel/kernel.c` - `cmd_rename()`

#### `MOVE <source> <destination_path>`
* **Purpose**: Simulates moving a file to a new "directory".
* **Implementation**: Finds the `<source>` file and renames it to `<destination_path>/<source>` to simulate a path change within the in-memory file system.
* **Code Location**: `kernel/kernel.c` - `cmd_move()`

#### `ECHO <message>`
* **Purpose**: Echoes the user-provided message back to the screen.
* **Implementation**: Parses the input string and prints all text following the `ECHO` keyword.
* **Code Location**: `kernel/kernel.c` - `cmd_echo()`

#### `CLEAR`
* **Purpose**: Clears the terminal screen.
* **Implementation**: Writes space characters to the entire VGA video memory buffer (starting at `0xB8000`) and resets the cursor position.
* **Code Location**: `drivers/screen.c` - `clear_screen()`

#### `HELP` / `END`
* **Purpose**: `HELP` provides simple help information. `END` halts the CPU, effectively stopping the operating system.

---

##  Technical Implementation Details

* **Bootloader**: A custom bootloader written in x86 Assembly, responsible for switching to 32-bit Protected Mode and loading the kernel into memory.
* **Kernel**: A simple monolithic kernel design where all drivers and functionalities are integrated.
* **File System**: To simplify the implementation, an **In-Memory Simulated File System** is used. All file states are stored in RAM and are lost upon reboot.
* **Interrupt Handling**: An Interrupt Descriptor Table (IDT) is implemented to handle hardware interrupts, such as keyboard input and the PIT (Programmable Interval Timer).
* **Display Driver**: The screen is controlled by directly manipulating the VGA text mode memory buffer at the physical address `0xB8000`.

### Code Statistics
* **Total Lines of Code**: 1065

---

## Future Improvements

Potential enhancements for this project could include:
* Implementing a real, persistent file system (e.g., FAT12).
* Adding process management and basic multitasking.
* Implementing virtual memory through paging.
* Developing a basic networking stack.

## Screenshots
#### `LIST`
<img width="1280" height="697" alt="image" src="https://github.com/user-attachments/assets/ecae5d58-ee7a-4cde-b3e2-dab6c176d017" />

#### `ECHO <message>`
<img width="1280" height="699" alt="image" src="https://github.com/user-attachments/assets/fefe34d4-f9b7-4589-9cb2-b5fee3308812" />

#### `RENAME <old_name> <new_name>`
<img width="1280" height="698" alt="image" src="https://github.com/user-attachments/assets/8c1a9b39-4789-4c6c-8aaa-a4e0f5db1053" />

#### `MOVE <source> <destination_path>`
<img width="1280" height="699" alt="image" src="https://github.com/user-attachments/assets/94d833d7-f0da-47b1-845a-a74a7d452db5" />
