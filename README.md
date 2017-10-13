Writeup for Getit question
===================
ok ...
lets see we have a file and a hint: "**read the file to get the flag**"

lets first try to run the file:
```
root@kali:~/getit-writeup# ./getit 
root@kali:~/getit-writeup# 
```
Interesting! nothing happened ...
ok lets find more about our file

```
root@kali:~/getit-writeup# file getit 
getit: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=e389cd7a4b9272ba80f85d7eb604176f6106c61e, not stripped

```
so it's obviously a 64-bit exevutable file ... we already know that for f sake :/

hmm ... maybe they hard coded the flag in the file.
lets check that:
```
root@kali:~/getit-writeup# strings getit > strs
root@kali:~/getit-writeup# cat strs
/tmp/flaH
g.txf
[]A\A]A^A_
;*3$"
c61b68366edeb7bdce3c6820314b7498
SharifCTF{????????????????????????????????}
*******************************************
```

shit! it's harder than I thought.
but at least we have some interesting strings here:

 1. **/tmp/flag.txt**
 2. **SharifCTF{????????????????????????????????}.**

The program might create the flag during its runtime.

first lets check the **/tmp/flag.txt** file.
but there is no **flag.txt** in my **/tmp** folder :(

Enough is enough! lets get our hand dirty ...

I am using GDB as debuger to see whats going on.

```
root@kali:~/getit-writeup# gdb getit --quiet

```

lets see the main flow

```
(gdb) disass main
Dump of assembler code for function main:
   0x0000000000400756 <+0>:	push   %rbp
   0x0000000000400757 <+1>:	mov    %rsp,%rbp
   0x000000000040075a <+4>:	push   %rbx
   0x000000000040075b <+5>:	sub    $0x38,%rsp
   0x000000000040075f <+9>:	mov    %fs:0x28,%rax
   0x0000000000400768 <+18>:	mov    %rax,-0x18(%rbp)
   0x000000000040076c <+22>:	xor    %eax,%eax
   0x000000000040076e <+24>:	movl   $0x0,-0x40(%rbp)
   0x0000000000400775 <+31>:	mov    -0x40(%rbp),%eax
   0x0000000000400778 <+34>:	movslq %eax,%rbx
   0x000000000040077b <+37>:	mov    $0x6010a0,%edi
   0x0000000000400780 <+42>:	callq  0x4005e0 <strlen@plt>
   0x0000000000400785 <+47>:	cmp    %rax,%rbx
   0x0000000000400788 <+50>:	jae    0x4007c7 <main+113>
   0x000000000040078a <+52>:	mov    -0x40(%rbp),%eax
   0x000000000040078d <+55>:	lea    0xa(%rax),%edx
   0x0000000000400790 <+58>:	mov    -0x40(%rbp),%eax
   0x0000000000400793 <+61>:	cltq
   0x0000000000400795 <+63>:	movzbl 0x6010a0(%rax),%eax
   0x000000000040079c <+70>:	mov    %eax,%ecx
   0x000000000040079e <+72>:	mov    -0x40(%rbp),%eax
   0x00000000004007a1 <+75>:	and    $0x1,%eax
   0x00000000004007a4 <+78>:	test   %eax,%eax
   0x00000000004007a6 <+80>:	je     0x4007af <main+89>
   0x00000000004007a8 <+82>:	mov    $0x1,%eax
   0x00000000004007ad <+87>:	jmp    0x4007b4 <main+94>
   0x00000000004007af <+89>:	mov    $0xffffffff,%eax
   0x00000000004007b4 <+94>:	add    %ecx,%eax
   0x00000000004007b6 <+96>:	mov    %eax,%ecx
   0x00000000004007b8 <+98>:	movslq %edx,%rax
   0x00000000004007bb <+101>:	mov    %cl,0x6010e0(%rax)
   0x00000000004007c1 <+107>:	addl   $0x1,-0x40(%rbp)
   0x00000000004007c5 <+111>:	jmp    0x400775 <main+31>
   0x00000000004007c7 <+113>:	movabs $0x616c662f706d742f,%rax
   0x00000000004007d1 <+123>:	mov    %rax,-0x30(%rbp)
   0x00000000004007d5 <+127>:	movl   $0x78742e67,-0x28(%rbp)
   0x00000000004007dc <+134>:	movw   $0x74,-0x24(%rbp)
   0x00000000004007e2 <+140>:	lea    -0x30(%rbp),%rax
   0x00000000004007e6 <+144>:	mov    $0x400974,%esi
   0x00000000004007eb <+149>:	mov    %rax,%rdi
   0x00000000004007ee <+152>:	callq  0x400650 <fopen@plt>
   0x00000000004007f3 <+157>:	mov    %rax,-0x38(%rbp)
   0x00000000004007f7 <+161>:	mov    -0x38(%rbp),%rax
   0x00000000004007fb <+165>:	mov    $0x601120,%edx
   0x0000000000400800 <+170>:	mov    $0x400976,%esi
   0x0000000000400805 <+175>:	mov    %rax,%rdi
   0x0000000000400808 <+178>:	mov    $0x0,%eax
   0x000000000040080d <+183>:	callq  0x400620 <fprintf@plt>
   0x0000000000400812 <+188>:	movl   $0x0,-0x3c(%rbp)
   0x0000000000400819 <+195>:	mov    -0x3c(%rbp),%eax
   0x000000000040081c <+198>:	movslq %eax,%rbx
   0x000000000040081f <+201>:	mov    $0x6010e0,%edi
   0x0000000000400824 <+206>:	callq  0x4005e0 <strlen@plt>
   0x0000000000400829 <+211>:	cmp    %rax,%rbx
   0x000000000040082c <+214>:	jae    0x4008b5 <main+351>
   0x0000000000400832 <+220>:	mov    -0x3c(%rbp),%eax
   0x0000000000400835 <+223>:	cltq
   0x0000000000400837 <+225>:	mov    0x601160(,%rax,4),%eax
   0x000000000040083e <+232>:	movslq %eax,%rcx
   0x0000000000400841 <+235>:	mov    -0x38(%rbp),%rax
   0x0000000000400845 <+239>:	mov    $0x0,%edx
   0x000000000040084a <+244>:	mov    %rcx,%rsi
   0x000000000040084d <+247>:	mov    %rax,%rdi
   0x0000000000400850 <+250>:	callq  0x400640 <fseek@plt>
   0x0000000000400855 <+255>:	mov    -0x3c(%rbp),%eax
   0x0000000000400858 <+258>:	cltq
   0x000000000040085a <+260>:	mov    0x601160(,%rax,4),%eax
   0x0000000000400861 <+267>:	cltq
   0x0000000000400863 <+269>:	movzbl 0x6010e0(%rax),%eax
   0x000000000040086a <+276>:	movsbl %al,%eax
   0x000000000040086d <+279>:	mov    -0x38(%rbp),%rdx
   0x0000000000400871 <+283>:	mov    %rdx,%rsi
   0x0000000000400874 <+286>:	mov    %eax,%edi
   0x0000000000400876 <+288>:	callq  0x400600 <fputc@plt>
   0x000000000040087b <+293>:	mov    -0x38(%rbp),%rax
   0x000000000040087f <+297>:	mov    $0x0,%edx
   0x0000000000400884 <+302>:	mov    $0x0,%esi
   0x0000000000400889 <+307>:	mov    %rax,%rdi
   0x000000000040088c <+310>:	callq  0x400640 <fseek@plt>
   0x0000000000400891 <+315>:	mov    -0x38(%rbp),%rax
   0x0000000000400895 <+319>:	mov    $0x601120,%edx
   0x000000000040089a <+324>:	mov    $0x400976,%esi
   0x000000000040089f <+329>:	mov    %rax,%rdi
   0x00000000004008a2 <+332>:	mov    $0x0,%eax
   0x00000000004008a7 <+337>:	callq  0x400620 <fprintf@plt>
   0x00000000004008ac <+342>:	addl   $0x1,-0x3c(%rbp)
   0x00000000004008b0 <+346>:	jmpq   0x400819 <main+195>
   0x00000000004008b5 <+351>:	mov    -0x38(%rbp),%rax
   0x00000000004008b9 <+355>:	mov    %rax,%rdi
   0x00000000004008bc <+358>:	callq  0x4005d0 <fclose@plt>
   0x00000000004008c1 <+363>:	lea    -0x30(%rbp),%rax
   0x00000000004008c5 <+367>:	mov    %rax,%rdi
   0x00000000004008c8 <+370>:	callq  0x4005c0 <remove@plt>
   0x00000000004008cd <+375>:	mov    $0x0,%eax
   0x00000000004008d2 <+380>:	mov    -0x18(%rbp),%rbx
   0x00000000004008d6 <+384>:	xor    %fs:0x28,%rbx
   0x00000000004008df <+393>:	je     0x4008e6 <main+400>
   0x00000000004008e1 <+395>:	callq  0x4005f0 <__stack_chk_fail@plt>
   0x00000000004008e6 <+400>:	add    $0x38,%rsp
   0x00000000004008ea <+404>:	pop    %rbx
   0x00000000004008eb <+405>:	pop    %rbp
   0x00000000004008ec <+406>:	retq
End of assembler dump.

```
Oh, great! we have some **fopen**, **fprintf** and **fclose**
hmmm ... so maybe the program opens the **/tmp/flag.txt** and write sth on it and then close it.
lets check the idea!
we have to set breakpoint somewhere between **fopen** and **fclose**.
I will set breakpoint right before fclose at **0x00000000004008b9**

```
(gdb) b *0x00000000004008b9
Breakpoint 1 at 0x4008b9

```
now lets run it again!

```
(gdb) r
Starting program: /root/getit-writeup/getit 

Breakpoint 1, 0x00000000004008b9 in main ()
```
now lets check our file **/tmp/flag.txt**

```
root@kali:~/getit-writeup# cat /tmp/flag.txt 
*********{*********************************
root@kali:~/getit-writeup# 
```
Interesting! looks like the program opens the file and write a charachter in it and then closes the file and maybee remove it somehow.
let's continue the program in GDB to see what will happen next.

```
(gdb) c
Continuing.
[Inferior 1 (process 2268) exited normally]
(gdb) 

```
Nooo it's exited normally. so I was wrong.
I think the program first opens the file then generate just one character and then generate others and finally it closes the file and then remove it.
lets change the breakpoint location and put it after **printf**.
```
(gdb) delete 
Delete all breakpoints? (y or n) y
(gdb) b *0x00000000004008ac
Breakpoint 3 at 0x4008ac
(gdb) r
Starting program: /root/getit-writeup/getit 

Breakpoint 3, 0x00000000004008ac in main ()
(gdb) c
Continuing.

Breakpoint 3, 0x00000000004008ac in main ()

```
looks like we found our point!
to find each characters we just need to cat the **flag.txt** in our **/tmp** folder.

```
root@kali:/tmp# cat flag.txt 
******************************5************
root@kali:/tmp# cat flag.txt 
************************a******************
root@kali:/tmp# cat flag.txt 
*************************e*****************
root@kali:/tmp# cat flag.txt 
********************************1**********
``` 
finally the program exited and I found all charachters.
I put them in a file and the result looks like bellow:

```
******************************5************
************************a******************
*************************e*****************
********************************1**********
****************************************8**
************************************3******
****************************2**************
*****************7*************************
**********************************2********
***************************************5***
****************2**************************
*********************************1*********
*******************f***********************
**************************b****************
*****f*************************************
***r***************************************
*****************************d*************
***************************f***************
*******************************9***********
****i**************************************
********F**********************************
***************9***************************
*************************************c*****
******************************************}
**************5****************************
*****************************************9*
**a****************************************
***********************8*******************
*********************f*********************
S******************************************
**********b********************************
********************c**********************
*******T***********************************
***********7*******************************
*h*****************************************
*************c*****************************
******C************************************
**************************************6****
******************5************************
***********************************2*******
************0******************************
**********************a********************
*********{*********************************
```
now we have just to put them in right order to capture this flag.
a simple python script will do the job.

```
file = open('flag').readlines()
flag='*******************************************'

for line in file:
	count =0
	for c in line:
		if c !='*':
			flag = flag[:count]+ c +flag[count+1:]
		count+=1

print flag

```
Yayyy we got our flag

```
root@kali:~# python 1.py 
SharifCTF{b70c59275fcfa8aebf2d5911223c6589}

```


















