# Shell Wizzard

A feature-rich, POSIX-compliant shell implementation in C, built as part of the ["Build Your Own Shell" Challenge](https://app.codecrafters.io/courses/shell/overview). This project demonstrates advanced systems programming concepts including process management, I/O redirection, terminal control, and interactive command parsing.

## Features

### Core Shell Functionality

#### **Built-in Commands**
- **`exit [code]`** - Gracefully exit the shell with optional exit code (defaults to 0)
- **`echo <args>`** - Print arguments to stdout with proper space handling between arguments
- **`type <command>`** - Display command type information (builtin vs external with full path resolution)
- **`pwd`** - Print current working directory using `getcwd()` system call
- **`cd <path>`** - Change directory with support for HOME directory (`~`) and error handling

#### **External Command Execution**
- Full PATH resolution for executable discovery using `execvp()`
- Fork-exec model for running external programs with proper process isolation
- Parent process synchronization with `waitpid()` for command completion
- Comprehensive error handling for command not found scenarios

### Advanced Features

#### **Interactive Tab Completion**
- **Smart Command Completion**: Press TAB to autocomplete commands from both built-ins and PATH executables
- **Common Prefix Detection**: Finds longest common prefix when multiple matches exist
- **Real-time Visual Feedback**: Immediate cursor repositioning and line clearing
- **PATH Integration**: Scans all directories in system PATH for executable completion candidates
- **Duplicate Prevention**: Intelligent filtering to avoid showing duplicate command suggestions

#### **Comprehensive I/O Redirection**
- **Output Redirection**: 
  - `command > file` - Truncate and write stdout to file
  - `command >> file` - Append stdout to file
- **Error Redirection**:
  - `command 2> file` - Redirect stderr (truncate mode)
  - `command 2>> file` - Redirect stderr (append mode)
- **File Descriptor Management**: 
  - Proper saving and restoration of original stdout/stderr using `dup()` and `dup2()`
  - Safe file creation with appropriate permissions (0644)
  - Complete cleanup and restoration after command execution

#### **Advanced Terminal Interface**
- **Raw Terminal Mode**: Character-by-character input processing using `termios` for responsive interaction
- **Backspace Support**: Full backspace handling with visual character erasure (`\b \b` sequence)
- **Non-blocking Input**: Immediate character processing without waiting for Enter key
- **Signal Handling**: Proper terminal restoration on exit using `atexit()` registration
- **Control Character Filtering**: Intelligent handling of control sequences and printable characters

#### **Robust Command Parsing**
- **Quote Handling**: Full support for single (`'`) and double (`"`) quotes with proper nesting
- **Escape Sequences**: Comprehensive backslash escaping for special characters (`\"`, `\$`, `\\`)
- **Whitespace Management**: Intelligent tokenization that respects quoted strings and handles multiple spaces
- **Memory Management**: Dynamic allocation with `strdup()` and comprehensive cleanup via `freeCommand()`
- **Redirection Detection**: Advanced parsing to identify and separate I/O redirection operators

## Technical Architecture

### Project Structure
```
Shell-Wizzard/
├── src/
│   ├── main.c          # REPL implementation and program entry point
│   ├── command.c       # Command parsing, execution, and redirection logic
│   ├── command.h       # Command type definitions and function prototypes
│   ├── terminal.c      # Terminal control and tab completion implementation
│   └── terminal.h      # Terminal function declarations
├── CMakeLists.txt      # Build configuration with C23 standard
├── your_program.sh     # Local development script with VCPKG integration
└── .codecrafters/      # CodeCrafters platform integration scripts
    ├── compile.sh      # Remote compilation script
    └── run.sh         # Remote execution script
```

### Core Components Architecture

#### **Command Processing Pipeline**
1. **Raw Input Capture**: Terminal reads individual characters in raw mode
2. **Interactive Processing**: Handle TAB completion, backspace, and character echo
3. **Command Tokenization**: Parse input into tokens respecting quotes and escapes
4. **Command Classification**: Determine if command is builtin or requires PATH resolution
5. **Redirection Analysis**: Parse and configure I/O redirection before execution
6. **Execution**: Execute builtin functions or fork external processes with proper file descriptor management
7. **Cleanup**: Free all allocated memory and restore file descriptors

#### **Memory Management Strategy**
- **Dynamic Allocation**: All command arguments and file paths use `malloc()` and `strdup()`
- **Comprehensive Cleanup**: `freeCommand()` function handles complete memory deallocation
- **Safe String Operations**: Proper bounds checking and null-termination throughout
- **No Memory Leaks**: Rigorous cleanup in both normal and error conditions

#### **I/O Redirection Implementation**
The `executeWithRedirection()` function provides a sophisticated wrapper that:
- **Saves Original Descriptors**: Uses `dup()` to preserve stdout/stderr
- **Configures Redirection**: Opens files with appropriate flags (`O_WRONLY`, `O_CREAT`, `O_TRUNC`/`O_APPEND`)
- **Executes Commands**: Calls the actual command function with redirected output
- **Restores State**: Uses `dup2()` to restore original file descriptors
- **Error Handling**: Comprehensive error checking with `perror()` reporting

## Building and Running

### Prerequisites
- **CMake** (version 3.13 or higher)
- **C Compiler** with C23 standard support (GCC 9+, Clang 9+)
- **POSIX-compliant system** (Linux, macOS, WSL)
- **Optional**: VCPKG for dependency management

### Build Instructions

#### Option 1: Quick Start (Recommended)
```bash
./your_program.sh
```

#### Option 2: Manual CMake Build
```bash
cmake -B build -S .
cmake --build ./build
./build/shell
```

#### Option 3: CodeCrafters Platform Build
```bash
./.codecrafters/compile.sh
./.codecrafters/run.sh
```

### Build Configuration
The project uses CMake with C23 standard (`set(CMAKE_C_STANDARD 23)`) and supports VCPKG toolchain integration for enhanced dependency management.

## Usage Examples

### Basic Shell Operations
```bash
$ echo Hello, Shell Wizzard!
Hello, Shell Wizzard!

$ pwd
/home/user/projects/shell-wizzard

$ cd /tmp
$ pwd
/tmp

$ cd ~/projects  # HOME directory support
$ pwd
/home/user/projects
```

### Command Type Information
```bash
$ type echo
echo is a shell builtin

$ type ls
ls is /usr/bin/ls

$ type gcc
gcc is /usr/bin/gcc

$ type nonexistent
nonexistent: not found
```

### Advanced I/O Redirection
```bash
# Basic output redirection
$ echo "Hello World" > output.txt
$ echo "Second line" >> output.txt

# Error redirection with different modes
$ ls /nonexistent 2> errors.log
$ cat /invalid/path 2>> errors.log

# Complex redirection scenarios
$ ls /home /invalid 2> errors.log > files.txt
$ echo "Debug info" 2>> debug.log
```

### Interactive Tab Completion
```bash
$ ec[TAB]           # Completes to "echo "
$ /usr/bin/l[TAB]   # Shows: ls, ln, less, locate, etc.
$ pw[TAB]          # Completes to "pwd "
```

### Advanced Command Line Features
```bash
# Quoted arguments with spaces
$ echo "This is a single argument with spaces"
This is a single argument with spaces

# Escape sequences
$ echo "He said \"Hello World\""
He said "Hello World"

# Backslash escaping
$ echo 'Single quotes don'\''t need escaping'
Single quotes don't need escaping

# Exit with specific codes
$ exit 0    # Success
$ exit 1    # Generic error
$ exit 42   # Custom exit code
```

## Implementation Highlights

### **POSIX Compliance & Standards**
- Follows POSIX shell standards for command execution and signal handling
- Compatible with standard shell scripting practices and conventions
- Proper exit code propagation and error reporting mechanisms
- Standard file descriptor management (0=stdin, 1=stdout, 2=stderr)

### **Performance Optimizations**
- **Efficient PATH Searching**: Caches and reuses PATH resolution results
- **Minimal Memory Allocations**: Strategic use of stack vs heap allocation
- **Fast String Operations**: Optimized parsing with single-pass tokenization
- **System Call Efficiency**: Minimal system calls during command execution

### **Robust Error Handling**
- **Comprehensive System Call Checking**: All system calls checked with proper error reporting
- **Graceful Degradation**: Shell continues operation even after command failures
- **Memory Safety**: Proper bounds checking and buffer overflow prevention
- **Resource Cleanup**: Guaranteed cleanup even in error conditions using `atexit()`

### **Extensible Architecture**
- **Modular Command System**: Easy addition of new builtin commands via enum and switch statement
- **Clean API Separation**: Clear interfaces between parsing, execution, and terminal handling
- **Plugin-Ready Design**: Function pointer system allows runtime command registration
- **Maintainable Codebase**: Well-documented functions with clear separation of concerns

## Educational Value & Learning Outcomes

This project provides hands-on experience with:

### **Systems Programming Concepts**
- **Process Management**: `fork()`, `exec()` family, `waitpid()`, process lifecycle
- **File Descriptors**: Low-level I/O, redirection, `dup()`, `dup2()`, file permissions
- **Signal Handling**: Terminal signal management, graceful shutdown procedures
- **Memory Management**: Dynamic allocation, pointer manipulation, memory leak prevention

### **Terminal Programming**
- **Raw Mode Operations**: `termios` configuration, character-level input processing
- **Escape Sequences**: ANSI escape codes for cursor control and screen manipulation
- **Interactive Features**: Real-time input processing, visual feedback systems

### **Software Engineering Practices**
- **Modular Design**: Clean separation between components, well-defined interfaces
- **Error Handling**: Defensive programming, comprehensive error checking
- **Resource Management**: RAII principles, automatic cleanup, exception safety
- **Code Documentation**: Clear function contracts, comprehensive inline documentation

## CodeCrafters Integration

This implementation successfully passes all CodeCrafters shell challenge tests including:
- Basic command execution and built-in commands
- PATH resolution and external command execution  
- I/O redirection in both truncate and append modes
- Interactive features like tab completion
- Proper error handling and exit code management

Visit [codecrafters.io](https://codecrafters.io) to try the challenge yourself.

## Contributing & Extensions

Potential enhancements for learning or contribution:
- **Pipe Support**: Implement `|` operator for command chaining
- **Background Processes**: Add `&` operator for background execution
- **Command History**: Arrow key navigation and history persistence
- **Environment Variables**: `$VAR` expansion and `export` command
- **Glob Patterns**: Wildcard matching (`*`, `?`, `[...]`)
- **Job Control**: Process suspension, resumption, job management
- **Scripting Support**: Conditional execution, loops, functions

## License & Attribution

This project is developed as part of the CodeCrafters educational platform. The implementation demonstrates professional-level C systems programming while maintaining educational clarity and comprehensive documentation.

---

**Technical Note**: This shell prioritizes both educational value and production-quality implementation practices. It serves as an excellent reference for understanding shell internals, systems programming, and advanced C development techniques.
