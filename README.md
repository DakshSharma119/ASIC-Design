# ASIC-Design
## GCC Compilation Of a simple C Program
**Step 1**
In the Linux Environment, create a new C Program file. Here filename used is sumtilln.c made using gedit. </br>
**Commands**
```
gedit sumtilln.c
```

**C program**
```
#include <stdio.h>

int main() {
    int i, n=150, sum=0;
    for(i=1; i<=n; i++){
      sum = sum + i;
    }
    printf("The sum from 1 to %d is %d\n", n, sum);
    return 0;
}
```
Save the code and compile using gcc compiler and run the executable a.out file created. </br>
**Commands**

```
gcc sumtilln.c
./a.out
```
![Screenshot from 2024-08-08 01-13-54](https://github.com/user-attachments/assets/c349bcaf-6149-4670-a6ea-7aa8768c15fc)


## RISC V compilation

**Step1**
Code
```
#include <stdio.h>

int main() {
    int i, n=150, sum=0;
    for(i=1; i<=n; i++){
      sum = sum + i;
    }
    printf("The sum from 1 to %d is %d\n", n, sum);
    return 0;
}
```
Compile the C code on RISC-V compiler and create the object file (.o) that is the output of the compiler as shown in the procedure shown below. 
```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sumtilln.o sumtilln.c 

```
![Screenshot from 2024-08-08 01-17-26](https://github.com/user-attachments/assets/c6ad532f-209e-4ef0-a391-0c3c727f7139)
![Screenshot from 2024-08-08 01-16-10](https://github.com/user-attachments/assets/7735968f-ac25-4e57-bc0a-e04c5e18a443)

From the "main" section, calculate the number of instructions. Here we can count no of instruction are 15.
```
 riscv64-unknown-elf-objdump -d sumtilln.o|less
```
![Screenshot from 2024-08-08 01-20-18](https://github.com/user-attachments/assets/6f381e39-8ba3-4dd2-9b32-7ee422ef6a77)

**Step 3**
Compile the code again with -Ofast compiler flag.</br>
**Commands**
```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sumtilln.c 
```
![Screenshot from 2024-08-08 01-27-34](https://github.com/user-attachments/assets/3d0ef721-986f-4179-8654-776ea07577f6)
 Now check the "main" section of assembly level code again. Here we count no of instructions has changed to 12. </br>
 **Commands**
 ```
 riscv64-unknown-elf-objdump -d sumtilln.o|less
```
![Screenshot from 2024-08-08 01-28-44](https://github.com/user-attachments/assets/28c74ee2-a1fb-4732-bda3-b8a5557638bc)

## Observation
Here we can observe that the number of instructions are reduced to 12 as compared to 15 after usinf -Ofast flab

   * -O1 is moderate in it's code optimization while -Ofast is highly aggressive to achieve highest possible performance
   * -O1 maintains strict adherence to standards while -Ofast may violate some standards to achieve better performance

## RISC-V COMPILER OUTPUT and DEBUGGING
Finding the output of the C program on the RISC V Compiler using the Spike command and debug the code

## Output Matching
In our previous lab, we compiled our C code using both gcc and a RISC-V compiler. In lab 2 we are going to have find the result of n numbers on the RISCV compiler using the SPIKE command.
**Command**
```
spike pk sum1ton.o
```
**The compiled code using SPIKE command along with object dump file**
![Screenshot from 2024-08-08 01-48-25](https://github.com/user-attachments/assets/ed0d9333-3204-49e2-9f63-24a280ca3c17)
![Screenshot from 2024-08-08 01-55-52](https://github.com/user-attachments/assets/d791403b-8d01-4cb5-9ca7-8e5f3871d342)

### Observation
Same output in both the processes.

## Debugging using SPIKE debugger

**Commands**
```
spike -d pk sum1ton.o
until pc 0 100bo
reg 0 a2
reg 0 a2
reg 0 a0
reg 0 sp
```

Run the Spike debugger until the main function i.e. till 100b0 instruction. Then we will manually continue debugging and inspect the a2 register before and after the execution. The instruction lui a2, 0x3 updates the a2 register from 0x0000000000000000 to 0x0000000000003000</br>
Next, we will manually debug the next instruction i.e., addi sp, sp, -16. This instruction decrements the stack pointer (sp) by 16. This instruction updates the value in sp register, the sp register held the value 0x0000003ffffffb50 earlier which is then updated to 0x0000003ffffffb40.</br>
**Note: the register names might change for different codes or machines. Here we had a2 in first instruction**
![Screenshot from 2024-08-08 02-40-47](https://github.com/user-attachments/assets/de43cf92-bdd1-4018-a3e0-caf14c935c40)
## Checking same using -O1 flag
**Commands**
```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sumtilln.o sumtilln.c
spike pk sumtilln.o
spike -d pk sumtilln.o
until pc 0 10184
reg 0 sp
reg 0 sp
```
Here we run the Spike debugger until the main function i.e. till 10184 instruction. We check from the .o file starting instruction of main function and then run till that instruction then start debugging manually.</br>
![Screenshot from 2024-08-08 02-23-38](https://github.com/user-attachments/assets/d61dd614-6b96-4488-8caf-a4d7cbdc1640)
![Screenshot from 2024-08-08 02-24-36](https://github.com/user-attachments/assets/0bfa55f4-b3f0-4d48-9de0-41b66f1c2fd0)


