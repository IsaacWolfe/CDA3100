# 01/06/20  
## Syllabus Week (day 1)  
  
Professor Zhang(Zan)  
Everything is done through Canvas  
  
## Number Representations  
Base 16 integers usually are preceded by 0x..., thus 23 in base 16 is equal to 0x17  
Number types are **Decimal(10)**, **Binary(2)**, **Octal(8)**, and **Hexadecimal(16)**  
In each base 15(D) == 1111(B) == 17(O) == F(H)  
In binary each position is 2 to the power of its position so the third term is 2^3 == 8 and 8 in binary is 1000  
*Converting from Hex to Binary and back is easy*:  
* Each digit of Hex is converted to binary and added each octet in the order they appear in Hex  
* Example 4E => 4 == 0100 && E == 1110, both together is 0100 1110 == 4E  
* Works in reverse as well, 1011 1001 => 1011 == B && 1001 == 9, added together B9 == 1011 1001    
*Adding Binary is the same as normal addition*:  
39 + 57 (convert to binary)  
00100111  
00111001 (Take conversion and add row by row from the right, carry 1's)  
01100000 == 96    
  
*Subtraction in Binary is the same but one might need to borrow to subtract*  
1101  
1010  
First digit from right is 1, the second digit is 0 - 1 so the left 1 needs to be pulled to get 10 (which equals 2) 10 - 1 == 1, third digit is 0 - 0 (due to borrowing), fourth digit is 1 - 1 == 0  
The final number is *0011*  
  
Another example: 17 - 10 =?  
17 == 00010001  
10 == 00001010  
00000111  
*Borrowing must be done step by step so right of leftmost 1 becomes 10 and since it still isn't over the right spot 10 becomes 1 and the right position becomes 10*  
**Signed numbers are represented by number in binary inversed with 1 added on**  
-5 => binary  
* 5 == 0101   
* 1010 (inverse)  
* 1011(Add 1)  
Example: -6  
* -6 == 0110  
* 1001  
* 1010  
Example: -7   
* 0111  
* 1000  
* 1001  
-7 == 9  
-6 == 10   
-5 == -11  

# 01/08/20  
Two's Compliment is changing number to negative where the leading bit (if 1) means that the number is negative  
* Example 1001 is -1 and 0001 is 1  
* **Significant due to the first bit being used for positive negative means in 4 bits the max range of numbers is -8 to 7 (and not -16 to 16**  
This is why signed ints and other data types lose half there range, leading bit is used only for negative or positive, but unsigned can be double the range (the first bit range can be used  
When using first significant notation the leading number is always 1 followed by decimal of number times 2 ^ the position the number must move (refer to s-notes)  
You only need the fraction field (since leading bit is always 1) and exponent (How far to move the decimal)  
Can't use **Two's Compliment** and **First Significant Bit**  
In the computer it is ordered the *Sign* in 1 bit (31), *Exponent* in 8 bits (30-23), *Fraction* in 23 bit (22-0) **For 32 bit processors**  
**Don't use significant precision in the real world**  
Double is better to use then float do to this as double uses 64 bits and is more accurate   
**Double uses 11 bits in exponent for 1023 as bias and not 127, fraction is then 52 bits**  

# 01/13/20
**Computer Model and intro to Assembly**  
In the processors there are *registers* and *ALUs*  
Registers:  
* Are where values are stored   
* Registers are similar to a building with 32 floors each floor has one room and each room has 32 lightbulbs that can be on and off in a variety of patterns (equates to 32 registers with 32 bits at each register, not true for all registers but what we are working with)      
* Important part is the lights stay off until altered and stay that value until altered again
ALUs:  
* This is where the math is done (Arithmetic Logic Unit)  
* ALUs are like a worker going into the rooms of the Registers and remembering 2 of the rooms, they then relay the two rooms light pattern and operator to be used to the ALU which will create output that needs to be assigned to a room  
* ALUs can only do one operator at a time so more advanced math must be further broken down  
In MIPS operands for arithmetic operations must be from the registers, MIPS has thirty two 32 bit registers  
**Registers**  
$0 == $zero == Permanently 0  
$1 == $at == Assembler Temporary (reserved)  
$2, $3 == $v0, $v1 == Value returned by a subroutine  
$4-$7 == $a0-$a3 == Arguments to a subroutine  
$8-$15 == $t0-$t7 == Temporary (not preserved across a function call)  
$16-$23 == $s0-$s7 == Saved registers (preserved across a function call)  
$24, $25 == $18, $19 == Temporary  
$26, $27 == $k0, $k1 == Kernel (reserved for OS)  
$28 == $gp == Global Pointer  
$29 == $sp == Stack Pointer  
$30 == $fp == Frame Pointer  
$31 == $ra == Return Address  

f = (g+h)-(i+j) in MIPS:  
add $t0, $s1, $s2  
add $t1, $s3, $s4  
sub $s0, $t0, $t1  
  
add $s0, $s0, $s0 //can be done since the last two parameters is calling the same value and putting the addition result back into register $s0  
**Memory**  
Is space in the computer from 0 to 0xfff...ffff  
Every byte in the memory is associated with an index referred to as an *index*  
We can **read** from an address just by being given the address  
We can **insert** into an address just by providing an address and a value  
Memory is like a straight street with houses that have addresses, the worker can go to each house with the address and can change the 8 bulbs within the house, similar to registers but slower   
Data Transfer Instructions are:   
* lw (load word) from memory to register  
* sw (store word) from register to memory  
  
Assuming the address of A is in $s3 and the variable h is in $s2 A[12] = h + A[8]
```    
lw $t0, 32($s3) #Temp register $t0 gets A[8]  
add $t0, $s2, $t0 #Temp register $t0 gets h + A[8]    
sw $t0, 48($s3)
```  

# 01/15/20
**Exam question**
Suppose memory at address 1000 to 1027 is storing 1, 2... 8 (in 4 bytes). Suppose $t0 is 1000. What will be in $t0 after this instruction?    
`lw $t0, 16($t0)`  
Answer is 5  
16 / 4 bytes == 4 so 1000 to 1003 (1), 1004 to 1007 (2), 1008 to 1011 (3), 1012 to 1015 (4), 1016 is (5)  
  
Example of swapping an int array. Example is an array A[i] in $t0, which has the value 100. Swap this value with the next value at A[i + 1], which is 101  
```  
#Order isn't important other than lw followed by sw
lw $t1, 0($t0)	#This will assign 100 into t1    
lw $t2, 4($t0)	#This will assign 101 into t2  
sw $t2, 0($t0)   #This will take 101 out and assign it to t1  
sw $t1, 4($t0)   #this will take 100 out and assign it to t2  
```  
Operations like i =+ 4 and i++ can be represented with the add function  
`addi $s1, $s2, 100		#Will add the constant 100 to $s2 and assign it to $s1`  
The constant must be within a 16 bit signed integer in 2's complement form  
5 bits is used to assign registers (since 2^5 is 32 options, 1 for each register)  
The opcode (lw or sw etc.) are 6 bits  
Appears as:  
6 bits of opcode (lw, sw, etc), 5 bits of register to look into, 5 bits for register to assign to, rest for constant to increment address by  
  
# 01/29/20
# Quiz Answers  
1. 11
2. 24  
3. 0,1,2,3,8,5,6,7,8,9  
```    
	.data    
A: .word 0,1,2,3,4,5,6,7,8,9 #.word means all following values are 4 bits
	.text  
	.globl main  
main:  
	la $t0, A # Copy address of label A to address of $t0
	lw $t1, 8($t0) # Set $t1 = 2
	sll $t1, $t1, 2 # Shift $t1 to left by 2 == 8
	add $t0, $t0, $t1 # Add $t1 and $t0 == 8 at $t0
	sw $t1, 8($t0) # Store $t0 offset by 8 into $t1 making $t0 == 8 at the 4th element 
  
done: li $v0, 10  
	  syscall
```  
4. 2  
5. Below:  
```  
    beq $t0, $t1, L1 # If the two registers are equal quit
    andi $t2, $t1, 3 # Testing if last two bits are 0 == divisible by 4, if so all of $t2 == 0
    bne $t2, $0, L1 # If divisible by 4 go to end (do nothing)
    sll $t2, $t0, 1 # Multiply by 2
    add $t0, $t0, $t2 # Add the value times two plus the original value
L1:
```      

# 02/03/20
3 Types of Instructions:  
1. R-Type: all operands are registers, D = S operation T  
2. I-Type: One operand is an immediate value, T = S operation Immediate  
3. J-Type: This is for the jump action  
Shift right is divide and Shift left is multiply  
Shift right 1 == divide by 2, Shift right 2 == divide by 4, etc...  
jr $ra always end a function   
jal calls the function  

# 02/05/20
```  
checkdiffloc: 	   xor $t0, $a0, $a1  
			  	   ori $v0, $0, 0  
checkdiffloc_loop: andi $t1, $t0, 1  
				   addi $v0, $v0, 1  
				   srl $t0, $t0, 1  
				   beq $t1, $t0, checkdiffloc_loop  
				   jr $ra
```    

# 02/10/20
### Creating loops in MIPS  
```  
while (save[i] == k)  
		i += 1;  
```  
Translated closer to MIPS with if and goto:  
```  
Loop : if (save[i] != k) goto Exit;  
	   i = i + 1;  
	   goto Loop;    
Exit:
```      
As MIPS instructions (i and k equal $s3 and $s5 and starting address of array is $s6):  
```  
Loop:
	sll $t1, $s3, 2 	#t1 = s3 * 4    
	add $t1, $s6, $t1	#t1 = &save[i]  
	lw $t1, 0($t1)		#Stores value of save[i]
	bne $t1, $s5, Exit  
  	addi $s3, $s3, 1  
	j Loop
Exit:
```      
Optimized version:  
```  
	sll $t1, $s3, 2  	#t1 = s3(i) * 4
	add $t1, $t1, $s6  	#t1 = t1 + s6
	lw $t0, 0($t1)  	#t0 = t1(s6[i])
	bne $t0, $s5, Exit  #Exit if s6[i] != k
Loop:  
	addi $s3, $s3, 1  	#Add 1 to i to count occurences 
	addi $t1, $t1, 4  	#Add 4 to temp i to get address
	lw $t0, 0($t1)  	#t0 == s6 at next address
	beq $t0, $s5, Loop  #if next address == k exit
Exit:
```    
Suppose we have three arrays A, B, and C all of size 10. Now we want to set C[i] = mun(A[i], B[i]) for all 0<= i <= 9.  
Load Base Addresses:  
```  
.data  
A:	.word 12, 14, 67, 1 ,45, 90, 11, 33, 67, 19
B:	.word 90, 2, 93, 66, 8, 120, 121, 11, 33, 9
C:	.word 0, 0, 0, 0, 0, 0, 0, 0, 0, 0
.text  
.globl main  

main:  
	la $s0, A
	la $s1, B
	la $s2, C  
	li $s3, 10  		#Set number of times to loop
	  
	li $t0, 0  			#Using t0 as i
Loop:  
	sll $t4, $t0, 2  	#t4 = i * 4
	add $t5, $t4, $s0  	#t5 will have the address of A[i]
	lw $t1, 0($t5)  	#t1 has A[i]
	add $t6, $t4, $s1  	#t6 will have the address of B[i]
	lw $t2, 0($t6)  	#t2 has B[i]
	slt $t5, $t1, $t2  	#Set t5 to 1 if A[i] >= B[i]
	beq $t5, $0, L1  	#If t5 ==0, goto L1
	ori $t8, $t1, 0  	#setting t8 to be A[i]
	j L2  				#Always remember to jump in an if else
L1:  
	ori $t8, $t2, 0  	#Setting t0 to be B[i] (copies t2 to t8)
L2:  
	add $t6, $t4, $s2  	#Now t6 has the address of C[i]
	sw $t8, 0($t6)  	#Now C[i] has minimum of A[i] and B[i]
	addi $t0, $t0, 1  	#i++
	bne $t0, $a3, Loop  #go back if not 10 times yet
done:  
	li $v0, 10  
	syscall  
```  

# 02/10/20
$sp is the top of the stack pointer, to use you can use   
```  
addi $sp, $sp, -4  
sw $s0, 0($sp)  
```  

# 02/12/20
Bubble Sort- for loop (for loop (if a at j < a at j + 1( swap ) ) )  

# 02/19/20
**Midterm is only before this**
SPIM output        
Register of what is to be executed is stored in EPC at address of command to execute
interuptsPC is the start of the interrupt handler at address 0x80000180  
Last instruction is eret which sets value of PC to what is stored in EPC  
k registers are reserved for interrupts   
AT register is for psuedo instructions  
### Starting Logic and Gates  
And gate has a straight back line   
Or gate has curved back line, like an arrow  
Xor gate has two curved lines  
  
## Homework 3 Logic  
**Psuedo code**
for (msgIn[i] != 0) {      
	for (msgIn[j] != 115 (endof encrypt table) ) {  
		if (msgIn[i] == encrypt[j])  
			msgOut[i] == encrypt[j]  
		else   
			continue
	}  
}        
  
**Assembly-like**  
a0 = encrypt   
a1 = msgIn  
a2 = msgOut  
  
t0 = 0 # i  
t2 = 0  # j  
start:
addi t0, t0, 1 # increment i
la t1, t0(a1) # msgIn[i]  
beq t1 == 0, start # call start if msgIn == 0
start2:
addi t2, t2, 1 # increment 1  
la t3, t2(a0)  # encrypt[j]
beq t3, 115, start2  # call start2 if encrypt[j] == 115
bne t1, t3, start # call start of j loop if encrypt[j] != msgIn[i]  
la t8, t0(t3)  # t8 is msgOut[i]  
j start    
  
# Test Review  
I/O Device <-> Processor (ALU and Registers) <-> Memory (Data and Instructions)  
**Processor**  
* *Registers* are where data is stored, 32 bits in size  
* *ALU (Arithmetic Logic Unit)* is where the math is done, Looks at 2 registers patterns does math and stores result in another register  
**Registers**  
|Register Num | Register Name | Function |
|----|-----|-------------|  
| $0 | $zero | Permanently zero |  
| $1 | $at- Assembler Temporary (Reserved) |
| $2, $3 | $v0, $v1 | Value returned by subroutines |  
| $4-$7 | $a0-$a3 | Arguments to a subroutine |  
| $8-$15 | $t0-$t7 | Temporary (not preserved across functions) |  
| $16-$23 | $s0-$s7 | Saved registers (perserved across function call |  
| $24, $25 | $t8, $t9 | Temporary |  
| $26, $27 | $k0, $k1 | Kernel (used by OS) |  
| $28 | $gp | Global Pointer |  
| $29 | $sp | Stack Pointer |   
| $30 | $fp | Frame Pointer |  
| $31 | $ra | Return Address |  
Mips uses words (thus the use of .word at start of MIPS program) each value takes up 4 bytes  
### MIPS Instruction Encoding 
Each instruction in MIPS is exactly 32 bits  
Consists of the **R-type** **I-type** and **J-type**
**R-type encoding** commands like add
1. OP code - first 5 bits, 31-26    
2. Rs - First non-return register in command, in add it is second register provided; 4 bits from 25 to 21  
3. Rt - Third parameter (in add it is the last parameter), from 20 to 16 and is four bits long  
4. Rd - Return register to store value (first parameter in add) and is 4 bits long from 15 to 11  
5. Shamt - 5 bits long from 10 to 6  
6. Funct - 6 bits long from 5 to 0      
**I-type encoding** commands like lw and addi  
1. Op code - from 31 to 26 same as R-type  
2. Rs - First parameter, is where values are returned to store in this register  
3. Rt - from 20 to 16 and is the second and only other register to look at  
4. Immediate value - Value to be compared with that is an int of bits to increment by, from 15 to 0 and are half of whats encoded  
At the end is translated to Hex and looks like 0x00622020

## MIPS Commands      
| Command | Name | What it does | Example | Explanation | If immediate version |
| LW | Load Word | Store from memory to a register | lw $t0, 32($s3) | Load from 32 bits into s3 into register t0 | No immediate version | 
| SW | Store Word | Store from register to memory | sw $t0, 48($s3) | Store from register t0 into s3 48 bits into it | No immediate version |
| add | Add | Adds two registers | add $t0, $t1, $t2 | Adds values in t1 with t2 and stores in t0 | Has an immediate version |  
| sub | Subtract | Subtracts two registers | sub $t0, $t1, $t2 | Subtracts t1 by t2 and stores in t0 | Has an immediate version |
| and | Logical and | Compare registers bit-wise and return true if only both are true | and $t2, $t0, $t1 | Compares t0 with t1 and stores bitwise and in t2 | Has an immediate version |      
| or | Logical or | Compares two registers and returns true if either is true | or $t2, $t0, $t1 | Compares t0 and t1 and if any have a 1 then 1 is stored in t2 | Has an immediate version |  
| xor | Logical exclusive or | Compares two registers and returns true only if one register is true and the other false | xor $t2, $t0, $t1 | Compares t0 to t1 and stores 1 in t2 only if one of the registers is true and not both true | Has an immediate version |   
| nor | Returns true only if both are 0, can be used to create *not by comparing with 0* | nor $t2, $t0, $t1 | Compares t0 and t1 and only if both are 0 does t2 store 1 | No immediate version |  
| sll | Shift left logically | Shifts register by given value bitwise left (multiplies by 2s) | sll $t1, $t0, 2 | Shifts left t0 by two bits and stores in t1 (equal to multiply by 4) | No immediate version | 
| srl | Shift right logically | Shifts register by given value bitwise right (divides by 2s) | srl $t1, $t0, 2 | Shifts right t0 by two bits and stores in t1 (equal to divide by 4) | No immediate version |   
| beq | Branch if equal | Jumps to label (name of an address) if the two registers are equal, else continues | beq $t0, $t1, DIV | Compares t0 and t1 and goes to DIV if the registers are equal | No immediate version | 
