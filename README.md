[![progress-banner](https://backend.codecrafters.io/progress/shell/117804de-19a7-4cb2-9af2-9c98d0b4ca6e)](https://app.codecrafters.io/users/codecrafters-bot?r=2qF)

# Shell Wizzard

A POSIX-compliant shell implementation in C, built as part of the
["Build Your Own Shell" Challenge](https://app.codecrafters.io/courses/shell/overview).

## Features

This shell implementation includes:

- **Builtin Commands**:
  - `exit [code]` - Exit the shell with optional exit code
  - `echo <args>` - Print arguments to stdout
  - `type <command>` - Display information about command type
  - `pwd` - Print working directory
  - `cd <path>` - Change directory

- **External Command Execution**: Run any executable in your PATH

- **Tab Completion**: Press TAB to autocomplete commands from builtins and PATH executables

- **I/O Redirection**:
  - Output redirection: `command > file` (truncate) or `command >> file` (append)
  - Error redirection: `command 2> file` (truncate) or `command 2>> file` (append)

- **Advanced Input Handling**:
  - Raw terminal mode for immediate character processing
  - Backspace support
  - REPL (Read-Eval-Print Loop) interface

**Note**: If you're viewing this repo on GitHub, head over to
[codecrafters.io](https://codecrafters.io) to try the challenge.

## Building and Running

### Prerequisites

- `cmake` (version 3.13 or higher)
- C compiler with C23 standard support

### Build Instructions

```sh
cmake -B build -S .
cmake --build ./build
```

Or simply run:

```sh
./your_program.sh
```

This script will automatically build the project and run the shell.

## Project Structure

- `src/main.c` - Main entry point and REPL implementation
- `src/command.c` - Command parsing and execution logic
- `src/command.h` - Command type definitions and function headers
- `src/terminal.c` - Terminal handling and tab completion
- `src/terminal.h` - Terminal function headers
- `CMakeLists.txt` - Build configuration

## Usage Examples

```sh
$ echo Hello, World!
Hello, World!

$ pwd
/home/user/current/directory

$ cd /tmp
$ pwd
/tmp

$ type echo
echo is a shell builtin

$ type ls
ls is /usr/bin/ls

$ echo "output" > file.txt
$ echo "more output" >> file.txt

$ ls nonexistent 2> error.log
```

## Development

To work on this project as part of the CodeCrafters challenge:

1. Make your changes in the `src/` directory
2. Build and test locally using `./your_program.sh`
3. Commit your changes: `git commit -am "your message"`
4. Push to CodeCrafters: `git push origin master`

Test output will be streamed to your terminal.
