# CHERI exercise

This is a small exercise to get started with CHERI (on RISC-V, QEMU emulation). 

## Recommended setup

While QEMU with CHERI-RISC-V should run on most Linux/Unix/Mac platforms, we recommend using Ubuntu 18.04 - if needed you can do this using a VM (for example from https://www.osboxes.org/ubuntu/#ubuntu-1804-vbox). Note that the tools take a while to build (several hours depending on CPU etc), so plan in some time to wait for the compilation to finish.

## Resources

The following resources by the CHERI team from Cambridge are useful:

 * The getting started guide, including installation instructions for the emulator etc: https://ctsrd-cheri.github.io/cheri-exercises/cover/index.html 
 * The `cheribuild` tool: https://github.com/CTSRD-CHERI/cheribuild.git
 * How to copy files in/out of QEMU to the host is documented here: https://github.com/CTSRD-CHERI/cheri-exercises/pull/26
   Essentially, you can simply use `mount_smbfs -I 10.0.2.4 -N //10.0.2.4/source_root /mnt` in the QEMU guest, which will mount the CHERI base directory of the host on `/mnt`.

Also, if you ever need to exit QEMU: press `Ctrl-a` then release and press `x`   

## Task

We will use a (slightly modified) exercise from https://github.com/CTSRD-CHERI/cheri-exercises/

 * Fork this repository here (*not* the CHERI exercise one) - we expect you to add your solutions in this README.md where it says *INSERT SOLUTION HERE*. Please make sure you do reasonable commits and commit messages. You can also use other features of Github e.g. issues.
 
 * Assuming that you have installed CHERI-RISC-V in `~/cheri`, make sure your forked repo is cloned to `~/cheri/riscv-exercise`
 
 * Compile `buffer-overflow.c` to a RISC-V binary `buffer-overflow-hybrid` in hybrid capability mode (`riscv64-hybrid`). You can use the `ccc` script from `task/tools` (see the exercise docs for details) for that. What is the full commandline for compilation? 
 
 ```
 We type: ./tools/ccc riscv64-hybrid ./buffer-overflow.c -o buffer-overflow-hybrid

the command output is:

Running: /home/saman/cheri/output/sdk/bin/clang -target riscv64-unknown-freebsd -march=rv64gcxcheri -mabi=lp64d -mno-relax --sysroot=/home/saman/cheri/output/sdk/sysroot-riscv64-purecap -g -O2 -fuse-ld=lld -Wall -Wcheri ./buffer-overflow.c -o buffer-overflow-hybrid

 ```
 
 * There is a security flaw in `buffer-overflow.c`. Briefly explain what the flaw is: 
 
 ```
 This code suffer from "buffer-overflow" attack. In line 64 (strcpy(buffer, argv[1]);) copy argv[1] from input to the 'bufer' with 16 bit length. If the argv[1] length is more than 16 bit then, the overflow would be occured. So, the aditional data would be written in the another location of the memory space and it is possible to change in 'c' var content.
 ```
 
 * Start CHERI-RISC-V in QEMU, copy `buffer-overflow-hybrid` to the QEMU guest, and run it with a commandline argument that triggers the mentioned security flaw to overwrite the variable `c` with an attacker-controlled value. Give all the commands you have to run (assuming CHERI is in `~/cheri` and cheribuild in `~/cheribuild`):
 
  ```
$ ./cheribuild.py cheribsd-riscv64
$ cd ../riscv-exercise-main/task
$ ./tools/ccc morello-hybrid -o buffer-overflow-hybrid
$ cd ../../cheribuild
$ ./heribuild.py cheribsd-riscv64
Enter user: root
$ ./buffer-overflow-hybrid 1234567890123456789
  ```
  
 * Now, compile the same program in pure capability mode (`riscv64-purecap`) to `buffer-overflow-purecap`. What happens when you run this program in QEMU with the same input that triggered the flaw in `buffer-overflow-hybrid`? Explain why this happens!

 ```
 In `riscv64-purecap` mode, the compiler prevent to 'buffer-overflow' becomes occured. So, the c variable remain its vale (c=c)
 ```
