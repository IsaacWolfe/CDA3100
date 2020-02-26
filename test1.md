# Test Review    
[MIPS Commands](#mips-commands)
### Single Precesion   
First bit - 1 
Following 8 bits [1-9]- Following 8 bits are the bias with default single precision being 127 so if the decimal doesn't move bias is 128 and if it moves forward one bias is 129 in binary  
Last bits up until 32 are the number converted to binary with the decimal moved and the leading 1 truncated
### Architecture
I/O Device <-> Processor (ALU and Registers) <-> Memory (Data and Instructions)    
**Processor**  
* *Registers* are where data is stored, 32 bits in size  
* *ALU (Arithmetic Logic Unit)* is where the math is done, Looks at 2 registers patterns does math and stores result in another register  
**Registers**  

| Register Num | Register Name | Function |  
| ------------ | ------------- | -------- |  
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
1. OP code- first 5 bits, 31-26    
2. Rs- First non-return register in command, in add it is second register provided; 4 bits from 25 to 21  
3. Rt- Third parameter (in add it is the last parameter), from 20 to 16 and is four bits long  
4. Rd- Return register to store value (first parameter in add) and is 4 bits long from 15 to 11  
5. Shamt- 5 bits long from 10 to 6  
6. Funct- 6 bits long from 5 to 0      
**I-type encoding** commands like lw and addi  
1. Op code- from 31 to 26 same as R-type  
2. Rs- First parameter, is where values are returned to store in this register  
3. Rt- from 20 to 16 and is the second and only other register to look at  
4. Immediate value- Value to be compared with that is an int of bits to increment by, from 15 to 0 and are half of whats encoded    
**J-type encoding** commands like j  
1. Op code- 31 to 26 bits  
2. Jump address- address of register to jump to, from 25 to 0  
* **MIPS only encodes address in multiples of 4**  
* BNE is similar but encoded like:   
1. Op code- 31 to 26 bits  
2. Rs- First parameter from 25 to 21  
3. Rt- Second parameter from 20 to 16  
4. Immediate value- from 15 to 0, where the label/offset is encoded to jump to
* Due to the amount of encoding done with commands like bne the address is not stored but rather the offset with respect to the address of bne
At the end is translated to Hex and looks like 0x00622020    

MIPS code always starts similar to   
```  
.text   
.globl main  
main:  
```  
And ends with:
```  
li $v0, 10  
syscall
```      
PC register always stores address of next command to execute    
Data segment- started by .data and has lines following similar to array declarations  
`A: .word 2, 2, 9, 1, 5, 8, 4`  
A becomes a label for starting address which stores 2, all following values are stored 4 bytes after each other sequentially (array)  
### Pseudo Instructions   
Not real instruction implemented by hardware but rather implemented to make code more readable  
Usually mapped to other real instructions, **the mapping is one-to-one**  
Example: `li $t0, 4` translates to `ori $t0, $t0, 4`, both make t0 equal to 4  
**$1** translates to $at register and should only be used for pseudo instructions   
Example:   
`li $t0, 90000` becomes -> `lui $t1, 1` which is load upper 16 bits and `ori $t0, $1, 24464`  
When using functions one needs to use jal to **jump and link** the address and it's return address and at the end of the function one needs jr $ra to jump back to the line that follows the jal  
When using functions registers $a0-$a3 are the four argument registers used to pass arguments, $v0 and $v1 are value registers used to return what is stored in them  
Also in functions $t0-$t9 can be freely altered but $s0-$s7 must not change ($s0-$s7 are similar to const variables)  
If we must use the $s0-$s7 registers in a function there original value must be stored somewhere safe so the altered register can be returned before jr $ra is called  
In order to store them we save them in the stack  
## Stack  
The stack starts at *0x7ffffffc* and grows downwards (towards 0x00000000)  
Stack is *FILO* (First in last out, similar to stack of papers)  
$sp is the **stack pointer** and points to the top of the stack, so when moving it it is done in two commands:  
1. `addi $sp, $sp, -4` moving the sp down four bits  
2. `sw $s0, 0($sp)` stores value at the sp into the $s0 register  
When done with the data the reverse must be used:  
1. `lw $s0, 0($sp)`  
2. `addi $sp, $sp, 4`  
If dealing with nested functions since the location of $ra will be changed when hitting the jal in the first function $ra must be saved on the stack (by the above) due to it being similar to the $a0-$a7 registers and can't be changed within function without messing up return calls
### Syscall  
Command used to read the interger value stored in $v0 and can be used anywhere in code but `li $v0, 10` followed by syscall is to terminate program  
Common syscall values:  
1. **Print integer stored in $a0**  
2. Print float stored in $f12  
3. Print double stored in $f12  
4. **Print string stored in $a0 (string must be terminated by null character)**  
5. **Read integer with $v0 containing character read**    
6. **Read double with $v0 conatining character read, value stored in $f0**
8. Read string with $a0 being address of input buffer and $a1 maximum characters to read  
10. **Terminate program**  
11. Print character in $a0  
12. Read character with $v0 containing character read
17. Exit with $a0 being exit code returned  
**$fp and $gp**  
* $fp is to the frame pointer, this is a fixed variable once a function is called and will point to the top of the stack and will not increment  
* $gp is to the global pointer, a reference to access the static data
* $f0-$f12 are pseudo registers used to store floating point numbers  
To use these registers:  
* `l.s $f0, val` loads val into $f0  
* `s.s $f0, val` stores val into $f0  
* `li.s $f0, 0.5` loads immediate into $f0  
To print and use these registers use:   
* Print  
	* `li $v0, 2`  
	* `li.s $f12, 0.5`  
	* syscall  
* Read  
	* `li $v0, 6`  
	* `syscall` (read will be stored in $f0)  
For raising to the power use (x + n/x) / 2 with n being power and x being base    
### MIPS Interrupts   
Everything above start of stack (0x7ffffff) is used by the system, meaning special operating system functions, I/O registers mapped to memory, Kernel data, etc...  
SPIM allows reading from keyboard, similar to reading from the actual I/O register  
Program to print input until 'q' is entered:  
```  
	.text  
	.globl main  
main:  
	addi $s0, $0, 113 #q key  
	lui $t0, 0xFFFF # $t0 = 0xFFFF0000  
waitloop:  
	lw $t1, 0($t0)  
	andi $t1, $t1, 0x0001  
	beq $t1, $zero, waitloop   
	  
	lw $a0, 4($t0)  
	  
	beq $a0, $s0, done  
	  
	li $v0, 1  
	syscall  
	  
	li $v0, 4  
	la $a0, new_line  
	syscall  
	  
	j waitloop  
done:   
	li $v0, 10 #exit  
	syscall  
	  
	.data  
new_line: .asciiz "\n"
```    
*SPIM Output*  
Spim has two memory locations for output  
1. 0xffff0008  
	* Transmitter control  
	* bit ready: 0  
2. 0xffff000c  
	* bit0-7: data byte    
To print you:  
1. Check if ready bit is 1  
2. Write to the data. Ready bit will reset to 0 and will change back to 1 after data is transmitted  
With an external interrupt following will occur:  
* The address of the instruction that is about to be executed is saved into a special register called EPC  
* PC is set to be 0x80000180 (this is the starting address of the interrupt handler, PC is program counter and keeps track of next command location to be executed)  
* Last, ERET is then executed setting PC to the value stored in EPC    
Can $t0 be used it interrupt?  
No, because the function is not expecting the interrupt to change and $t0 can't be viewed to see if a change occurred and thus will use the wrong value  
Interrupts should be short and mostly used to set flags (which can be registers but are accessed quicker)
### R-type, I-type, and J-type    
**R-types**:  
* Consists of:  
	* Op code(31 to 26)- Machine code used to identify command to be used in 6 bits and different commands can have the same op code differentiated by the func code  
	* Rs(25 to 21)- Register source (first parameter being used by command)  
	* Rt(20 to 16)- Register target (second parameter being used by command)  
	* Rd(15 to 11)- Register destination (what register to store the return of the command in)  
	* Shift/SHAMT(10 to 6)- How much to shift the rs by  
	* Func(5 to 0)- for functions with the same Op code this is used to distinguish the command    
* Includes FR instructions which are used only for floating point numbers  
* All these functions have the same op code of 0x00 and use func codes to distinguish them
**I-types**:  
* Consists of:  
	* Op code(31 to 26)- Same as above  
	* Rt(25 to 21)- Register target  
	* Rs(20 to 16)- Register source  
	* IMM(15 to 0)- 16 bits for the immediate value as a signed int (any number exceeding 16 bits cannot be used)    
* Most are done in order of **op, rt, imm(rs)** but commands like beq and bne execute by **op, rs, rt, imm**    
* Includes FI instructions which are used only for floating point numbers
**J-types**:  
* Consists of:  
	* Op code(31 to 26)- Same as above  
	* Label/pseudo address( 25 to 0)- in order to fit the address (which is 32 bits) the 2 least significant and 4 most significant bits are removed 

# MIPS Commands        

| Command | Name | What it does | Example | Explanation | If immediate version | Op Code/Func codes |    
| ------- | ---- | ------------ | ------- | ----------- | -------------------- | ------------------ |
| lw | Load Word | Store from memory to a register | lw $t0, 32($s3) | Load from 32 bits into s3 into register t0 | No immediate version built in (li pseudo instruction) | Ox23 |    
| li | Load immediate | Store immediate value to register | li $t0, 4 | Store the value 4 into the register t0 | No immediate version, **pseudo instruction** |  
| la | Load address | Store address into a register | la $t0, L1 | Store address of L1 into register t0 | No immediate version |
| sw | Store Word | Store from register to memory | sw $t0, 48($s3) | Store from register t0 into s3 48 bits into it | No immediate version | 0x2b |
| add | Add | Adds two registers | add $t0, $t1, $t2 | Adds values in t1 with t2 and stores in t0 | Has an immediate version | 0x00/0x20 |  
| sub | Subtract | Subtracts two registers | sub $t0, $t1, $t2 | Subtracts t1 by t2 and stores in t0 | Has an immediate version | 0x00/0x22 |
| and | Logical and | Compare registers bit-wise and return true if only both are true | and $t2, $t0, $t1 | Compares t0 with t1 and stores bitwise and in t2 | Has an immediate version | 0x00/0x24 |  
| or | Logical or | Compares two registers and returns true if either is true | or $t2, $t0, $t1 | Compares t0 and t1 and if any have a 1 then 1 is stored in t2 | Has an immediate version | 0x00/0x25 |  
| xor | Logical exclusive or | Compares two registers and returns true only if one register is true and the other false | xor $t2, $t0, $t1 | Compares t0 to t1 and stores 1 in t2 only if one of the registers is true and not both true | Has an immediate version | 0x00/0x25 |  
| nor | Returns true only if both are 0, can be used to create *not by comparing with 0* | nor $t2, $t0, $t1 | Compares t0 and t1 and only if both are 0 does t2 store 1 | No immediate version | 0x00/0x27 |  
| sll | Shift left logically | Shifts register by given value bitwise left (multiplies by 2s) | sll $t1, $t0, 2 | Shifts left t0 by two bits and stores in t1 (equal to multiply by 4) | No immediate version | 0x00/0x00 |  
| srl | Shift right logically | Shifts register by given value bitwise right (divides by 2s) | srl $t1, $t0, 2 | Shifts right t0 by two bits and stores in t1 (equal to divide by 4) | No immediate version | 0x00/0x02 |   
| beq | Branch if equal | Jumps to label (name of an address) if the two registers are equal, else continues | beq $t0, $t1, DIV | Compares t0 and t1 and goes to DIV if the registers are equal | No immediate version | 0x04 |   
| bne | Branch if not equal | Jumps to label if two registers are not equal, else continues | bne $t0, $t1, DIV | Compares t0 and t1 and jumps to DIV if the registers are not equal | No immediate version | 0x05 |   
| j | Jump to label | Jumps to label provided no matter what | j L1 | jumps to label L1 | No immediate version | 0x02 |   
| slt | See if register is less then | Sets register to 1 if register 1 is less than register 2, else sets register to 0 | slt $t2, $t0, $t1 | Sets t2 to 1 if t0 is less than t1 else t0 is 0 | Has an immediate version | 0x00/0x2a |   
| sgt | See if register is greater then | Sets register to 1 if register 1 is greater than register 2, else sets register to 0 | sgt $t2, $t0, $t1 | Sets t2 to 1 if t0 is greater than t1 else t0 is 0 | Has an immediate version |    
| blt | See if register is less then and branch | Jumps to label if register 1 is less than register 2 | slt $t0, $t1, L1 | Jumps to L1 if t0 is less then t1|  No immediate version, **pseudo instruction** |    
| bgt | See if register is greater then and branch | Jumps to label if register 1 is greater than register 2 | bgt $t0, $t1, L1 | Jumps to L1 if t0 is greater than t1 | No immediate version, **pseudo instruction** |   
| mul | Multiply | Multiples two registers and stores them in first argument | mul $t2, $t0, $t1 | Multiplies t0 by t1 and stores in t2 | Has an immediate version |  
| div | Divide | Divides register by following register and stores in first argument | div $t2, $t0, $t1 | Divides t0 by t1 and stores result in t2 | Has an immediate version, **pseudo instruction** |  
| mult | Multiply into hi and lo | Multiply and store result in *Hi* (first half of number since multiplying can make number exceed one register) and *Lo* (second half of number) | mult $t0, $t1 | multiply t0 by t1 and store high word in hi and low word in lo | **pseudo instruction** | 0x00/0x18 |  
| div | Divide into hi and lo | Divide and store result in *Hi* and *Lo* (Result is in hi while remainder, if there is one, is stored in lo) | div $t0, $t1 | divide t0 by t1 and store result in hi and remainder in lo | **pseudo instruction** | 0x00/0x1a |    
| mfhi | Move from hi | Move from high register | mfhi $t0 | Move from hi register (wher mult and div are stored) and store in $t0 | No immediate version | 0x00/0x10 |  
| mflo | Move from lo | Move from lo register | mflo $t0 | Move from lo register (where mult and div are stored) and store in $t0 | No immediate version | 0x00/0x12 |  
| jal | Jump and link | Jump to label and link line after jal to return | jal L1 | Jumps to label L1 and links the line after jal to register $ra (return address) | No immediate version | 0x03 |   
| jr | Jump to return address | Jump to return address register | jr $ra | Jumps to the address stored in $ra(almost same as return in c++ but no value is returned here just exits function back to where it was called) | No immediate version | 0x08 |
| lb | Load byte | Load a byte to the rightmost 8 bits of a register | lb $t0, 0($a0) | Stores a0 into t0 as it's byte value | No immediate version | 0x20 |  
| sb | Store byte | Store rightmost 8 bits of a register into memory | sb $t0, 0($a1) | Stores t0 byte value into t0 | No immediate version | 0x28 |
| syscall | System call | System call that executes command based on value in $v0 | syscall | Looks into $v0 and does operation dependent on value stored there, common values are 10(exit), 1 (print int stored in $a0), 11 (print char in $a0) | No immediate version |
| abs | Absolute Value | Finds values absolute value | abs.s $f0, $f1 | Finds values absolute value and stores result from $f1 to $f0 | No immediate version, **pseudo instruction** |
| neg | Make negative | Finds value a negative number | neg.s $f0, $f1 | Makes value in $f1 negative and stores it in $f0 | No immediate version, **pseudo instruction** |  
| mov.s | Copy | Copy value from one register to another | mov.s $f0, $f1 | Copies $f1 into $f0 | No immediate version |   
| mfc1 | Copy register $f to $t | Copies from f register to t | mfc1 $t0, $f0 | Copies $f0 data and puts it into $t0 | No immediate version |  
| mtc1 | Copy register $t to $f | Copies from t register to f | mtc1 $f0, $t0 | Copies $t0 data into $f0 register | No immediate version |  
| cvt.s.w | Convert int to float | Converts integer to float | cvt.s.w $f0, $f1 | Converts 32 bits in $f1 that is an integer to a float stored in $f0 | No immediate version |   
| cvt.w.s | Convert float to int | Converts float to integer | cvt.w.s $f0, $f1 | Converts 32 bit float in $f1 to 32 bit integer and stores it in $f0 | No immediate version |   
| c.lt.s | Set flag if less than |Set a flag in coprocessor if first parameter is less then second, else clear set flag values | c.lt.s $f0, $f1 | If $f0 is less then $f1 set flag, else clear set flag | No immediate version |   
| c.le.s | Set flag if less than or equal | Set a flag in the coprocessor if the first parameter is less than or equal to the second, else clear set flag | c.le.s $f0, $f1 | If $f0 is less than or equal set flag, else clear flag value | No immediate version |
| bc1t | Branch if flag set | Branch if flag value is set | bc1t L1 | Jump to L1 if flag value is set | No immediate version |  
| bc1f | Branch flag unset | Branch if flag value is unset | bc1f L1 | Jump to L1 if flag value is unset | no immediate version | 

[Test Review 1](#test-review)                
[Top of MIPS Commands](#mips-commands)
