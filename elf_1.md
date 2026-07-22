# ELF: From File Layout to Process Image

## 1. Introduction
Every day, we execute programs without giving much thought to what happens behind the scenes.
```c
#include <stdio.h>

int main() {
	printf("Hello, world!\n");
	return 0;
}
```
```bash
$./hello
Hello, world!
```
From the user's perspective, the program simply starts running. In reality, however, a considerable amount of work takes place before the first instruction is ever executed by the **CPU**.

An Executable stored on disk is nothing more than a file containing bytes. The processor cannot execute instructions directly from a file; it can only fetch instructions from memory. When a program is launched, the operating system (some say: terminal/bash/zsh) must interpret the executable, create a new process, construct its virtual address space, map the program into memory, and finally transfer control to the program's entry point.

On Linux, this transformation is guided by the **Executable and Linkable Format (ELF)**. An ELF file is far more than a container for machine code. It also contains metadata that describes how the operating should construct the process image -- where different parts of the program should reside in memory, which regions should be executable or writable, and where execution should begin.

Many introductions to ELF focus on the individual structures defined by the specification, such as the ELF header, section headers, and program headers. While these structures are important, understanding them in isolation often leaves an important question unanswered: **How does an ELF file becomes a running process?**

This article answers that question by following the journey of an executable from its layout on disk to the process image created by the Linux Kernel. Rather than treating ELF as a collection of data structures, we examine how its components work together during program loading, how they describe the process memory layout, and how they ultimately enable the CPU to begin executing the program.
## 2. The Journey of an Executable
## 3. The anatomy of an ELF File
## 4. The ELF Reader
## 5. Sections: Organizing the File
## 6. Segments: Organizing the Memory
## 7. Sections Vs. Segments
## 8. From ELF to Process Image
## 9. The Entry Point: Where Execution Begins
## 10. The Process Image
## 11. Conclusion
