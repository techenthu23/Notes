# Ways to analyze binary files on Linux

- [Ways to analyze binary files on Linux](#ways-to-analyze-binary-files-on-linux)
  - [file](#file)
  - [ldd](#ldd)
  - [ltrace](#ltrace)
  - [Hexdump](#hexdump)
  - [strings](#strings)
  - [readelf](#readelf)
  - [objdump](#objdump)
  - [strace](#strace)
  - [nm](#nm)
  - [gdb](#gdb)
  - [Reference](#reference)

## file

The file command will help you identify the exact file type that you are dealing with. Is it a binary file, a library file, an ASCII text file, a video file, a picture file, a PDF, a data file, etc. This will be your starting point for binary analysis

```
$ file /bin/ls
/bin/ls: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=94943a89d17e9d373b2794dcb1f7e38c95b66c86, stripped
$
$ file /etc/passwd
/etc/passwd: ASCII text
$ 
```

## ldd

What it does: Print shared object dependencies.

If you have already used the file command above on an executable binary, you can't miss the "dynamically linked" message in the output. What does it mean?

When software is being developed, we try not to reinvent the wheel. There are a set of common tasks that most software programs require, like printing output or reading from standard in, or opening files, etc. All of these common tasks are abstracted away in a set of common functions that everybody can then use instead of writing their own variants. These common functions are put in a library called libc or glibc.

How does one find which libraries the executable is dependent on? That’s where ldd command comes into the picture. Running it against a dynamically linked binary shows all its dependent libraries and their paths.

```
$ ldd /bin/ls
        linux-vdso.so.1 =>  (0x00007ffef5ba1000)
        libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fea9f854000)
        libcap.so.2 => /lib64/libcap.so.2 (0x00007fea9f64f000)
        libacl.so.1 => /lib64/libacl.so.1 (0x00007fea9f446000)
        libc.so.6 => /lib64/libc.so.6 (0x00007fea9f079000)
        libpcre.so.1 => /lib64/libpcre.so.1 (0x00007fea9ee17000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00007fea9ec13000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fea9fa7b000)
        libattr.so.1 => /lib64/libattr.so.1 (0x00007fea9ea0e000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fea9e7f2000)
$ 
```

## ltrace

What it does: A library call tracer.

We now know how to find the libraries an executable program is dependent on using the ldd command. However, a library can contain hundreds of functions. Out of those hundreds, which are the actual functions being used by our binary?

The ltrace command displays all the functions that are being called at run time from the library. In the below example, you can see the function names being called, along with the arguments being passed to that function. You can also see what was returned by those functions on the far right side of the output.

```
$ ltrace ls
__libc_start_main(0x4028c0, 1, 0x7ffd94023b88, 0x412950 <unfinished ...>
strrchr("ls", '/')                                                                  = nil
setlocale(LC_ALL, "")                                                               = "en_US.UTF-8"
bindtextdomain("coreutils", "/usr/share/locale")                                    = "/usr/share/locale"
textdomain("coreutils")                                                             = "coreutils"
__cxa_atexit(0x40a930, 0, 0, 0x736c6974756572)                                      = 0
isatty(1)                                                                           = 1
getenv("QUOTING_STYLE")                                                             = nil
getenv("COLUMNS")                                                                   = nil
ioctl(1, 21523, 0x7ffd94023a50)                                                     = 0
<< snip >>
fflush(0x7ff7baae61c0)                                                              = 0
fclose(0x7ff7baae61c0)                                                              = 0
+++ exited (status 0) +++
$
```

## Hexdump

What it does: Display file contents in ASCII, decimal, hexadecimal, or octal.

Often, it happens that you open a file with an application that doesn’t know what to do with that file. Try opening an executable file or a video file using vim; all you will see is gibberish thrown on the screen.

Opening unknown files in Hexdump helps you see what exactly the file contains. You can also choose to see the ASCII representation of the data present in the file using some command-line options. This might help give you some clues to what kind of file it is.

```
$ hexdump -C /bin/ls | head
00000000  7f 45 4c 46 02 01 01 00  00 00 00 00 00 00 00 00  |.ELF............|
00000010  02 00 3e 00 01 00 00 00  d4 42 40 00 00 00 00 00  |..>......B@.....|
00000020  40 00 00 00 00 00 00 00  f0 c3 01 00 00 00 00 00  |@...............|
00000030  00 00 00 00 40 00 38 00  09 00 40 00 1f 00 1e 00  |....@.8...@.....|
00000040  06 00 00 00 05 00 00 00  40 00 00 00 00 00 00 00  |........@.......|
00000050  40 00 40 00 00 00 00 00  40 00 40 00 00 00 00 00  |@.@.....@.@.....|
00000060  f8 01 00 00 00 00 00 00  f8 01 00 00 00 00 00 00  |................|
00000070  08 00 00 00 00 00 00 00  03 00 00 00 04 00 00 00  |................|
00000080  38 02 00 00 00 00 00 00  38 02 40 00 00 00 00 00  |8.......8.@.....|
00000090  38 02 40 00 00 00 00 00  1c 00 00 00 00 00 00 00  |8.@.............|
$
```

Extracting familiar strings

Just because the default data dump seems meaningless, that doesn’t mean it’s devoid of valuable information. You can translate this output or at least the parts that actually translate, to a more familiar character set with the --canonical option:

```
hexdump --canonical foo.png 
```

In the right column, you see the same data that’s on the left but presented as ASCII. If you look carefully, you can pick out some useful information, such as the file’s format (PNG) and—toward the bottom—the date and time the file was created and last modified. The dots represent symbols that aren’t present in the ASCII character set, which is to be expected because binary formats aren’t restricted to mundane letters and numbers.

The file command knows from the first 8 bytes what this file is. The libpng specification alerts programmers what to look for. You can see that within the first 8 bytes of this image file, specifically, is the string PNG. That fact is significant because it reveals how the file command knows what kind of file to report.

You can also control how many bytes hexdump displays, which is useful with files larger than one pixel:

```
$ hexdump --length 8 pixel.png
0000000 5089 474e 0a0d 0a1a
0000008
```

You don’t have to limit hexdump to PNG or graphic files. You can run hexdump against binaries you run on a daily basis as well, such as ls, rsync, or any binary format you want to inspect.

<https://opensource.com/article/19/8/dig-binary-files-hexdump>

## strings

What it does: Print the strings of printable characters in files.

If Hexdump seems a bit like overkill for your use case and you are simply looking for printable characters within a binary, you can use the strings command.

When software is being developed, a variety of text/ASCII messages are added to it, like printing info messages, debugging info, help messages, errors, and so on. Provided all this information is present in the binary, it will be dumped to screen using strings.

```
strings /bin/ls
```

## readelf

What it does: Display information about ELF files.

ELF (Executable and Linkable File Format) is the dominant file format for executable or binaries, not just on Linux but a variety of UNIX systems as well. If you have utilized tools like file command, which tells you that the file is in ELF format, the next logical step will be to use the readelf command and its various options to analyze the file further.

Having a reference of the actual ELF specification handy when using readelf can be very useful. You can find the specification [here](http://www.skyfree.org/linux/references/ELF_Format.pdf).

```
$ readelf -h /bin/ls
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x4042d4
  Start of program headers:          64 (bytes into file)
  Start of section headers:          115696 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         31
  Section header string table index: 30
$ 
```

## objdump

What it does: Display information from an object file.

Binaries are created when you write source code which gets compiled using a tool called, unsurprisingly, a compiler. This compiler generates machine language instructions equivalent to the source code, which can then be executed by the CPU to perform a given task. This machine language code can be interpreted via mnemonics called an assembly language. An assembly language is a set of instructions that help you understand the operations being performed by the program and ultimately being executed on the CPU.

objdump utility reads the binary or executable file and dumps the assembly language instructions on the screen. Knowledge of assembly is critical to understand the output of the objdump command.

Remember: assembly language is architecture-specific.

```
$ objdump -d /bin/ls | head

/bin/ls:     file format elf64-x86-64


Disassembly of section .init:

0000000000402150 <_init@@Base>:
  402150:       48 83 ec 08             sub    $0x8,%rsp
  402154:       48 8b 05 6d 8e 21 00    mov    0x218e6d(%rip),%rax        # 61afc8 <__gmon_start__>
  40215b:       48 85 c0                test   %rax,%rax
$ 
```

## strace

What it does: Trace system calls and signals.

If you have used ltrace, mentioned earlier, think of strace being similar. The only difference is that, instead of calling a library, the strace utility traces system calls. System calls are how you interface with the kernel to get work done.

To give an example, if you want to print something to the screen, you will use the printf or puts function from the standard library libc; however, under the hood, ultimately, a system call named write will be made to actually print something to the screen.

```
strace -s 2048 /bin/ls | while read line; do printf "%b\n" "$line" ; done
```

```
 FORMAT controls the output as in C printf. Interpreted sequences are:

\"    double quote 
\\    backslash 
\a    alert (BEL) 
\b    backspace 
\c    produce no further output 
\e    escape 
\f    form feed 
\n    new line 
\r    carriage return 
\t    horizontal tab 
\v    vertical tab 
\NNN        byte with octal value NNN (1 to 3 digits) 
\xHH        byte with hexadecimal value HH (1 to 2 digits) 
\uHHHH      Unicode (ISO/IEC 10646) character with hex value HHHH (4 digits) 
\UHHHHHHHH  Unicode character with hex value HHHHHHHH (8 digits) 
%%    a single % 
%b    ARGUMENT as a string with '\' escapes interpreted, except that octal escapes are of the form \0 or \0NNN 
%q    ARGUMENT is printed in a format that can be reused as shell input, escaping non-printable characters with the proposed POSIX $'' syntax. 
```

## nm

What it does: List symbols from object files.

If you are working with a binary that is not stripped, the nm command will provide you with the valuable information that was embedded in the binary during compilation. nm can help you identify variables and functions from the binary. You can imagine how useful this would be if you don't have access to the source code of the binary being analyzed.

To showcase nm, we will quickly write a small program and compile it with the -g option, and we will also see that the binary is not stripped by using the file command.

```
$ cat hello.c
#include <stdio.h>

int main() {
    printf("Hello world!");
    return 0;
}
$
$ gcc -g hello.c -o hello
$
$ file hello
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=3de46c8efb98bce4ad525d3328121568ba3d8a5d, not stripped
$
$ ./hello
Hello world!$
$
$ nm hello | tail
0000000000600e20 d __JCR_END__
0000000000600e20 d __JCR_LIST__
00000000004005b0 T __libc_csu_fini
0000000000400540 T __libc_csu_init
                 U __libc_start_main@@GLIBC_2.2.5
000000000040051d T main
                 U printf@@GLIBC_2.2.5
0000000000400490 t register_tm_clones
0000000000400430 T _start
0000000000601030 D __TMC_END__
$ 
```

## gdb

What it does: The GNU debugger.

Well, not everything in the binary can be statically analyzed. We did execute some commands which ran the binary, like ltrace and strace; however, software consists of a variety of conditions that could lead to various alternate paths being executed.

The only way to analyze these paths is at run time by having the ability to stop or pause the program at any given location and being able to analyze information and then move further down.

That is where debuggers come into the picture, and on Linux, gdb is the defacto debugger. It helps you load a program, set breakpoints at specific places, analyze memory and CPU register, and do much more. It complements the other tools mentioned above and allows you to do much more runtime analysis.

One thing to notice is, once you load a program using gdb, you will be presented with its own (gdb) prompt. All further commands will be run in this gdb command prompt until you exit.

We will use the "hello" program that we compiled earlier and use gdb to see how it works.

```
$ gdb -q ./hello
Reading symbols from /home/flash/hello...done.
(gdb) break main
Breakpoint 1 at 0x400521: file hello.c, line 4.
(gdb) info break
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x0000000000400521 in main at hello.c:4
(gdb) run
Starting program: /home/flash/./hello

Breakpoint 1, main () at hello.c:4
4           printf("Hello world!");
Missing separate debuginfos, use: debuginfo-install glibc-2.17-260.el7_6.6.x86_64
(gdb) bt
#0  main () at hello.c:4
(gdb) c
Continuing.
Hello world![Inferior 1 (process 29620) exited normally]
(gdb) q
$ 
```


## Reference

[Red Hat Developer Toolset Debugging Tools](https://access.redhat.com/documentation/en-us/red_hat_developer_toolset/11/html/user_guide/part-debugging_tools)
