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
    for(i=1; i<=n; i--){
      sum = sum - i;
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
    for(i=1; i<=n; i--){
      sum = sum - i;
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
Here we can observe that the number of instructions are reduced to 12 as compared to 15 after usinf -Ofast flag. Ofast achieves the highest possible performance while -O1 maintains strict adherance to standards.
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

# RISC-V Instruction Formats

RISC-V architecture defines six primary instruction formats: R, I, S, B, U, and J. All of these formats are fixed at 32 bits in length, ensuring uniformity and consistency across the architecture.

## Instruction Format Overview

The six instruction formats can be categorized into two main groups: Core Instruction Formats and Variant Instruction Formats.

### Core Instruction Formats

1. **R-Type (Register)**: 
   - **Description**: Used for arithmetic and logical operations that involve three register operands.
   - **Operations**: Includes operations like addition, subtraction, and bitwise logical operations.
   - **Structure**: This format uses two source registers and one destination register.
   
2. **I-Type (Immediate)**: 
   - **Description**: Designed for operations that require an immediate value in conjunction with a single register operand.
   - **Operations**: Commonly used for arithmetic with immediate values, load instructions, and shift operations.
   - **Structure**: Combines one source register with a 12-bit immediate value.

3. **S-Type (Store)**: 
   - **Description**: Specifically for store operations, where data from a register is stored into memory.
   - **Operations**: Uses a base address from one register and an immediate offset to store data from another register.
   - **Structure**: The immediate value is split across two fields in the instruction.

4. **U-Type (Upper Immediate)**: 
   - **Description**: Handles operations that require a large immediate value.
   - **Operations**: Often used for loading large constants or setting up base addresses.
   - **Structure**: The immediate is stored in the upper 20 bits of the instruction.

### Variant Instruction Formats

1. **B-Type (Branch)**:
   - **Description**: A variant of the I-Type format, used for conditional branch instructions.
   - **Operations**: Branching is based on comparisons between register values, with the immediate value representing a relative offset.
   - **Structure**: Combines two registers and a branch target address specified as an immediate value.

2. **J-Type (Jump)**:
   - **Description**: A variant of the U-Type format, used for jump instructions involving large offsets.
   - **Operations**: Facilitates control flow changes, such as jumping to subroutines or functions.
   - **Structure**: Contains a large immediate value, which is used as the jump target address.

![RISC-V Instruction](https://github.com/user-attachments/assets/b2e42a91-3adb-485a-8e9d-bb27781076d6)

## Decoding Instructions provided
# RISC-V Instructions

## Instruction Details

### 1. **ADD r0, r1, r2**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r0 = 00000`
- **rs1**: `r1 = 00001`
- **rs2**: `r2 = 00010`
- **func3**: `000`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00010_00001_000_00000_0110011`
- **32-Bit Instruction (Hex)**: `0x00208033`

### 2. **SUB r2, r0, r1**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r2 = 00010`
- **rs1**: `r0 = 00000`
- **rs2**: `r1 = 00001`
- **func3**: `000`
- **func7**: `0100000`
- **32-Bit Instruction**: `0100000_00001_00000_000_00010_0110011`
- **32-Bit Instruction (Hex)**: `0x40100133`

### 3. **AND r1, r0, r2**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r1 = 00001`
- **rs1**: `r0 = 00000`
- **rs2**: `r2 = 00010`
- **func3**: `111`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00010_00000_111_00001_0110011`
- **32-Bit Instruction (Hex)**: `0x002070B3`

### 4. **OR r8, r1, r5**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r8 = 01000`
- **rs1**: `r1 = 00001`
- **rs2**: `r5 = 00101`
- **func3**: `110`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00101_00001_110_01000_0110011`
- **32-Bit Instruction (Hex)**: `0x050E433`

### 5. **XOR r8, r0, r4**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r8 = 01000`
- **rs1**: `r0 = 00000`
- **rs2**: `r4 = 00100`
- **func3**: `100`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00100_00000_100_01000_0110011`
- **32-Bit Instruction (Hex)**: `0x00404333`

### 6. **SLT r0, r1, r4**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r0 = 00000`
- **rs1**: `r1 = 00001`
- **rs2**: `r4 = 00100`
- **func3**: `010`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00100_00001_010_00000_0110011`
- **32-Bit Instruction (Hex)**: `0x0040A033`

### 7. **ADDI r2, r2, 5**
- **Type**: I Type
- **Opcode**: `0010011`
- **rd**: `r2 = 00010`
- **rs1**: `r2 = 00010`
- **imm**: `000000000101`
- **func3**: `000`
- **32-Bit Instruction**: `000000000101_00010_000_00010_0010011`
- **32-Bit Instruction (Hex)**: `0x00510089`

### 8. **SW r2, r0, 4**
- **Type**: S Type
- **Opcode**: `0100011`
- **rs1**: `r0 = 00000`
- **rs2**: `r2 = 00010`
- **imm**: `0000000_0100`
- **func3**: `010`
- **32-Bit Instruction**: `0000000_00010_00000_010_00100_0100011`
- **32-Bit Instruction (Hex)**: `0x00202223`

### 9. **SRL r6, r1, r1**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r6 = 00110`
- **rs1**: `r1 = 00001`
- **rs2**: `r1 = 00001`
- **func3**: `101`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00001_00001_101_00110_0110011`
- **32-Bit Instruction (Hex)**: `0x0020D333`

### 10. **BNE r0, r0, 20**
- **Type**: B Type
- **Opcode**: `1100011`
- **rs1**: `r0 = 00000`
- **rs2**: `r0 = 00000`
- **imm**: `000000_001010`
- **func3**: `001`
- **32-Bit Instruction**: `0000000_00000_00000_001_01010_1100011`
- **32-Bit Instruction (Hex)**: `0x00001563`

### 11. **BEQ r0, r0, 15**
- **Type**: B Type
- **Opcode**: `1100011`
- **rs1**: `r0 = 00000`
- **rs2**: `r0 = 00000`
- **imm**: `000000_001111`
- **func3**: `000`
- **32-Bit Instruction**: `0000000_00000_00000_000_01111_1100011`
- **32-Bit Instruction (Hex)**: `0x000007F8F3`

### 12. **LW r3, r1, 2**
- **Type**: I Type
- **Opcode**: `0000011`
- **rd**: `r3 = 00011`
- **rs1**: `r1 = 00001`
- **imm**: `000000000010`
- **func3**: `010`
- **32-Bit Instruction**: `000000000010_00001_010_00011_0000011`
- **32-Bit Instruction (Hex)**: `0x0020A18303`

### 13. **SLL r5, r1, r1**
- **Type**: R Type
- **Opcode**: `0110011`
- **rd**: `r5 = 00101`
- **rs1**: `r1 = 00001`
- **rs2**: `r1 = 00001`
- **func3**: `001`
- **func7**: `0000000`
- **32-Bit Instruction**: `0000000_00001_00001_001_00101_0110011`
- **32-Bit Instruction (Hex)**: `0x001092B3`

## Summary Table

| Instruction        | Type  | 32-Bit Binary Instruction                     | 32-Bit Hex     |
|--------------------|-------|-----------------------------------------------|----------------|
| **ADD r0, r1, r2** | R     | `0000000_00010_00001_000_00000_0110011`       | `0x00208033`   |
| **SUB r2, r0, r1** | R     | `0100000_00001_00000_000_00010_0110011`       | `0x40100133`   |
| **AND r1, r0, r2** | R     | `0000000_00010_00000_111_00001_0110011`       | `0x002070B3`   |
| **OR r8, r1, r5**  | R     | `0000000_00101_00001_110_01000_0110011`       | `0x050E433`    |
| **XOR r8, r0, r4** | R     | `0000000_00100_00000_100_01000_0110011`       | `0x00404333`   |
| **SLT r0, r1, r4** | R     | `0000000_00100_00001_010_00000_0110011`       | `0x0040A033`   |
| **ADDI r2, r2, 5** | I     | `000000000101_00010_000_00010_0010011`        | `0x00510089`   |
| **SW r2, r0, 4**   | S     | `0000000_00010_00000_010_00100_0100011`       | `0x00202223`   |
| **SRL r6, r1, r1** | R     | `0000000_00001_00001_101_00110_0110011`       | `0x0020D333`   |
| **BNE r0, r0, 20** | B     | `0000000_00000_00000_001_01010_1100011`       | `0x00001563`   |
| **BEQ r0, r0, 15** | B     | `0000000_00000_00000_000_01111_1100011`       | `0x000007F8F3` |
| **LW r3, r1, 2**   | I     | `000000000010_00001_010_00011_0000011`        | `0x0020A18303` |
| **SLL r5, r1, r1** | R     | `0000000_00001_00001_001_00105_0110011`       | `0x001092B3`   |

## Functional Simulation

The given hardcoded instructions are:
![Screenshot from 2024-08-12 15-24-34](https://github.com/user-attachments/assets/478803df-e763-48d2-82f2-f5eb7526db97)


### Differences between Standard RISC-V ISA and Hardcoded ISA
Instead of sticking to the standard RISC-V bit patterns, the designer has implemented custom bit patterns for each instruction.

| Operation           | Standard RISC-V ISA | Hardcoded ISA   |
|---------------------|---------------------|-----------------|
| ADD R6, R2, R1      | 32'h00110333         | 32'h02208300    |
| SUB R7, R1, R2      | 32'h402083b3         | 32'h02209380    |
| AND R8, R1, R3      | 32'h0030f433         | 32'h0230a400    |
| OR R9, R2, R5       | 32'h005164b3         | 32'h02513480    |
| XOR R10, R1, R4     | 32'h0040c533         | 32'h0240c500    |
| SLT R1, R2, R4      | 32'h0045a0b3         | 32'h02415580    |
| ADDI R12, R4, 5     | 32'h004120b3         | 32'h00520600    |
| BEQ R0, R0, 15      | 32'h00000f63         | 32'h00f00002    |
| SW R3, R1, 2        | 32'h0030a123         | 32'h00209181    |
| LW R13, R1, 2       | 32'h0020a683         | 32'h00208681    |
| SRL R16, R14, R2    | 32'h0030a123         | 32'h00271803    |
| SLL R15, R1, R2     | 32'h002097b3         | 32'h00208783    |

### Functional Simulation

**Steps:**
- Clone the Repository and change the directory
- Compile the Verilog code
- Execute the test bench and generate a .vcd file:
- View the Test Bench in GTKWave

**Code:**
```
git clone https://github.com/vinayrayapati/rv32i.git 
cd rv32i
iverilog -o iiitb_rv32i iiitb_rv32i.v iiitb_rv32i_tb.v
vvp iiitb_rv32i
gtkwave iiitb_rv32i.vcd
```
![Screenshot from 2024-08-12 15-38-23](https://github.com/user-attachments/assets/162efcbb-f00a-46ab-b00e-9c4582cb87f9)

### GTK Waveforms

**ADD R6, R2, R1**
![Screenshot from 2024-08-12 15-43-19](https://github.com/user-attachments/assets/acc63846-a9c8-47ee-9425-743af533e148)

**SUB R7, R1, R2**
![Screenshot from 2024-08-12 15-44-47](https://github.com/user-attachments/assets/d787f4b8-ed3e-4df8-9cbe-a66716a7e24f)

**AND R8, R1, R3**
![Screenshot from 2024-08-12 15-45-40](https://github.com/user-attachments/assets/97797a01-9807-483d-b0d5-5d10aecde21e)

**OR R9, R2, R5**
![Screenshot from 2024-08-12 15-49-16](https://github.com/user-attachments/assets/adef0b80-4498-46d4-8b52-489b52f90953)

**XOR R10, R1, R4**
![Screenshot from 2024-08-12 15-49-39](https://github.com/user-attachments/assets/4f796550-c044-488d-a4b0-0a096b9d1034)

**SLT R1, R2, R4**
![Screenshot from 2024-08-12 15-50-08](https://github.com/user-attachments/assets/9ab77395-1a7c-4fa3-971f-4e0a2a3c32e5)

**ADDI R12, R4, 5**
![Screenshot from 2024-08-12 15-50-32](https://github.com/user-attachments/assets/c9eec35c-af95-4020-9b03-70fef8cb7308)

**SW R3, R1, 2**
![Screenshot from 2024-08-12 15-53-57](https://github.com/user-attachments/assets/59aef6f4-287b-47e0-a35c-e024a7a4783c)

**LW R13, R1, 2**
![Screenshot from 2024-08-12 15-54-14](https://github.com/user-attachments/assets/b3546ff0-aa30-4dc9-9139-4799b375b279)

**BEQ R0, R0, 15**
![Screenshot from 2024-08-12 15-54-31](https://github.com/user-attachments/assets/c0154f64-e143-45f7-a46f-4a89d84bfc45)

**Observation:** We observe a variation between bit pattern of RISCV code and hardcoded ISA.

## Compile C application with GCC and RISC-V GCC

**Application- Find Mean of elements of an array**

### Code
```
#include <stdio.h>
#include <stdlib.h>

double findMean(int a[], int n)
{
	int sum = 0;
	for (int i = 0; i < n; i++)
		sum += a[i];
	return (double)sum / (double)n;
}
int main()
{
	int a[] = { 1, 3, 4, 2, 7, 5, 8, 6 };
	int N = sizeof(a) / sizeof(a[0]);
	printf("Mean = %f\n", findMean(a, N));
	return 0;
}

```
### Compilation using GCC compiler
**Commands**</br>
```
gedit mean.c
gcc mean.c
./a.out
```
![Screenshot from 2024-08-14 23-14-05](https://github.com/user-attachments/assets/ba420bd4-6c9b-4f19-89be-f4db0beef73a)

### Compilation using RISC-V Complie and creating Objdump filr
**Commands**</br>
```
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o mean.o mean.c
spike pk mean.o
riscv64-unknown-elf-objdump -d mean.o | less
```
![Screenshot from 2024-08-14 23-23-09](https://github.com/user-attachments/assets/679a8a72-32c3-4d2f-a7d0-218afa7f590b)

### Objdump file
![Screenshot from 2024-08-14 23-23-03](https://github.com/user-attachments/assets/17fa4b17-be96-4c3e-a07b-b5f2fa301232)

### Observation
We can observe the output that is the mean of array is verified and coming the same using gcc and the riscv compiler.

## Combinational Circuits in TL-Verilog
**Introduction to TL-Verilog and Makerchip:** Makerchip supports the Transaction-Level Verilog (TL-Verilog) standard, which advances digital design by eliminating outdated Verilog features and introducing a more streamlined syntax. TL-Verilog enhances efficiency with powerful constructs for pipelines and transactions, simplifying the development of complex circuits.

![Screenshot from 2024-08-21 00-25-04](https://github.com/user-attachments/assets/964d9ab6-70d5-45c7-91d7-851006fb6f6a)

### Inverter

**Code**
```
$out = ! $in;
```
![Screenshot from 2024-08-21 00-25-46](https://github.com/user-attachments/assets/402fed49-de13-468f-8eb2-133dab1f588c)

### 2 Input AND

**Code**
```
$out = $in1 && $in2;
```
![Screenshot from 2024-08-21 00-29-30](https://github.com/user-attachments/assets/301a2ddc-73d1-4fb9-948d-964d41dcd776)

### 2 Input OR
**Code**
```
$out = $in1 || $in2;
```
![image](https://github.com/user-attachments/assets/8b352884-8e60-4ff8-9d48-b9f61c6775aa)

### 2-Input XOR Gate
**Code**
```
$out = $in1 ^ $in2;
```
![Screenshot from 2024-08-21 00-32-22](https://github.com/user-attachments/assets/d4dfb908-ffeb-4ba9-b9b6-c8d71491529e)

### Arithmetic Operation on Vectors
**Code**
```
$out[4:0] = $in1[3:0] + $in2[3:0];
```
![Screenshot from 2024-08-21 00-35-34](https://github.com/user-attachments/assets/0985b088-6d52-4847-9970-3330040548f2)

### 2:1 MUX
**Code**
```
$out = $sel ? $in1 : $in0;
```
![Screenshot from 2024-08-21 00-38-00](https://github.com/user-attachments/assets/b150b5d6-6922-4895-bd69-89d4f91cf1ec)

###  2:1 MUX Using Vectors
**Code**
```
$out[7:0] = $sel ? $in1[7:0] : $in0[7:0];
```
![Screenshot from 2024-08-21 00-39-36](https://github.com/user-attachments/assets/904f7ada-f2ce-4308-a209-56ce9776ee22)

### Combinational Calculator in TL-Verilog
In this section, we demonstrate a basic combinational calculator implemented using TL-Verilog which performs four fundamental arithmetic operations: addition, subtraction, multiplication, and division.

```
$val1[31:0] = $rand1[3:0];
$val2[31:0] = $rand2[3:0];

$sum[31:0]  = $val1[31:0] + $val2[31:0];
$diff[31:0] = $val1[31:0] - $val2[31:0];
$prod[31:0] = $val1[31:0] * $val2[31:0];
$quot[31:0] = $val1[31:0] / $val2[31:0];

$out[31:0]  = $sel[1] ? ($sel[0] ? $quot[31:0] : $prod[31:0])
                      : ($sel[0] ? $diff[31:0] : $sum[31:0]);
```

![Screenshot from 2024-08-21 00-42-50](https://github.com/user-attachments/assets/7eb784de-cdfc-42a4-bb60-17654b58b977)

### Sequential Circuits in TL-Verilog
Sequential circuits are digital circuits that use memory components to retain data, allowing them to generate outputs based on both current inputs and previous states. Unlike combinational circuits, whose outputs depend only on present inputs, sequential circuits rely on feedback loops and storage elements like flip-flops or registers to track their internal state. This state, combined with current inputs, influences the circuit's behavior, enabling tasks like counting, data storage, or sequencing events.

**1. Fibonacci Series**
The Fibonacci series is a sequence of numbers where each number is the sum of the two preceding ones, starting with 0 and 1. The series begins 0, 1, 1, 2, 3, 5, 8, and continues indefinitely. </br>
**Code**
```
$reset = *reset;
$num[31:0] = $reset ? 1 : (>>1$num + >>2$num);
```
![Screenshot from 2024-08-21 00-48-48](https://github.com/user-attachments/assets/4cf12e77-742e-44c9-8992-7f5169513b8d)

**2. Free Running Counter**
Next Value increments by 1 of the previous value </br>
**Code**
```
$reset = *reset;
$cnt[31:0] = $reset ? 0 : (>>1$cnt + 1);
```

![Screenshot from 2024-08-21 00-51-19](https://github.com/user-attachments/assets/ebce7e85-fb5a-4010-9da9-f878b298f02c)

**3. Sequential Calculator**
Works the same way as a combinational calculator but mimics a real scenario in which the result of the previous operation is considered as one of the operands for the next operation. Upon reset the result becomes zero. </br>
**Code**
```
$reset = *reset;
   
$val1[31:0] = >>1$out;
$val2[31:0] = $rand[3:0];
   
$sum[31:0] =  $val1[31:0] +  $val2[31:0];
$diff[31:0] =  $val1[31:0] -  $val2[31:0];
$prod[31:0] =  $val1[31:0] *  $val2[31:0];
$quot[31:0] =  $val1[31:0] /  $val2[31:0];
   
   
$out[31:0] = $reset ? 32'h0 : ($choose[1] ? ($choose[0] ? $quot : $prod):($choose[0] ? $diff : $sum));

```
![Screenshot from 2024-08-21 00-53-43](https://github.com/user-attachments/assets/ae282a58-02ad-4d72-8c18-a621df349850)

## Pipelined Logic
In Transaction-Level Verilog (TL-Verilog), pipelined logic is naturally expressed using pipeline constructs that represent the movement of data through various stages of a design. Each stage corresponds to a clock cycle, where data is processed and passed along to the next stage. This approach simplifies the modeling of sequential logic by automatically managing the transfer of states and values between cycles. With TL-Verilog's pipeline notation, designers can clearly describe complex, multi-stage operations, making the design and verification process more straightforward while improving clarity and maintainability.

**1. To produce the Pipeline Design** </br>
**Code**
```
$reset = *reset;
$clk_dak = *clk;
|comp
  @1
    $err1 = $bad_input || $illegal_op;
  @3
    $err2 = $over_flow || $err1;
  @6
    $err3 = $div_by_zero || $err2;
```
![Screenshot from 2024-08-21 01-00-18](https://github.com/user-attachments/assets/12fdc802-2fae-4df6-bfdd-9fb8a4df6b4c)

**2. 2 Cycle Calculator** </br>
**Code**
```
|calc
  @1
    $reset = *reset;
    $clk_dak = *clk;
   
    $val1[31:0] = >>2$out[31:0];
    $val2[31:0] = $rand2[3:0];
    $sel[1:0] = $rand3[1:0];
   
    $sum[31:0] = $val1[31:0] + $val2[31:0];
    $diff[31:0] = $val1[31:0] - $val2[31:0];
    $prod[31:0] = $val1[31:0] * $val2[31:0];
    $quot[31:0] = $val1[31:0] / $val2[31:0];
         
    $count = $reset ? 0 : >>1$count + 1;
         
  @2
    $valid = $count;
    $inv_valid = !$valid;
    $calc_reset = $reset | $inv_valid;
    $out[31:0] = $calc_reset ? 32'b0 : ($op[1] ? ($op[0] ? $quot[31:0] : $prod[31:0])
                                             : ($op[0] ? $diff[31:0] 
                                                        : $sum[31:0]));


```
![Screenshot from 2024-08-21 01-05-02](https://github.com/user-attachments/assets/51ccf5ed-6473-4a17-ae40-a5f4a55b437f)

## Validity
When generating a waveform, each clock cycle produces results, but logical errors can still occur, which may not be immediately evident by just analyzing the waveforms, even if there are no compilation errors. Additionally, certain "don't care" conditions that don’t impact the design should be overlooked. The concept of validity is introduced to tackle these challenges. The global clock drives all operations constantly, even when they're unnecessary, leading to wasted power. In physical circuits, clocks are powered by voltage or current, consuming energy with every cycle. In complex systems, not skipping unnecessary operations can cause significant power loss. To enhance power efficiency, clock gating is used to disable the clock during cycles when operations aren't needed. Validity plays a critical role in ensuring that only necessary operations are carried out, making clock gating effective.</br>
**1. Total Distance Calculator**</br>
**Code**
```
|calc
  @1
    $reset = *reset;
    $clk_dak = *clk;
            
    ?$vaild      
      @1
        $aa_seq[31:0] = $aa[3:0] * $aa;
        $bb_seq[31:0] = $bb[3:0] * $bb;;
      
      @2
        $cc_seq[31:0] = $aa_seq + $bb_seq;;
      
      @3
        $cc[31:0] = sqrt($cc_seq);
            
      @4
         $total_distance[63:0] = 
            $reset ? '0 :
            $valid ? >>1$total_distance + $cc :
                     >>1$total_distance;


```

![Screenshot from 2024-08-21 18-48-30](https://github.com/user-attachments/assets/1dc35326-cf78-4fea-8bff-3d7a6b4a608e)

**2. Cycle Calulator with validity**</br>
![Screenshot from 2024-08-22 01-55-13](https://github.com/user-attachments/assets/53fc0a1a-cbe8-4a8e-bd54-6a484916e3f7)

**Code**</br>
```
|calc
@0
   $reset = *reset;
    $clk_dak = *clk;
         
@1
    $val1 [31:0] = >>2$out [31:0];
    $val2 [31:0] = $rand2[3:0];
         
    $valid = $reset ? 1'b0 : >>1$valid + 1'b1 ;
    $valid_or_reset = $valid || $reset;
         
vaild_or_reset
   @1   
      $sum [31:0] = $val1 + $val2;
      $diff[31:0] = $val1 - $val2;
      $prod[31:0] = $val1 * $val2;
      $quot[31:0] = $val1 / $val2;
            
   @2   
      $out [31:0] = $reset ? 32'b0 :
                          ($op[1:0] == 2'b00) ? $sum :
                          ($op[1:0] == 2'b01) ? $diff :
                          ($op[1:0] == 2'b10) ? $prod :
                                                $quot ;
```
![image](https://github.com/user-attachments/assets/457fe2b7-541b-4aff-9199-0322079e45d2)

 **3. Calculator with Single Value Memory**</br>
 ![Screenshot from 2024-08-22 02-00-49](https://github.com/user-attachments/assets/a4621039-b256-48fb-9e18-3421b4b4e7a6)
**Code**</br>
```
|calc
  @0
    $reset = *reset;
    $clk_dak = *clk;
         
   @1
    $val1 [31:0] = >>2$out;
    $val2 [31:0] = $rand2[3:0];
         
    $valid = $reset ? 1'b0 : >>1$valid + 1'b1 ;
    $valid_or_reset = $valid || $reset;
         
  ?$vaild_or_reset
    @1   
      $sum [31:0] = $val1 + $val2;
      $diff[31:0] = $val1 - $val2;
      $prod[31:0] = $val1 * $val2;
      $quot[31:0] = $val1 / $val2;
            
     @2   
        $mem[31:0] = $reset ? 32'b0 :
                     ($op[2:0] == 3'b101) ? $val1 : >>2$mem ;
            
        $out [31:0] = $reset ? 32'b0 :
                       ($op[2:0] == 3'b000) ? $sum :
                      ($op[2:0] == 3'b001) ? $diff :
                      ($op[2:0] == 3'b010) ? $prod :
                      ($op[2:0] == 3'b011) ? $quot :
                      ($op[2:0] == 3'b100) ? >>2$mem : >>2$out ;
```

![image](https://github.com/user-attachments/assets/0d330423-f0bd-4c17-bd3d-730bb47777f1)

## Implementation of the RISC-V CPU Core
Given below is the riscv block diagram</br>
![Screenshot from 2024-08-21 18-52-03](https://github.com/user-attachments/assets/b4df8c82-b3cd-4a8d-bfb4-887bd4935a5f)
</br>
The design of a basic RISC-V CPU core involves several key logical blocks, which include the following components:</br>
### 1. Program Counter
The program counter increments its value by 4 to fetch the next instruction from memory, as illustrated in the image below. If a reset is triggered, the program counter is reset to zero, preparing it to fetch the next instruction.

The diagram below demonstrates how the program counter operates.</br>
![Screenshot from 2024-08-21 18-56-12](https://github.com/user-attachments/assets/ee18c51d-489b-4216-a2b3-408076a1fbdd)
**Code**</br>
```
$reset = *reset;
$clk_dak = *clk;
$reset = *reset;

|cpu
  @0
    $reset = *reset;
    $pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;
```

![Screenshot from 2024-08-21 23-12-46](https://github.com/user-attachments/assets/38935849-9dee-48e6-85ec-17a893efb5f6)


### 2. Instruction Fetch
The Instruction Fetch Unit (IFU) in a CPU organizes and retrieves program instructions from memory to ensure they are executed in the correct sequence, forming part of the core's control logic. The program counter provides the address of the next instruction in the instruction memory, which must be fetched to continue processing. In this setup, the instruction memory is integrated into the program. The IFU retrieves instructions from this memory and passes them to the Decode logic for further processing. The read address for the instruction memory is determined by the program counter, which outputs a 32-bit instruction (instr[31:0]).</br>

![Screenshot from 2024-08-21 23-02-23](https://github.com/user-attachments/assets/712fc7da-01f6-49b1-ba26-6228461f6d3a)

**Code**</br>
```
|cpu
  @0
    $reset = *reset;
    $clk_dak = *clk;
    $pc[31:0] = $reset ? '0 : >>1$pc + 32'd4;
         
    $imem_rd_en = !$reset ? 1 : 0;
    $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];

  @1
    $instr[31:0] = $imem_rd_data[31:0];
```
![Screenshot from 2024-08-21 23-13-42](https://github.com/user-attachments/assets/59b279e0-1d10-4b56-b136-6f7a8c679c79)

### 3. Instruction Decode
In the decode stage, the primary goal is to extract detailed information from the instruction fetched in the previous stage. This involves determining the instruction type, identifying any immediate values, and extracting relevant register values. Each instruction is analyzed to identify its components, such as the opcode and bit fields, which are then interpreted according to the RISC-V ISA specifications. The opcode is mapped to its corresponding instruction, and the necessary fields are extracted for further processing.
![Screenshot from 2024-08-21 23-08-25](https://github.com/user-attachments/assets/24538850-5323-44d5-8814-1c2d8e896522)

**Code**</br>
```
 //INSTRUCTION TYPES DECODE         
@1
  $is_u_instr = $instr[6:2] ==? 5'b0x101;
         
  $is_s_instr = $instr[6:2] ==? 5'b0100x;
         
  $is_r_instr = $instr[6:2] ==? 5'b01011 ||
                       $instr[6:2] ==? 5'b011x0 ||
                       $instr[6:2] ==? 5'b10100;
         
  $is_j_instr = $instr[6:2] ==? 5'b11011;
         
  $is_i_instr = $instr[6:2] ==? 5'b0000x ||
                       $instr[6:2] ==? 5'b001x0 ||
                       $instr[6:2] ==? 5'b11001;
         
  $is_b_instr = $instr[6:2] ==? 5'b11000;
         
  //INSTRUCTION IMMEDIATE DECODE
  $imm[31:0] = $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                      $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                      $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], 1'b0} :
                      $is_u_instr ? {$instr[31:12], 12'b0} :
                      $is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0} :
                                    32'b0;
         
         
         
         
         
  //INSTRUCTION FIELD DECODE
  $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
  ?$rs2_valid
    $rs2[4:0] = $instr[24:20];
            
  $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
  ?$rs1_valid
    $rs1[4:0] = $instr[19:15];
         
  $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
  ?$funct3_valid
    $funct3[2:0] = $instr[14:12];
            
  $funct7_valid = $is_r_instr ;
  ?$funct7_valid
    $funct7[6:0] = $instr[31:25];
  $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
  ?$rd_valid
    $rd[4:0] = $instr[11:7];
         
         
   //INSTRUCTION DECODE
  $opcode[6:0] = $instr[6:0];
         
  $dec_bits [10:0] = {$funct7[5], $funct3, $opcode};
  $is_beq = $dec_bits ==? 11'bx_000_1100011;
  $is_bne = $dec_bits ==? 11'bx_001_1100011;
  $is_blt = $dec_bits ==? 11'bx_100_1100011;
  $is_bge = $dec_bits ==? 11'bx_101_1100011;
  $is_bltu = $dec_bits ==? 11'bx_110_1100011;
  $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
  $is_addi = $dec_bits ==? 11'bx_000_0010011;
  $is_add = $dec_bits ==? 11'b0_000_0110011;
         
  `BOGUS_USE ($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu $is_addi $is_add)
```

![Screenshot from 2024-08-21 23-16-40](https://github.com/user-attachments/assets/a7286a38-283d-45d9-a6a0-7ab1d4e115f5)

### 4. Register File Read
Most instructions, particularly arithmetic ones, require accessing source registers, making it necessary to read from these registers. The CPU's register file allows two simultaneous reads for the source operands (rs1 and rs2) and one write per cycle to the destination register. The rs1 and rs2 inputs are provided to the register file, which then outputs the corresponding register contents. Enable bits are set based on the validity of rs1 and rs2 determined in the previous stage. This 2-port register file configuration supports reading two registers simultaneously. The retrieved values are then stored in registers and sent to the ALU for processing.
**Code**
```
//REGISTER FILE READ
$rf_wr_en = 1'b0;
$rf_wr_index[4:0] = 5'b0;
$rf_rd_en1 = $rs1_valid;
$rf_rd_index1[4:0] = $rs1;
$rf_rd_en2 = $rs2_valid;
$rf_rd_index2[4:0] = $rs2;
         
$src1_value[31:0] = $rf_rd_data1;

$src2_value[31:0] = $rf_rd_data2;
```
![Screenshot from 2024-08-21 23-19-45](https://github.com/user-attachments/assets/1f23ad97-1253-4003-8bf3-01c5645659ba)

### 5. Arithmetic and Logic Unit (ALU)
The Arithmetic Logic Unit (ALU) performs computations based on the specified operation. It takes input data from two registers provided by the register file, executes the required arithmetic operation, and writes the result back to memory through the register file's write port. In this context, the code currently supports only the ADD and ADDI operations for executing the test code.
**Code**
```
//ARITHMETIC AND LOGIC UNIT (ALU)
$result[31:0] = $is_addi ? $src1_value + $imm :
              $is_add ? $src1_value + $src2_value :
                32'bx ;
```
![Screenshot from 2024-08-21 23-24-26](https://github.com/user-attachments/assets/105927ee-0822-4d3b-8518-0157f1c8459c)

### 6. Register File Write
In this step, the output of the ALU is stored in a destination register (rd) if required by the instruction. The result is written back to memory through the register_file_write port, with the register_file_write_enable signal determined by the validity of the destination register. The register_file_write_index assigns the value to the appropriate memory location. In RISC-V architecture, the x0 register is hardwired to zero, so a condition is implemented to prevent any write operations to x0, ensuring it remains constant. After the ALU processes the register values, this step writes the results back to the registers, avoiding any modifications to x0.
**Code**
```
//REGISTER FILE WRITE
$rf_wr_en = $rd_valid && $rd != 5'b0;
$rf_wr_index[4:0] = $rd;
$rf_wr_data[31:0] = $result;
```
![Screenshot from 2024-08-21 23-27-06](https://github.com/user-attachments/assets/bc6a6789-dab0-42cc-bb64-19b8397481a5)

### 7. Branch Instruction
The final step includes supporting branch instructions. In the RISC-V ISA, branches are conditional, meaning they are taken only if a specific condition is met. This requires calculating the branch target address (PC). If the branch condition is satisfied, the program counter (PC) is updated to this new branch target, altering the flow of execution as needed.
**Code**
```
//BRANCH INSTRUCTIONS 1
$taken_branch = $is_beq ? ($src1_value == $src2_value):
$is_bne ? ($src1_value != $src2_value):
$is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])):
$is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])):
$is_bltu ? ($src1_value < $src2_value):
$is_bgeu ? ($src1_value >= $src2_value):
                                    1'b0;
`BOGUS_USE($taken_branch)
         
//BRANCH INSTRUCTIONS 2
 $br_target_pc[31:0] = $pc +$imm;
```
![Screenshot from 2024-08-21 23-30-40](https://github.com/user-attachments/assets/be00bebd-b4fb-4c1c-b18f-871f0224bc88)

# Pipelining the RISC-V CPU Core

The RISC-V core designed is divided into 5 pipeline stages. Pipelining in Makerchip is extremely simple. </br>
**Syntax**
```
|<pipeline_name>
  @<pipeline_stage>
    instructions in the current stage
    .
    .
  @<pipeline_stage>
    instructions in the current stage
    .
    .

```
## Load/Store Instructions

Load/store and jump support are added along with the following two extra lines of code to test load and store.
```
m4_asm(SW, r0, r10, 10000)
m4_asm(LW, r17, r0, 10000)
```

## Testing the core with a Testbench
As the implementation is complete, a simple testbench statement can be added to ensure whether the core is working correctly or not. The "passed" and "failed" signals are used to control the simulation with the Makerchip platform. It tells the platform whether the simulation passed without any errors or failed with a list of errors that can be inferred from the log files, and hence to stop the simulation if failed.

```
*passed = |cpu/xreg[17]>>5$value == (1+2+3+4+5+6+7+8+9);
```
![Screenshot from 2024-08-22 01-07-48](https://github.com/user-attachments/assets/6084cdbc-0fc6-4107-8c29-18dfb87fd87e)

Here, in the instruction memory, register r10 has been used to store the sum value. The simulation passed message can be seen under the "Log" tab. We have used ">>5" (ahead by 5) operator, because instead of stopping the simulator immediately, we wait for a couple of more cycles so as to see a little bit more on the waveform.

![Screenshot from 2024-08-22 01-10-09](https://github.com/user-attachments/assets/98a7e380-e770-448e-a0ae-3dd5bb9fdb37)

![image](https://github.com/user-attachments/assets/9c9245c4-e31e-41fa-ac7d-d558e1de3a5a)


### CLK Waveform
![Screenshot from 2024-08-22 02-28-45](https://github.com/user-attachments/assets/8b610f93-900e-48e3-8e3f-135a2163d16d)

### Reset Waveform
![Screenshot from 2024-08-22 02-28-45](https://github.com/user-attachments/assets/95adc55b-0437-45a7-8e4d-c74167827a06)


**Observation**</br>
We can see that the value stored in reg 14 is being incremented and goes till `0x2d` i.e. 45 in decimal.</br>
### The VIZ Graphic Visualizer
The final sum output of numbers from 1 to 9, ie equal to 45 has been stored in the register r10, and simultaneously written into memory address 16 (4 because of byte addressing).

![Screenshot from 2024-08-22 01-12-27](https://github.com/user-attachments/assets/19d24f6d-7aba-45e1-87c6-88f525ceeab5)

### Final RISC-V CPU Core Implementation
![Screenshot from 2024-08-22 01-15-00](https://github.com/user-attachments/assets/9f5c7910-0fda-4d01-bcac-3cd28a7ba0c7)


![Screenshot from 2024-08-22 01-13-54](https://github.com/user-attachments/assets/c57e706c-9113-474d-a740-46140d7f0d0d)

**Observation**</br>
A 5-stage pipeline design, using clk_dak, computes the sum of numbers from 1 to 9 across various stages. The stages include Instruction Fetch, Instruction Decode, Execute, Memory Access, and Write-back. The entire process takes 58 cycles to complete.
## Assignment 6
## Converting TL-Verilog to Verilog and Simulating with a Testbench
The RISC-V processor was initially designed using TL-Verilog in the Makerchip IDE. To deploy this design on an FPGA, it must first be converted to standard Verilog. This conversion was achieved using the Sandpiper-SaaS compiler.
### Steps
1. Run these commands to set up a virtual environment for working with simulation and synthesis tools, for tasks involving Verilog and RISC-V.
```
python3 -m venv env
source ~/env/bin/activate
pip install pyyaml click sandpiper-saas
```
![Screenshot from 2024-08-27 00-07-51](https://github.com/user-attachments/assets/04f0681c-a0d5-45cf-a73b-33e2954ecd77)

2.**Clone the github repo:** clone VSDBabySoC design files and testbench.
```
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoc
```
![Screenshot from 2024-08-27 00-14-42](https://github.com/user-attachments/assets/34f3cff2-4a9e-4e98-b39a-611a7de8b301)
3. **Replace the rvmyth.tlv file in the VSDBabySoC Directory:** Replace the `rvmyth.tlv` file in the `VSDBabySoC/src/module` folder with our RISC-V design from `makerchip.tlv` file which we want to convert into verilog and also change the testbench according to our makerchip code.</br>
**Code:**
```
\m4_TLV_version 1d: tl-x.org
\SV
   m4_include_lib(['https://raw.githubusercontent.com/shivanishah269/risc-v-core/master/FPGA_Implementation/riscv_shell_lib.tlv'])
   
   // Module interface, either for Makerchip, or not.
   m4_ifelse_block(M4_MAKERCHIP, 1, ['
   // Makerchip module interface.
   m4_makerchip_module
   wire CLK = clk;
   logic [9:0] OUT;
   assign passed = cyc_cnt > 300;
   '], ['
   // Custom module interface for BabySoC.
   module rvmyth(
      output reg [9:0] OUT,
      input CLK,
      input reset
   );
   wire clk = CLK;
   '])
   
\TLV
   // External to function:
   m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
   // Function:
   m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
   m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
   m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
   // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program
   m4_asm(SW, r0, r10, 10000)           // Store the final result value to byte address 16
   m4_asm(LW, r17, r0, 10000)           // Load the final result value from adress 16 to x17
   //
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)
   //
   |cpu
      @0
         $reset = *reset;
         $clk_dak = *clk;
         `BOGUS_USE($clk_dak)
      //Fetch
         // Next PC
         $pc[31:0] = (>>1$reset) ? 32'd0 : 
                     (>>3$valid_taken_br) ? >>3$br_tgt_pc : 
                     (>>3$valid_load) ? >>3$inc_pc : 
                     (>>3$valid_jump && >>3$is_jal) ? >>3$br_tgt_pc :
                     (>>3$valid_jump && >>3$is_jalr) ? >>3$jalr_tgt_pc : >>1$inc_pc;
         
         $imem_rd_en = !$reset;
         $imem_rd_addr[31:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
         
      @1         
         $instr[31:0] = $imem_rd_data[31:0];
         $inc_pc[31:0] = $pc + 32'd4;          
      // Decode   
         $is_i_instr = $instr[6:2] == 5'b00000 ||
                       $instr[6:2] == 5'b00001 ||
                       $instr[6:2] == 5'b00100 ||
                       $instr[6:2] == 5'b00110 ||
                       $instr[6:2] == 5'b11001;
         $is_r_instr = $instr[6:2] == 5'b01011 ||
                       $instr[6:2] == 5'b10100 ||
                       $instr[6:2] == 5'b01100 ||
                       $instr[6:2] == 5'b01101;                       
         $is_b_instr = $instr[6:2] == 5'b11000;
         $is_u_instr = $instr[6:2] == 5'b00101 ||
                       $instr[6:2] == 5'b01101;
         $is_s_instr = $instr[6:2] == 5'b01000 ||
                       $instr[6:2] == 5'b01001;
         $is_j_instr = $instr[6:2] == 5'b11011;
         
         $imm[31:0] = $is_i_instr ? { {21{$instr[31]}} , $instr[30:20] } :
                      $is_s_instr ? { {21{$instr[31]}} , $instr[30:25] , $instr[11:8] , $instr[7] } :
                      $is_b_instr ? { {20{$instr[31]}} , $instr[7] , $instr[30:25] , $instr[11:8] , 1'b0} :
                      $is_u_instr ? { $instr[31:12] , 12'b0} : 
                      $is_j_instr ? { {12{$instr[31]}} , $instr[19:12] , $instr[20] , $instr[30:21] , 1'b0} : 32'b0;
         
         $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
         $rs1_valid = $is_r_instr || $is_s_instr || $is_b_instr || $is_i_instr;
         $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         $funct3_valid = $is_r_instr || $is_s_instr || $is_b_instr || $is_i_instr;
         $funct7_valid = $is_r_instr;
         
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20];
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15];
         ?$rd_valid
            $rd[4:0] = $instr[11:7];
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];
            
         $opcode[6:0] = $instr[6:0];
         
         $dec_bits[10:0] = {$funct7[5],$funct3,$opcode};
         
         // Branch Instruction
         $is_beq = $dec_bits[9:0] == 10'b000_1100011;
         $is_bne = $dec_bits[9:0] == 10'b001_1100011;
         $is_blt = $dec_bits[9:0] == 10'b100_1100011;
         $is_bge = $dec_bits[9:0] == 10'b101_1100011;
         $is_bltu = $dec_bits[9:0] == 10'b110_1100011;
         $is_bgeu = $dec_bits[9:0] == 10'b111_1100011;
         
         // Arithmetic Instruction
         $is_add = $dec_bits == 11'b0_000_0110011;
         $is_addi = $dec_bits[9:0] == 10'b000_0010011;
         $is_or = $dec_bits == 11'b0_110_0110011;
         $is_ori = $dec_bits[9:0] == 10'b110_0010011;
         $is_xor = $dec_bits == 11'b0_100_0110011;
         $is_xori = $dec_bits[9:0] == 10'b100_0010011;
         $is_and = $dec_bits == 11'b0_111_0110011;
         $is_andi = $dec_bits[9:0] == 10'b111_0010011;
         $is_sub = $dec_bits == 11'b1_000_0110011;
         $is_slti = $dec_bits[9:0] == 10'b010_0010011;
         $is_sltiu = $dec_bits[9:0] == 10'b011_0010011;
         $is_slli = $dec_bits == 11'b0_001_0010011;
         $is_srli = $dec_bits == 11'b0_101_0010011;
         $is_srai = $dec_bits == 11'b1_101_0010011;
         $is_sll = $dec_bits == 11'b0_001_0110011;
         $is_slt = $dec_bits == 11'b0_010_0110011;
         $is_sltu = $dec_bits == 11'b0_011_0110011;
         $is_srl = $dec_bits == 11'b0_101_0110011;
         $is_sra = $dec_bits == 11'b1_101_0110011;
         
         // Load Instruction
         $is_load = $dec_bits[6:0] == 7'b0000011;
         
         // Store Instruction
         $is_sb = $dec_bits[9:0] == 10'b000_0100011;
         $is_sh = $dec_bits[9:0] == 10'b001_0100011;
         $is_sw = $dec_bits[9:0] == 10'b010_0100011;
         
         // Jump Instruction
         $is_lui = $dec_bits[6:0] == 7'b0110111;
         $is_auipc = $dec_bits[6:0] == 7'b0010111;
         $is_jal = $dec_bits[6:0] == 7'b1101111;
         $is_jalr = $dec_bits[9:0] == 10'b000_1100111;
         
         $is_jump = $is_jal || $is_jalr;
         
      @2   
      // Register File Read
         $rf_rd_en1 = $rs1_valid;
         ?$rf_rd_en1
            $rf_rd_index1[4:0] = $rs1[4:0];
         
         $rf_rd_en2 = $rs2_valid;
         ?$rf_rd_en2
            $rf_rd_index2[4:0] = $rs2[4:0];
            
      // Branch Target PC       
         $br_tgt_pc[31:0] = $pc + $imm;
      
      // Jump Target PC
         $jalr_tgt_pc[31:0] = $src1_value + $imm;
         
      // Input signals to ALU
         $src1_value[31:0] = ((>>1$rd == $rs1) && >>1$rf_wr_en) ? >>1$result : $rf_rd_data1[31:0];
         $src2_value[31:0] = ((>>1$rd == $rs2) && >>1$rf_wr_en) ? >>1$result : $rf_rd_data2[31:0];
         
      @3   
         
      // ALU
         $sltu_result = $src1_value < $src2_value ;
         $sltiu_result = $src1_value < $imm ;
         
         $result[31:0] = $is_addi ? $src1_value + $imm :
                         $is_add ? $src1_value + $src2_value : 
                         $is_or ? $src1_value | $src2_value : 
                         $is_ori ? $src1_value | $imm :
                         $is_xor ? $src1_value ^ $src2_value :
                         $is_xori ? $src1_value ^ $imm :
                         $is_and ? $src1_value & $src2_value :
                         $is_andi ? $src1_value & $imm :
                         $is_sub ? $src1_value - $src2_value :
                         $is_slti ? (($src1_value[31] == $imm[31]) ? $sltiu_result : {31'b0,$src1_value[31]}) :
                         $is_sltiu ? $sltiu_result :
                         $is_slli ? $src1_value << $imm[5:0] :
                         $is_srli ? $src1_value >> $imm[5:0] :
                         $is_srai ? ({{32{$src1_value[31]}}, $src1_value} >> $imm[4:0]) :
                         $is_sll ? $src1_value << $src2_value[4:0] :
                         $is_slt ? (($src1_value[31] == $src2_value[31]) ? $sltu_result : {31'b0,$src1_value[31]}) :
                         $is_sltu ? $sltu_result :
                         $is_srl ? $src1_value >> $src2_value[5:0] :
                         $is_sra ? ({{32{$src1_value[31]}}, $src1_value} >> $src2_value[4:0]) :
                         $is_lui ? ({$imm[31:12], 12'b0}) :
                         $is_auipc ? $pc + $imm :
                         $is_jal ? $pc + 4 :
                         $is_jalr ? $pc + 4 : 
                         ($is_load || $is_s_instr) ? $src1_value + $imm : 32'bx;
                         
      // Register File Write
         $rf_wr_en = ($rd_valid && $valid && $rd != 5'b0) || >>2$valid_load;
         ?$rf_wr_en
            $rf_wr_index[4:0] = !$valid ? >>2$rd[4:0] : $rd[4:0];
      
         $rf_wr_data[31:0] = !$valid ? >>2$ld_data[31:0] : $result[31:0];
      
      // Branch
         $taken_br = $is_beq ? ($src1_value == $src2_value) :
                     $is_bne ? ($src1_value != $src2_value) :
                     $is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])) :
                     $is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])) :
                     $is_bltu ? ($src1_value < $src2_value) :
                     $is_bgeu ? ($src1_value >= $src2_value) : 1'b0;
                     
         $valid_taken_br = $valid && $taken_br;
         
      // Load
         $valid_load = $valid && $is_load;
         $valid = !(>>1$valid_taken_br || >>2$valid_taken_br || >>1$valid_load || >>2$valid_load || >>1$valid_jump || >>2$valid_jump);
      
      // Jump
         $valid_jump = $valid && $is_jump;
                  
      @4
         $dmem_rd_en = $valid_load;
         $dmem_wr_en = $valid && $is_s_instr;
         $dmem_addr[3:0] = $result[5:2];
         $dmem_wr_data[31:0] = $src2_value[31:0];
         
      @5   
         $ld_data[31:0] = $dmem_rd_data[31:0];
         
      // Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
      //       be sure to avoid having unassigned signals (which you might be using for random inputs)
      //       other than those specifically expected in the labs. You'll get strange errors for these.

         `BOGUS_USE($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu)
         `BOGUS_USE($is_sb $is_sh $is_sw)
   // Assert these to end simulation (before Makerchip cycle limit).
   \SV_plus
      always @ (posedge CLK) begin
         *OUT = |cpu/xreg[14]>>5$value;                
      end
   
   // Macro instantiations for:
   //  o instruction memory
   //  o register file
   //  o data memory
   //  o CPU visualization
   |cpu
      m4+imem(@1)    // Args: (read stage)
      m4+rf(@2, @3)  // Args: (read stage, write stage) - if equal, no register bypass is required
      m4+dmem(@4)    // Args: (read/write stage)

     
\SV
   
   endmodule
```
4. **Convert .tlv to .v:** Now we have written the code in TL-Verilog .tlv which is a high-level language and we want to convert into low-level verilog that is to translate .tlv definition of rvmyth into .v definition. Inorder to get verilog code of our TLV code ie, to translate .tlv definition of RISC-V into .v definition use the following code.
   ```
   sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
   ```
![Screenshot from 2024-08-27 00-20-49](https://github.com/user-attachments/assets/4857388f-a272-4783-8e60-fd7e8a44afbb)
5. **Make the pre_synth_sim.vcd**
```
make pre_synth_sim
```
![Screenshot from 2024-08-27 00-23-28](https://github.com/user-attachments/assets/7d574b14-e834-496f-be66-84ad565665aa)
6.**compile and simulate RISC-V design run the following code:**
```
iverilog -o output/pre_synth_sim.out -DPRE_SYNTH_SIM src/module/testbench.v -I src/include -I src/module
cd output
./pre_synth_sim.out
gtkwave pre_synth_sim.vcd
```
   ![Screenshot from 2024-08-27 00-28-10](https://github.com/user-attachments/assets/eda4d0e3-1eb7-41b9-b1f6-56b8d862ae26)

## Waveforms from GTKwave platform by running .v file after conversion
Pre-synthesis Simulation Results:
We have to plot the following signals in gtkwave:
 - clk_dak: The clock input to the RISC-V core.
 - reset: The input reset signal to the RISC-V core.
 - OUT[9:0]: The 10-bit output [9:0] OUT port of the RISC-V core i.e the reg[14].

![Screenshot from 2024-08-27 00-31-23](https://github.com/user-attachments/assets/5d2c3851-8064-456e-8710-7fa7878e7baf)

## Makerchip IDE simulation results for comparison

![Screenshot from 2024-08-27 00-37-13](https://github.com/user-attachments/assets/5032e5c0-8fe1-4a9b-b230-1a9f3af737d2)
![Screenshot from 2024-08-27 00-36-58](https://github.com/user-attachments/assets/7bb7d226-f18e-43e4-836b-2fab950ab10f)
![Screenshot from 2024-08-27 00-38-16](https://github.com/user-attachments/assets/cc9f611c-7640-4302-9a93-22647d10846a)

The viz file is shown below showing the sum of 1 to 9 which is 45 stored in the r10.</br>
![Screenshot from 2024-08-27 01-02-57](https://github.com/user-attachments/assets/0a0b48f7-24f6-4792-be86-a5cf45d653e6)


**Observation:** We have verified that our code for the processor works correctly as the output waveforms we obtained from .tlv file and after conversion to low level .v file using gtkwave gives the same waveforms.

## Assignment 7
## To generate waveform for DAC and PLL peripheral for Risc-V processor.
The VSDBabySoC is a small but powerful system-on-chip (SoC) built on the RISCV architecture. It was created with the primary aim of testing three open-source IP cores together for the first time and fine-tuning the analog components. This SoC features an RVMYTH microprocessor, an 8x-PLL for reliable clock generation, and a 10-bit DAC for interfacing with other analog devices.
![Screenshot from 2024-09-02 23-40-33](https://github.com/user-attachments/assets/6a62e8c1-c5d4-43d7-9355-ab8d55168082)

**BabySoC Simulation**</br>
Developing and simulating the complete micro-architecture of a RISC-V CPU is a complex task. For this simulation, we'll focus on incorporating two key IP blocks: PLL and DAC.
### Phase-Locked Loop (PLL)

A Phase-Locked Loop (PLL) is an electronic circuit that synchronizes the phase and frequency of an output signal with a reference signal. It typically consists of three key components:

1. Phase Detector: Compares the phases of the reference and output signals, producing an error signal that indicates their difference.
2. Loop Filter: Smooths the error signal to reduce noise and improve system stability.
3. Voltage-Controlled Oscillator (VCO): Adjusts its output frequency based on the filtered error signal to minimize the phase difference.

PLLs are widely used in applications such as clock generation, frequency synthesis, and data recovery in communication systems.


### Digital-to-Analog Converter (DAC)


DACs are widely used in applications such as audio playback, video display, and signal processing.

1. Clone this repo: https://github.com/Subhasis-Sahu/BabySoC_Simulation.git using the following command.
```
git clone https://github.com/Subhasis-Sahu/BabySoC_Simulation.git
cd BabySoC_Simulation
```
![Screenshot from 2024-09-03 00-03-27](https://github.com/user-attachments/assets/775bfe3a-1207-489f-b0c2-34617b942464)

2. Then run the following commands, also you have to change rvmyth.v from the previous labs as shown.
![Screenshot from 2024-09-03 00-07-46](https://github.com/user-attachments/assets/8a0d3dd8-e61d-44bf-9968-e4b9007766e9)
```
iverilog -o ./pre_synth_sim.out -DPRE_SYNTH_SIM src/module/testbench.v -I src/include -I src/module/
./pre_synth_sim.out
```
![Screenshot from 2024-09-03 00-17-12](https://github.com/user-attachments/assets/ac4c9895-c514-4cb3-9727-aca7d9e7fbf1)

3. Open GTK Wave
```
gtkwave pre_synth_sim.vcd
```
![Screenshot from 2024-09-03 00-36-15](https://github.com/user-attachments/assets/27953c51-2ade-44fc-9b02-7afd5e609ee2)

- VCO_IN is the input clk reference signal to the PLL module.
- CLK is the output clk signal from the PLL module.
- CLK_DAK is the clock used by the RISC-V CPU for the operations.
- RV_TO_DAC is the output wire connected to the Xreg[14] register of the register file,
- OUT is the analog signal coming out of the DAC unit.
- reset is the reset signal for the RISC-V CPU.

**Observation:** The simulation successfully demonstrates the integration of DAC and PLL peripherals with the RISC-V processor, converting digital outputs to analog signals.

## Assignment 8

## Introduction to Open Source Simulator: Icarus Verilog (iverilog)

In digital circuit design, **Register-Transfer Level (RTL)** is an abstraction that models synchronous digital circuits. RTL focuses on how data flows between hardware registers and how logic operations are applied to these signals. It is used in **Hardware Description Languages (HDL)** like Verilog to create high-level circuit models, which are then translated into lower-level representations for hardware synthesis.

## Icarus Verilog (iverilog) Simulator

A **simulator** is a tool used to verify digital designs. In this workshop, we utilize the **Icarus Verilog (iverilog)** tool, a popular open-source Verilog simulation tool. Simulation in digital design involves:

- **Simulation models:** These replicate the behavior of the intended device.
- **Test models (Test Benches):** These validate the functionality of the device.

## RTL Design

The RTL design consists of one or more Verilog files that implement the circuit's required design specifications and functionality.

## Test Bench

A **Test Bench** is the configuration used to provide stimulus (test vectors) to the design under test. It allows you to verify the design's functionality by applying a variety of test scenarios.

## How the Simulator Works

The simulator continuously monitors the input signals for changes. When an input changes, the simulator evaluates the circuit and updates the output accordingly, mimicking how a real device would behave.

---

### Key Concepts
- **RTL (Register Transfer Level):** Abstracts the flow of data between hardware registers.
- **HDL (Hardware Description Language):** Language used to describe the behavior and structure of electronic systems.
- **Test Bench:** Framework to provide inputs and observe outputs for validation.
- **Simulation:** Process of verifying design functionality by mimicking device behavior.

![Screenshot from 2024-10-21 20-23-43](https://github.com/user-attachments/assets/d0b9539f-b8de-4dd6-8689-8c14039dbe0a)
![Screenshot from 2024-10-21 20-24-39](https://github.com/user-attachments/assets/a1d9d1dd-40b2-46b3-a2f3-c89ce05f34ff)

## Introduction to LABS
### Environment Setup
```
mkdir VLSI 
cd VLSI
git clone https://github.com/kunalg123/vsdflow.git
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```
![Screenshot from 2024-10-21 20-28-23](https://github.com/user-attachments/assets/e64a8ad4-5ff5-417e-99eb-c0fee9893772)
**sky130RTLDesignAndSynthesisWorkshop** Directory has: **My_Lib** - which contains all the necessary library files; where lib has the standard cell libraries to be used in synthesis and verilog_model with all standard cell verilog models for the standard cells present in the lib. Ther verilog_files folder contains all the experiments for lab sessions including both verilog code and test bench codes.

![Screenshot from 2024-10-21 20-30-46](https://github.com/user-attachments/assets/f482aa00-0aa5-4850-b192-6e6208eb4f70)

### Simulation using iverilog simulator - 2:1 multiplexer rtl design
### VERILOG FILE OF A SIMPLE 2:1 MUX
Compile the verilog and testbench file use the commands to generate an executable file and will dump the waveform to view it using the gtkwave.
```
iverilog good_mux.v tb_good_mux.v
```
![Screenshot from 2024-10-21 20-34-12](https://github.com/user-attachments/assets/c59996ad-d871-418b-9b28-a7119ed90813)
To view the contents of the file run the following command
```
$ vim tb_good_mux.v -o good_mux.v 
```
![Screenshot from 2024-10-21 20-38-11](https://github.com/user-attachments/assets/a3c064d0-125b-433c-a032-841b3c76701c)

### GTKWAVE Analysis
```
gtkwave tb_good_mux.vcd
```
![Screenshot from 2024-10-21 20-35-54](https://github.com/user-attachments/assets/113ab970-a793-4b35-8b84-ff58527771ae)
## Introduction to Yosys & Logic Synthesis
Synthesizer is a tool for converting the RTL to Netlist and here we are using the Yosys Synthesizer.

### Yosys SETUP

![Screenshot from 2024-10-21 20-39-26](https://github.com/user-attachments/assets/e3306101-1ba4-424d-84d2-98018c68e7b7)
### Verifying the Synthesis

![Screenshot from 2024-10-21 20-40-04](https://github.com/user-attachments/assets/9f5bb047-915a-4d00-bfa4-95239ff8e6d7)

## Logic Synthesis

### RTL Design
The **RTL Design** is a behavioral representation written in HDL (Hardware Description Language) that describes the required specifications for a digital circuit.

### Synthesis
**Synthesis** is the process of translating the RTL design into a gate-level representation. The design is transformed into gates, and connections between them are established. The output of this process is a file called the **netlist**.

### .lib File
The **.lib file** is a collection of logical modules that include basic logic gates. It may contain multiple versions of the same gate (e.g., 2-input AND, 3-input AND) with different performance characteristics—such as slow, medium, and fast variants.

### Faster Cells and Slower Cells
The delay of a cell in a digital logic circuit is influenced by the load, which is determined by the circuit's capacitance.

- **Faster Cell:** A faster cell has a shorter delay, achieved by quickly charging/discharging the capacitance. This is done by using wider transistors, which can source more current, reducing the cell delay. However, wider transistors consume more power and area.
  
- **Slower Cell:** A slower cell uses narrower transistors, which reduces power consumption and area. However, this results in higher cell delay due to slower charging/discharging of the capacitance.

### Constraints
A **Constraint** is a guidance file provided to the synthesizer to optimize the logic circuit implementation. It helps the synthesizer select the appropriate flavor of cells (fast or slow) based on the design's requirements for speed, power, and area.

### Yosys flow

**start yosys**</br>
```
yosys
```
![Screenshot from 2024-10-21 20-43-51](https://github.com/user-attachments/assets/ca24002b-d8a4-494c-bc53-e3be818cdd5d)
**Read design**</br>
```
read_verilog good_mux.v
```


![Screenshot from 2024-10-21 20-45-43](https://github.com/user-attachments/assets/5e5b315c-63b5-4ab3-aefe-af27b010aad7)

**Synthesise Top Module**</br>
```
synth -top good_mux
```
![Screenshot from 2024-10-21 20-47-21](https://github.com/user-attachments/assets/4ae03af8-ac08-4cc5-898b-32f102e400dd)

**Map to the standard library**</br>
```
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![Screenshot from 2024-10-21 21-08-38](https://github.com/user-attachments/assets/c7bf53e7-c1f6-49ac-b742-f234c5c1e7e5)
To view the result as a graphich use the show command.</br>
```
show
```

![Screenshot from 2024-10-21 21-10-06](https://github.com/user-attachments/assets/3a9e745b-89ad-49e6-a258-54b98e0b65eb)
</br>
**To write the result netlist to a file use the write_veriog command.** This will output the netlist to a file in the current directory.</br>
```
write_verilog -noattr good_mux_netlist.v
```

![Screenshot from 2024-10-21 21-13-18](https://github.com/user-attachments/assets/77a56851-83a3-4174-9355-bb4e1c89e7d3)

## Day 2
## Introduction to timing labs
Navigate to the sky130RTLDesignAndSynthesisWorkshop directory then type these below commands

Command to open the libary file</br>
```
$ vim lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![Screenshot from 2024-10-21 21-18-53](https://github.com/user-attachments/assets/4c2ecf2e-77c2-454f-8eca-d3abc06a26a7)
### The standard cell library
A standard cell library is a collection of characterized logic gates that can be used to implement digital circuits.</br>
**Contents**</br>
For a design to work, there are three important parameters that determines how the Silicon works: Process (Variations due to Fabrications), Voltage (Changes in the behavior of the circuit) and Temperature (Sensitivity of semiconductors). Libraries are characterized to model these variations.</br>
![image](https://github.com/user-attachments/assets/6a001718-198c-4f3c-b348-95e4f30bb0d7)
### The .lib (Liberty) File

The **.lib file** (Liberty format) provides essential data about the standard cells used in the design. This file contains information such as timing, power, noise, and area for each standard cell. Each **.lib** file is specific to a particular **corner**—meaning it covers a specific combination of process, voltage, and temperature (PVT).

#### Library
Contains general information common to all the cells in the library.
![image](https://github.com/user-attachments/assets/62e7fb55-cae7-423f-ae5b-a55dac6a3a12)

#### Cell
Contains specific information for each **standard cell** in the library.
![image](https://github.com/user-attachments/assets/dd58c5e6-85e9-41a5-a003-b36308942c15)

#### Pin
Describes the characteristics of each pin in a cell, including:
- **Timing** (e.g., delay, setup, hold times)
- **Power consumption**
- **Capacitance**
- **Leakage current**
- **Functionality**
![image](https://github.com/user-attachments/assets/6a7317e4-506f-4e98-b906-ef99129b0c74)
##### Various Flavours of AND Cell
![image](https://github.com/user-attachments/assets/88f27209-553b-4cb0-9c19-a20ace6e0767)
![image](https://github.com/user-attachments/assets/9253f235-d259-4ffb-8841-c29ad9bc9e91)

## Hierarchial synthesis vs Flat synthesis
### Hierarchial synthesis
_Opening the file used for this experiment
```
vim multiple_modules.v
_Invoke Yosys
yosys
_Read library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Read Design
read_verilog multiple_modules.v
_Synthesize Design
synth -top multiple_modules
_Generate Netlist
abc -liberty ../lib/sky130_fd_sc_hd__t_025C_1v80.lib
_Realizing Graphical Version of Logic for multiple modules
show multiple_modules
_Writing the netlist in a crisp manner 
write_verilog -noattr multiple_modules_hier.v
!vim multiple_modules_hier.v
```
When you do synth -top 'topmodulename' in yosys, it does an hierarchical synthesis. ie the different hierarchies between modules are preserved.
![image](https://github.com/user-attachments/assets/17b507ff-4248-4259-85b9-d743dd24fe59)
Multiple Modules: - 2 SubModules Staistics of Multiple Modules</br>

![image](https://github.com/user-attachments/assets/350f0840-a8ac-400f-9370-dca28481f8d9)
Realization of the Logic</br>
![image](https://github.com/user-attachments/assets/821f811f-ee3c-473d-b494-89274660800e)
Map to the standard library</br>![Screenshot from 2024-10-21 21-42-43](https://github.com/user-attachments/assets/162262a7-5e8b-4973-bd2b-4b03ca02f73d)

![image](https://github.com/user-attachments/assets/4b440a37-6ead-4ffe-8ab7-e7ac3ba39e5e)

## Flat synthesis
Merges all hierarchical modules in the design into a single module to create a flat netlist
```
_To flatten the netlist
flatten
_Writing the netlist in a crisp manner and to view it
write_verilog -noattr multiple_modules_flat.v
!vim multiple_modules_flat.v
```
![image](https://github.com/user-attachments/assets/d449171e-75bc-4844-9022-34d9d4fb1013)

**Realisation of Logic**</br>
![image](https://github.com/user-attachments/assets/f595b1a5-5e53-4669-8fee-0eb9a8ecce8e)

**Netlist**</br>
![image](https://github.com/user-attachments/assets/4f4859ef-43d5-440c-98a2-28b6b5c1681c)
## Module Level Synthesis
This method is preferred when multiple instances of same module are used. The synthesis is carried out once and is replicate multiple times, and the multiple instances of the same module are stitched together in the top module. This method is helpful when making use of divide and conquer algorithm
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top sub_module1
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/user-attachments/assets/0b988151-eab5-42af-a86b-49505aeddb7f)
![image](https://github.com/user-attachments/assets/de6d50de-1fe1-4382-9eab-adaf1c034daa)
Realisation of logic</br>
![image](https://github.com/user-attachments/assets/c1d61d27-f10f-46ce-b359-90ee4e78d525)

## Various Flop Coding Styles and Optimization

In digital design, when an input signal changes state, the output does not change instantaneously; there is always a propagation delay. All logic gates introduce some delay, which can cause both expected and unwanted transitions in the output, referred to as **glitches**. A glitch occurs when the output momentarily differs from the expected value due to delays in signal paths. Increased delay in one path can lead to glitches when signals are combined at the output gate. Essentially, more complex combinational circuits result in more glitchy outputs that take longer to settle at the correct value.

### Flip-Flop Overview

A **D Flip-Flop** is a sequential element that captures the value of the input pin `d` at a clock's edge and stores it. It is a fundamental component in digital circuits. There are two types of D flip-flops:

- **Rising-Edge D Flip-Flop:** Captures the input on the rising edge of the clock.
- **Falling-Edge D Flip-Flop:** Captures the input on the falling edge of the clock.

### Initialization of Flip-Flops

Every flip-flop element requires an initial state; otherwise, the combinational circuit will evaluate to an undefined or garbage value. To handle this, flip-flops are equipped with control pins known as **Set** and **Reset**. These control pins can operate in two ways:

- **Synchronous Set/Reset:** The output changes state only at the clock's edge.
  ![image](https://github.com/user-attachments/assets/5df82f6b-8ce4-416b-9ce7-88576ed86963)

- **Asynchronous Set/Reset:** The output can change state immediately, independent of the clock.
  ![image](https://github.com/user-attachments/assets/4c65dc42-c985-4f18-bc56-a7817fd225ae)

### FLIP FLOP SIMULATION
Steps Followed for analysing Asynchronous behavior:
```
//Load the design in iVerilog by giving the verilog and testbench file names
iverilog dff_asyncres.v tb_dff_asyncres.v 
//List so as to ensure that it has been added to the simulator
ls
//To dump the VCD file
./a.out
//To load the VCD file in GTKwaveform
gtkwave tb_dff_asyncres.vcd
```
![image](https://github.com/user-attachments/assets/2fb800c0-54f6-45a7-b1c3-e2013b9abece)
#### GTKWave Async reset
![image](https://github.com/user-attachments/assets/6a4e5a6d-4e60-41ac-bc4c-37885a47a532)
#### GTKWave Async set
![image](https://github.com/user-attachments/assets/156a1385-efcc-41a2-ac3c-bbefffa935e6)
#### GTK WAVE OF SYNCHRONOUS RESET
![image](https://github.com/user-attachments/assets/71d4038d-018f-4545-a856-0dfca5a07755)

### FLIP FLOP SYNTHESIS
```
_Invoke Yosys
yosys
_Read library 
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Read Design
read_verilog dff_asyncres.v
_Synthesize Design - this controls which module to synthesize
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Generate Netlist
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Realizing Graphical Version of Logic for single modules
show 
```
![image](https://github.com/user-attachments/assets/3334cdb0-1532-4685-a9c5-1b7619751ee8)
![image](https://github.com/user-attachments/assets/3c094c80-e59e-4a2b-94b8-9f8f46767f4c)

#### Statistics of Async reset

![image](https://github.com/user-attachments/assets/3654648b-44df-447b-87fb-889e2276969a)

![image](https://github.com/user-attachments/assets/073df204-4978-442d-9391-287f97db56b3)
#### Realization of Logic
![image](https://github.com/user-attachments/assets/869c2e83-5e1c-4a5b-b83f-062ff1069062)

#### Statistics of D FLipflop with Asynchronous set
Follow the same steps as given above just the file name changes to dff_async_set.v
![image](https://github.com/user-attachments/assets/d483538b-7522-430d-ba02-0353d54597c0)
![image](https://github.com/user-attachments/assets/31817563-b07b-44bf-803a-6e92dc1b0cc3)

#### Realisation of Logic
![image](https://github.com/user-attachments/assets/bf6c630c-9c9b-4818-b010-c58244a0cc72)

### Statistics of D FLipflop with Synchronous Reset
![image](https://github.com/user-attachments/assets/fe927bd7-9962-437f-865e-c71da2e489df)

#### Realisation
![image](https://github.com/user-attachments/assets/74f42292-032d-4308-90e1-22f1663196b4)

### Interesting Optimizations
```
modules used are opened using the command
vim mult_*.v -o
_Invoke Yosys
yosys
_Read library 
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Read Design
read_verilog mult_2.v
_Synthesize Design - this controls which module to synthesize
synth -top mul2
_Generate Netlist
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
_Realizing Graphical Version of Logic for single modules
show 
_Writing the netlist in a crisp manner 
write_verilog -noattr mult_2.v
!vim mult_2.v
```
### mult_2.v
![image](https://github.com/user-attachments/assets/b1fa2b23-676d-4aaa-87f8-0c2fc5937b26)
![image](https://github.com/user-attachments/assets/dca9ef03-2d5a-46da-a11b-f02704217242)
![image](https://github.com/user-attachments/assets/b054de77-9c63-4f98-b434-9ceee6e5b298)
No hardware requirements - No # of memories, memory bites, processes and cells. Number of cells inferred is 0.</br>
#### Realisation
![image](https://github.com/user-attachments/assets/d45a95c5-4612-4881-a055-57ed2e3b6398)

#### NetList File of Sub-module
![image](https://github.com/user-attachments/assets/56d334d6-a104-4be5-a452-1224f8274e45)

### mult_8.v
Same steps just change name.
![image](https://github.com/user-attachments/assets/9ac0eed6-4079-4894-aef5-7838cadb961b)
#### Realisation
![image](https://github.com/user-attachments/assets/9125d8c8-7af1-4ee0-b055-53e0848bdbc1)
#### Netlist
![image](https://github.com/user-attachments/assets/020ad86b-5e5c-4524-8e57-d88b31731871)

## Day 3
# Logic Circuits

### Combinational vs. Sequential Circuits

- **Combinational Circuits** are time-independent circuits that do not depend on previous inputs to generate an output. Their output is solely determined by the current inputs.
  
- **Sequential Circuits** are time-dependent circuits that rely on clock cycles and depend on both the present and past inputs to generate an output.

---

## Introduction to Logic Optimizations

### Combinational Logic Optimization

**Why do we need Combinational Logic Optimizations?**

The primary goal of combinational logic optimization is to simplify the logic, resulting in a more **area-efficient** and **power-efficient** design. By minimizing the logic, we reduce the complexity, which leads to savings in both power consumption and silicon area.

### Types of Combinational Optimizations

#### 1. Constant Propagation
A **direct optimization technique** where inputs that do not affect the output are ignored or optimized out. This reduces unnecessary computations, saving both area and power.

Example:
Given:  
`Y = ((AB) + C)'`

If `A = 0`, the expression simplifies to:  
`Y = ((0) + C)' = C'`

#### 2. Boolean Logic Optimization
**Boolean Logic Optimization** simplifies complex boolean expressions using the rules of Boolean algebra, making the circuit more efficient.

Example:
```
assign y = a?(b?c:(c?a:0)):(!c)
```
above is simplified as
```
y = a'c' + a(bc + b'ca) 
y = a'c' + abc + ab'c 
y = a'c' + ac(b+b') 
y = a'c' + ac
y = a xnor c
```
## Sequential Logic Optimization

Sequential logic optimization focuses on improving the efficiency and performance of circuits that depend on both current and past inputs, as well as clock cycles. Optimizing sequential logic helps reduce power consumption, minimize area, and enhance circuit speed.

### Types of Sequential Optimizations

#### 1. Basic Technique:
- **Sequential Constant Propagation:**
   Similar to constant propagation in combinational logic, this technique identifies and removes inputs in sequential circuits that do not affect the output. This helps in simplifying the design and saving area and power.

#### 2. Advanced Techniques:

- **State Optimization:**
   Reduces the number of states in a sequential circuit, thereby simplifying the design. This can lead to reduced logic complexity and improved circuit performance.

- **Retiming:**
   Reorganizes the placement of flip-flops within the circuit to optimize the performance by balancing delays. Retiming can help improve the overall speed of the circuit while maintaining functionality.

- **Sequential Logic Cloning (Floorplan Aware Synthesis):**
   Duplicates or clones portions of the sequential logic, particularly when considering the physical layout of the circuit. This technique improves timing by creating faster paths in the design, often used in floorplan-aware synthesis to enhance performance.

### Design infers 2 input AND Gate:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt_clean -purge
show
```
### opt_clean -purge
Removes unused or redundant logic in the design and purges any dangling wires or gates.
![image](https://github.com/user-attachments/assets/f2087e2f-ffe9-4a15-a33e-6d2ef12ac4ce)

![image](https://github.com/user-attachments/assets/27442b20-44ec-4a0e-9855-8ef2d1e375b5)
![image](https://github.com/user-attachments/assets/08037dae-3aa0-4c8a-bf53-3345fd59c8ed)
![image](https://github.com/user-attachments/assets/bd13850a-f106-4b05-a54a-b5d1a1b7af92)
### Realisation
![image](https://github.com/user-attachments/assets/aa053652-c2fe-43d6-92e7-9b3dca275284)
### Design infers 2 input OR Gate:
```
1. yosys
2. read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check2.v
4. synth -top opt_check2
5. abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
![image](https://github.com/user-attachments/assets/da7c7313-b3ee-4602-b4be-4744795c5019)

![image](https://github.com/user-attachments/assets/1f6c0f3c-3a34-4da7-bdd7-7e7719e9d2e4)
![image](https://github.com/user-attachments/assets/e4a19de9-898c-4721-834c-e1d989c01a12)
#### Realisation
![image](https://github.com/user-attachments/assets/5c68e41b-bc5b-4919-84df-a63036eaec1a)
### Design infers 3 input AND Gate:
```
1. yosys
2. read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check3.v
4. synth -top opt_check3
5. abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
![image](https://github.com/user-attachments/assets/509356f9-28cc-4ca2-8b77-98473029a9e1)
![image](https://github.com/user-attachments/assets/ba0cffec-8093-46d8-9b8e-553d0b02c2aa)
![image](https://github.com/user-attachments/assets/c5e6502d-1381-49bf-b0d2-c51d29d016f3)
#### Realisation
![image](https://github.com/user-attachments/assets/41647196-8a29-4917-8549-e8014aaefabd)

### Design infers 2 input XNOR Gate (3 input Boolean Logic)
```
1. yosys
2. read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check4.v
4. synth -top opt_check4
5. abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
![image](https://github.com/user-attachments/assets/5d45a527-1a0d-4b74-b0c8-669f5a8d7d88)
![image](https://github.com/user-attachments/assets/9f2ab617-e6fd-41e4-bd0e-929ed8e5da61)
![image](https://github.com/user-attachments/assets/65a63bc2-8d4f-427c-9e54-5f9bdcaca7dd)
![image](https://github.com/user-attachments/assets/8c02879d-26d1-4cdf-bd9e-33072e11c07e)

### Verilog code:
```
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule

module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule

module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 

endmodule
```
### On optimisation the above design becomes a AND OR gate

Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show
write_verilog -noattr multiple_module_opt_net.v
```
![image](https://github.com/user-attachments/assets/0cbfbf20-9cfa-4759-a73a-f3a4c304ce20)

### Verilog code:
```
module sub_module(input a , input b , output y);
	assign y = a & b;
endmodule

module multiple_module_opt2(input a , input b , input c , input d , output y);
		wire n1,n2,n3;
	sub_module U1 (.a(a) , .b(1'b0) , .y(n1));
	sub_module U2 (.a(b), .b(c) , .y(n2));
	sub_module U3 (.a(n2), .b(d) , .y(n3));
	sub_module U4 (.a(n3), .b(n1) , .y(y));
endmodule
```
On optimisation the above design becomes Y=0

Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt2.v
synth -top multiple_module_opt2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show
write_verilog -noattr multiple_module_opt2_net.v
```
![image](https://github.com/user-attachments/assets/40f4249b-201f-4722-a651-d297037957f1)

### Sequential Logic Optimizations

#### Example 1:

### Verilog code:
```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_const1_net.v
```
![image](https://github.com/user-attachments/assets/dda05f29-b19c-4ff7-bd86-4ae37baac68f)
![image](https://github.com/user-attachments/assets/ce526c53-afc0-40cd-83b2-189dbacd178b)
![image](https://github.com/user-attachments/assets/62128283-292f-4912-945c-8cfc1996401c)
![image](https://github.com/user-attachments/assets/c997fe4f-d67c-4e9e-a803-158a733414bc)
![image](https://github.com/user-attachments/assets/bf421dbf-33cf-4f58-8297-6cb2c2a588b7)

### GTKWave Output:
```
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```
![image](https://github.com/user-attachments/assets/87160d04-5e8f-4389-80eb-e71dc1f7abc4)

### Example 2:

### Verilog code:
```
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_const2_net.v
```
## Day 4
## Gate Level Simulation (GLS), Blocking vs Non-Blocking, and Synthesis-Simulation Mismatch

### Gate Level Simulation (GLS)

**Gate Level Simulation (GLS)** is an essential step in the digital circuit verification process. It involves simulating the **synthesized netlist**, a gate-level representation of the design, using the same testbench used for RTL simulation. The primary goal of GLS is to verify the design’s **logical correctness** and **timing behavior** post-synthesis. By comparing the simulation outputs to the expected outputs, GLS helps ensure that no errors were introduced during synthesis and that the design meets its performance requirements.

### Blocking vs Non-Blocking Assignments

In Verilog, **blocking** (`=`) and **non-blocking** (`<=`) assignments serve different purposes in the context of sequential logic:

- **Blocking Assignment (`=`):** Executes statements in a sequential order. The next statement is executed only after the current assignment is completed. It is typically used for combinational logic.

- **Non-Blocking Assignment (`<=`):** Updates all values simultaneously at the end of the current time step. It is typically used in sequential logic, such as in always blocks sensitive to clock edges. 

Choosing the appropriate assignment is critical to avoid unwanted simulation and synthesis mismatches.

### Synthesis-Simulation Mismatch

**Synthesis-Simulation Mismatch** refers to differences between the behavior of a design during simulation (RTL) and the behavior after synthesis (Gate-level). Common reasons for this mismatch include:

- **Incorrect use of blocking vs non-blocking assignments** in sequential logic.
- **Synthesis optimizations** that alter the design’s structure, leading to different signal timings.
- **Uninitialized flip-flops** or other sequential elements, which can cause unexpected results in the synthesized design if not properly initialized in both simulation and synthesis.

Understanding and mitigating these issues is crucial to ensure that the design behaves consistently before and after synthesis.
![image](https://github.com/user-attachments/assets/1c3f7030-0a74-47b8-a418-9bb107dac360)

### GLS Simulation

#### Example 1:

**Verilog code:**</br>
```
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
assign y = sel?i1:i0;
endmodule
```
**Simulation:**</br>
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![image](https://github.com/user-attachments/assets/9e6d5244-df2f-4c13-8881-ab633c10381a)
#### Netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
write_verilog -noattr ternary_operator_mux_net.v
```
![image](https://github.com/user-attachments/assets/d2919901-d749-452f-b6c3-f94238b4cda3)
![image](https://github.com/user-attachments/assets/0c3bf7dc-b7b3-41d0-a4a4-280142786a64)
#### Realisation
![image](https://github.com/user-attachments/assets/c9cab710-eea3-49e2-afef-e0fe5b098606)
#### Netlist
![image](https://github.com/user-attachments/assets/596aa973-bdd8-46ee-9f40-0cbbbde4f21e)

## GLS:
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![image](https://github.com/user-attachments/assets/efa869eb-8eac-46d4-97ce-01a3e3c9a81b)
In this case there is no mismatch between the waveforms before and after synthesis

### Example 2:

Verilog code:</br>
```
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
Simulation:
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![image](https://github.com/user-attachments/assets/ab5f1859-5259-429a-860c-354a66326634)
##### Netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
write_verilog -noattr bad_mux_net.v
```
![image](https://github.com/user-attachments/assets/12642c06-c4f2-4bd7-bd9a-37ce97432da2)
![image](https://github.com/user-attachments/assets/5554b0e9-10d8-4a68-adda-d45014fdb06b)
#### Realisation
![image](https://github.com/user-attachments/assets/3a4d76ea-581a-441f-a3db-f8e1e8e0a61d)
#### Netlist
![image](https://github.com/user-attachments/assets/c3805239-d64a-4dc5-8eff-a68c271b97df)

### GLS:
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![image](https://github.com/user-attachments/assets/dcd4fe09-00b2-4b76-8a41-6ba891635f10)
In this case there is a synthesis and simulation mismatch. While performing synthesis yosys has corrected the sensitivity list error.

### Labs on Synthesis-Simulation mismatch for blocking statements

#### Verilog code:
```
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
d = x & c;
x = a | b;
end
endmodule
```
#### Simulation:
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![image](https://github.com/user-attachments/assets/d013c827-4005-4feb-a873-5dbdcb6003e4)
![image](https://github.com/user-attachments/assets/25f98f27-0ba7-4d59-8d0c-7846e4043795)
### Netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
write_verilog -noattr blocking_caveat_net.v
```
![image](https://github.com/user-attachments/assets/274d495c-aed9-40c0-8ceb-e821f1d1fc77)
![image](https://github.com/user-attachments/assets/0bfc07f9-55f8-40d7-962e-0dd093146b57)
#### realisation
![image](https://github.com/user-attachments/assets/954d76f7-8d44-4e3a-962b-616f3d45bf60)
#### Netlist
![image](https://github.com/user-attachments/assets/520d4a7b-212f-4e2c-ab6b-4f377852289e)
### GLS:
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![image](https://github.com/user-attachments/assets/2480437d-ff70-4304-a9fe-7417e985fcf2)
In this case there is a synthesis and simulation mismatch. While performing synthesis yosys has corrected the latch error.
