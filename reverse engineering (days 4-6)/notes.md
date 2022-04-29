##### CCTC Challenge Server Resources 
- Reverse Engineering Workflow
- x86 student guide
- memory diagram
- stack practice
- the stack

##### Programming

c+ Code -> Compiler -> Program.exe

Program.exe -> Decompiler -> Low Level Language (assembly)

# Assembly and Registers
- Assembly Instruction format: **Instruction Operand1, Operand2**
```
Instruction Operand1
Insruction Operand1, Operand2


Operand:
Hex Value | 0x3
Register | eax
Memory Address | 0x00000001

Instruction

### Data Movement/Storage
MOV
LEA

### ARITHMETIC
ADD
SUB
INC
DEC
MUL
DIV

### LOGIC
OR
AND
XOR
SHR
SHL

### STACK
PUSH
POP
CALL
RET

### CONTROL FLOW
TEST
CMP	((compare))
JMP	(unconditional jump)
JLE/JE	(conditional jump)
```

##### GySgt's Example
```
((read these top to bottom))

#data storage/movement
mov <dst>,<src>
mov eax,100	| eax=100
mov ebx,200	| eax=100,ebx=200
mov eax,200	| eax=200,ebx=200
mov eax,300	| eax=300,ebx=200
mov eax,ebx	| eax=200,ebx=200
-----------------------------------

#arithmetic
add <dst>,<src>
add eax,30	| eax=230,ebx=200
sub ebx,60	| eax=230,ebx=140
inc eax		| eax=231,ebx=140
dec ebx		| eax=231,ebx=139
-----------------------------------


sub vs cmp:	compare statement performs a comparison on operands, the result of which affects what status is set by the flags register
		subtract literally subtracts


#compare operand
cmp operand1 - operand2
cmp eax,231
cmp eax - 231		| eax=231,ebx=139
cmp 231 - 231 = 1	| flags register : zero flag : 

#flags register (also specific to this problem)
zero flag (set if 0)			= 0
sign flag (set if negative) 		= 0
carry flag (set if too small/large) 	= 1


#example jump instruction
jle 0x00000001
eax = 10
cmp eax,10
cmp 10-10 = 0

	#therefore flags register:
	zero flag (0)	:	1
	carry flag (int):	0
	sign flag (neg)	:	0
	
je 0x00000001
- the jump instruction relies on the status of the flags register
	#je	:	jump if equal
	jle	:	jump oif less than or equal
	jg	:	jump if greater than
	jge	:	jump if greater than or equal to


#stack movement ex1
last in, first out; the stack

main:
	mov rax, 16	| rax=16
	push rax	| rax=16 | stack=16
	jmp mem2	| 
mem1:
	mov rax, 0	| rax=0,r8=16
	ret (returns the value of the first return register, rax) 0
mem2:
	pop r8		| rax=16,r8=16 | stack=16	#pop value on the stack (16) into r8
	cmp rax,r8					#cinoare rax register value (16) to r8 register value
		16 - 16 = 0
		Flag Register : Zero Flag : 1
	je mem1		| rax=16,r8=16
	
------------------------------------------------------------------------------------------------------------------------------------------------------


#stack movement ex2
main:
    mov rcx, 25     //store the value 25 in rcx register	#rcx=25
    mov rbx, 62     //store the value 62 in rbx register	#rbx=62
    jmp mem1        //jumps to mem1 location			

mem1:
    sub rbx, 40     //subtract 40 from rbx			#rbx=22
    mov rsi, rbx    //copy rbx value to rsi			#rsi=22,r bx=22
    cmp rcx, rsi    //compare the values in rcx and rsi		#rcx=25,rsi=22	->	25-22=3
    jmple mem2      //jumps to mem2 location if value is less than or equal. in this case it shouldnt since the carry flag is set (jle compares it to 0); however the code also executes sequentially so mem2 would be executed anyways
    

mem2:
    mov rax, 0      //store 0 in rax
    ret             //return out of code
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

#### Examining C source code

`main():`
> indicates main entry point and start of program

`printf`
> sends formatted output to stdout

- %d = signed decimal integer
- %f = decimal floating point
- %s = strings

`fgets():`
> reads a line from the specified stream and stores it inot the string pointned ot by str.
`char *fgets(char *str, in n, FILE *stream)`

`strcmp():`
> compares str1 and str2




```


## x86_64 Assembly Common terms

- Heap:			Memory that can be allocated and deallocated
- Stack:		A contiguous section of memory used for passing arguments
- General Register	A multipurpose register that can be used by either programmer or user to store data or a memory location address
- Control Register	A processor register that changes or controls the behavior of a CPU
- Flags Register	Contains the current status of the processor

#
3Registers
 - **Registers can store values**
 - There are 16 general purpose 64 bit registers
 - 64 bit registers begin with `r`
 - 32 bit registers begin with `e
 `
- %rax	:	the first return register
- %rbp	:	the base pointer that keeps track of the base (bottom) of the stack; static
- %rsp	:	the stack pointer that points to the top of the stack; dynamic
- %rip	:	instruction pointer; points to the memory offset of the next instruction
 
## x86_64 Assembly Common Instruction Pointers

- *format this into table later*

```

```








## Hex Value
- Memory Address
- 
