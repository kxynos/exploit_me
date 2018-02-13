# exploit_me
Very vulnerable ARM application (CTF style exploitation tutorial for ARM, but portable to other platforms)
---------------------------------------------------------------------

Why:
----
Some of my friends asked me if I could do some examples
of exploitable stuff I've seen in real-world the past years for ARM/others.

So, for training purposes, I thought: Why not :)

Current vulnerabilities:
------------------------
```
Level 1: Integer overflow
Level 2: Stack overflow
Level 3: Array overflow
Level 4: Off by one
Level 5: Stack cookie
Level 6: Format string
Level 7: Heap overflow
Level 8: Structure redirection / Type confusion
Level 9: Zero pointers
Level 10: Command injection
Level 11: Path Traversal
Level 12: Basic ROP
Level 13: Use-after-free
```

Quick-start using Vagrant VM (based on Lubuntu 17.10):
------------------------------------------------------
1. Get and install VirtualBox (https://www.virtualbox.org)
2. Get and install Vagrant (https://www.vagrantup.com)
3. In any directory:
   
   Install virtual machine on host
   ```
   $ vagrant init bkerler/reversing
   ```
   
   Start virtual machine (Will take some time to download and init on first start) on host
   ```
   $ vagrant up
   ```
   
   For each session on host
   ```
   $ vagrant ssh
   ```
   
   Time to do exploit training on Guest (Password for user vagrant is "vagrant")
   ```
   vagrant@vagrant-VirtualBox:~$ cd exploit_me
   vagrant@vagrant-VirtualBox:~/exploit_me$ sudo ./disableaslr.sh
   vagrant@vagrant-VirtualBox:~/exploit_me$ qemu-arm-static -L /usr/arm-linux-gnueabi/ -g 1234 ./exploit hello &
   vagrant@vagrant-VirtualBox:~/exploit_me$ gdb-multiarch ./exploit
   ```
   
   Once you're done on Guest:
   ```
   vagrant@vagrant-VirtualBox:~$ logout
   ```
   
   To reset any changes :
   ```
   $ vagrant destroy 
   ```
   or to keep changes :
   ```
   $ vagrant halt
   ```

Usage hints:
------------

- For trying if it works :
  "qemu-arm-static -L /usr/arm-linux-gnueabi/ ./exploit"

- Example debugging session:
  ```
  $ sudo ./disableaslr.sh
  ```
  (Disable aslr, don't run if you want more fun)
  (Path dir1/dir2 needed in current exploit directory for Path Traversal vulnerability)
  
  In first terminal:
  ------------------
  ```
  $ qemu-arm-static -L /usr/arm-linux-gnueabi/ -g 1234 ./exploit [levelpassword] [options]
  ```

  In second terminal (make sure .gdbinit is in the same directory):
  -----------------------------------------------------------------
  ```
  $ gdb-multiarch ./exploit
  ```

- GDB Basics: 
  ``` 
  Use 
  "si" to step into functions or 
  "so" to step over functions, 
  "info functions" to print all functions, 
  "b [function]" (Example: "b main" to set a breakpoint and "b *0x1234" to set a breakpoint at addr 0x1234, 
  "c" to continue program, 
  "x/[dwords]x" to print offsets, for example "x/4x 0x1234" and 
  "x/[dwords]x $reg" to print register contents, for example "x/4x $sp". 
  
  Using pwndbg, you can use 
  "rop" to list rop gadgets, for example "rop --grep 'pop {r3'" to list gadgets which pop values from stack to r3. 
  See https://github.com/pwndbg/pwndbg/blob/dev/FEATURES.md for more details !
  ```

- After you've exploited correctly, you will see the password for the next level.
  So if level2 password would be "Level2":
  ```
  $ qemu-arm-static -L /usr/arm-linux-gnueabi/ ./exploit Level2
  ```

- For cheaters or people trying to understand with less instruction knowledge :
  ```
  See solution and source code in exploit.cpp
  ```

- There are more solutions possible, even with rop chains, not just my example solutions given

- There are some hints printed to console (information leak), which you normally wouldn't have, but these make things easier for beginners, that's why I added it

- For cross-compiling the code for ARM:
  ```
   $ arm-linux-gnueabi-g++ -fno-stack-protector exploit.cpp -o exploit
  ```
  (Remove -fno-stack-protector for more fun)
  Add -marm to prevent compiling in thumb mode
  

Manual installation (if you don't trust vagrant):
-------------------------------------------------
Use either "python" or "pip" for python 2.x
or "python3" or "pip3" accordingly for python 3.x (preferred)

Example:
  ```sudo pip install capstone``` for python 2.x
  or
  ```sudo pip3 install capstone``` for python 3.x
  
1. Basic install (tested with Ubuntu/LUbuntu 17.10 64Bit)
   ```
   $ sudo apt-get update
   $ sudo apt-get install gdb-multiarch
   $ sudo apt-get install python2.7 python-dev python3 python3-dev python-pip python3-pip git libssl-dev libffi-dev build-essential
   $ pip install --upgrade capstone
   $ sudo apt-get install qemu-system-arm
   $ sudo apt-get install qemu-user-static
   $ sudo apt-get install python-capstone python3-capstone
   ```
   
   If you want to crosscompile:
   
   ```
   $ sudo apt-get install libc6-armel-cross libc6-dev-armel-cross
   $ sudo apt-get install binutils-arm-linux-gnueabi
   $ sudo apt-get install libncurses5-dev
   $ sudo apt-get install gcc-arm-linux-gnueabi
   $ sudo apt-get install g++-arm-linux-gnueabi
   ```
   
2. Install latest ROPgadget:
   ```
   $ git clone https://github.com/JonathanSalwan/ROPgadget
   $ cd ROPgadget && python setup.py install && cd ..
   ```

3. Install latest pwndbg:
   ```
   $ git clone https://github.com/pwndbg/pwndbg
   $ cd pwndbg && python setup.py install && cd ..
   ```

4. Install pwntools (Note, currently Python3 version doesn't work !, thanks zachriggle):
   ```
   $ pip install --upgrade pip
   $ pip install --upgrade pwntools
   ```

  Add this to .gdbinit in home directory:
  ```
  set auto-load safe-path /
  ```

  My .gdbinit from the repo:
  ```
  set endian little
  set architecture arm
  target remote :1234
  ```

ToDo:
-----
- Will add other vulnerabilities as I see them or have spare time (like multi-thread vulnerability). But if you want to add some, I'd be happy to provide !
- Port to ARM64

Some referrals to ARM reversing beginners :
-------------------------------------------
- Learn some ARM Assembly Basics and Shellcode stuff over here : https://azeria-labs.com/
- Get Book "Beginner's Guide to Exploitation on ARM" by Billy Ellis and his YouTube tutorial videos
- Read blog "ARM exploitation for IoT" Part 1 - 3 https://quequero.org/category/security/
- Read book "A Bug Hunter's Diary" By Tobias Klein

License:
--------
MIT License
(Share, modify and use as you like, but refer to the original author !)
