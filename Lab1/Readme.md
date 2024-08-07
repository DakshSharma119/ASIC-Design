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





