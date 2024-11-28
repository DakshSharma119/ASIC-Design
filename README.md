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
![image](https://github.com/user-attachments/assets/2adbb863-c6e6-4ca5-be92-c2c6ff26954b)
![image](https://github.com/user-attachments/assets/aed48f53-ec3a-4bb7-ae50-cfe4ba11aecc)
![image](https://github.com/user-attachments/assets/2dc59194-b7ae-426b-a5bb-86e354d253d6)
### GTKWave Output:
```
iverilog dff_const2.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```
![image](https://github.com/user-attachments/assets/e9566fd3-947c-431c-b9f5-dd1e05bfe5fe)
### Example 3:

#### Verilog code:
```
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_const3_net.v
```
![image](https://github.com/user-attachments/assets/dd7b38aa-d1f2-443f-bbeb-e66800f134cc)
![image](https://github.com/user-attachments/assets/ef8ba52b-5774-4bc3-9a2d-0cef7f9648cc)
![image](https://github.com/user-attachments/assets/32523b54-03cf-4f78-b6f8-b7422d274773)
![image](https://github.com/user-attachments/assets/30450e27-5d25-46d9-b5ee-000e1078e5fa)
### GTKWave Output:
```
iverilog dff_const3.v tb_dff_const3.v
./a.out
gtkwave tb_dff_const3.vcd
```
![image](https://github.com/user-attachments/assets/290a0e79-a0b9-4b77-bb23-b35171542871)

### Example 4:

#### Verilog code:
```
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_const4_net.v
```
![image](https://github.com/user-attachments/assets/e20e24ee-3631-47f5-91a7-545262b94a9a)
![image](https://github.com/user-attachments/assets/f3716545-c725-4910-9f30-aad517e86000)
![image](https://github.com/user-attachments/assets/12c54715-a548-420c-9524-3d084325aaa3)

#### GTKWave Output:
```
iverilog dff_const4.v tb_dff_const4.v
./a.out
gtkwave tb_dff_const4.vcd
```
![image](https://github.com/user-attachments/assets/b0544727-ff54-4078-a031-991e936705be)

### Example 5:

#### Verilog code:
```
module dff_const5(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
	begin
		if(reset)
		begin
			q <= 1'b0;
			q1 <= 1'b0;
		end
	else
		begin
			q1 <= 1'b1;
			q <= q1;
		end
	end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr dff_const5_net.v
```
![image](https://github.com/user-attachments/assets/26c79558-a75b-45cb-96e4-3b91010c3895)
![image](https://github.com/user-attachments/assets/50eaca7b-ae46-4d5b-ae14-50865e002c50)
![image](https://github.com/user-attachments/assets/8b064e4f-ea30-4550-917b-8be5d4250e62)
#### GTKWave Output:
```
iverilog dff_const5.v tb_dff_const5.v
./a.out
gtkwave tb_dff_const5.vcd
```
![image](https://github.com/user-attachments/assets/da47bfe3-4b34-4726-8a11-9097afa2b8e3)

### Sequential Logic Optimizations for unused outputs

### Example 1:

#### Verilog code:
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];
always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr counter_opt_net.v
```
![image](https://github.com/user-attachments/assets/ac9089b9-e05d-459a-abea-4d2f1df30f20)
![image](https://github.com/user-attachments/assets/0a774514-2af5-46db-9896-96b29b9dde6d)
![image](https://github.com/user-attachments/assets/35dcdad0-3248-42d8-9ff9-dfa694348281)
![image](https://github.com/user-attachments/assets/da15cb2b-e0c0-4d41-8263-3bcfdf98102d)

#### GTKWave Output:
```
iverilog counter_opt.v tb_counter_opt.v
./a.out
gtkwave tb_counter_opt.vcd
```
![image](https://github.com/user-attachments/assets/24ce6c33-38a9-4c00-b221-826b78a4e534)

### Modified counter logic:

#### Verilog code:
```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = {count[2:0]==3'b100};
always @(posedge clk ,posedge reset)
begin
if(reset)
	count <= 3'b000;
else
	count <= count + 1;
end
endmodule
```
Run the below code for netlist:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr counter_opt_net.v
```
![image](https://github.com/user-attachments/assets/a7fe428a-b6a1-408c-a12b-6fe850df2072)
![image](https://github.com/user-attachments/assets/d2c3844f-1656-404a-b17c-8f2dc7d681aa)
![image](https://github.com/user-attachments/assets/6574a773-82be-4653-91aa-e069f4be84c3)
![image](https://github.com/user-attachments/assets/b0196b76-3e0a-4d86-9861-5f3bdc78c5f7)

#### GTKWave Output:
```
iverilog counter_opt.v tb_counter_opt.v
./a.out
gtkwave tb_counter_opt.vcd
```
![image](https://github.com/user-attachments/assets/34bb3592-01b7-4de9-ba02-eb115db81a82)


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

## To Synthesize RISC-V and compare output with functional simulations
### Steps:

Copy the src folder from your VSDBabySoC folder to your VLSI folder.
![image](https://github.com/user-attachments/assets/38b3c4d1-1bf9-4793-9d00-29b42738c511)

Synthesis:
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog clk_gate.v
read_verilog rvmyth.v
synth -top rvmyth
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr rvmyth.v
!gedit rvmyth.v

```
![image](https://github.com/user-attachments/assets/ec2c6622-3abc-4098-9c3d-2dbe5d9c10f8)
![image](https://github.com/user-attachments/assets/025c6a6b-329d-40ab-aaac-2aaefb73c1ad)
![image](https://github.com/user-attachments/assets/0a37ad3b-d4d5-4ef1-9e8f-0d54742edd3b)
![image](https://github.com/user-attachments/assets/cd482746-76f9-475b-950d-da19eb70e4b2)
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -lib ../lib/avsddac.lib
read_liberty -lib ../lib/avsdpll.lib  
read_verilog vsdbabysoc.v
read_verilog rvmyth.v
read_verilog clk_gate.v 
synth -top vsdbabysoc
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
write_verilog -noattr vsdbabysoc.synth.v
```
![Screenshot from 2024-10-24 00-40-55](https://github.com/user-attachments/assets/eb3db17b-0276-44d7-9973-e5b47202c5e5)
![Screenshot from 2024-10-24 00-41-25](https://github.com/user-attachments/assets/6ffc8732-efb0-448a-8557-86af400740e1)
![Screenshot from 2024-10-24 01-47-07](https://github.com/user-attachments/assets/c1d89add-227e-4081-8c7f-78540b5861e6)
### Generate GTKWave simulations:
```
iverilog ../../my_lib/verilog_model/primitives.v ../../my_lib/verilog_model/sky130_fd_sc_hd.v rvmyth.v testbench.v vsdbabysoc.v avsddac.v avsdpll.v clk_gate.v
./a.out
gtkwave dump.vcd
```
![image](https://github.com/user-attachments/assets/befd3936-e162-4e22-91bc-88678536be7e)

![image](https://github.com/user-attachments/assets/b0607d27-ad3e-4168-bfd9-2144ab549842)
### Functional Simulations

```
cd VSDBabySoC
iverilog -o ./pre_synth_sim.out -DPRE_SYNTH_SIM src/module/testbench.v -I src/include -I src/module/
./pre_synth_sim.out
gtkwave pre_synth_sim.vcd
```
![image](https://github.com/user-attachments/assets/53ce9f58-84cd-47d5-b7bf-d3d54f1d5428)
![image](https://github.com/user-attachments/assets/1baafc63-cae5-4ecd-af19-fa880448b6f8)
![image](https://github.com/user-attachments/assets/49d7d69b-5b44-45c9-8e41-56ce3ae2f28a)
As we can see comparing both the outputs are same hence verifying our results.
![image](https://github.com/user-attachments/assets/3555f0ec-807b-44db-a0bf-400d0f4d653c)


## Post Synthesis Static Timing Analysis using OpenSTA
The contents of VSDBabySoc/src/sdc/vsdbabysoc_synthesis.sdc:
```
set PERIOD 11.15

set_units -time ns
create_clock [get_pins {pll/CLK}] -name clk -period $PERIOD
set_clock_uncertainty -setup  [expr $PERIOD * 0.05] [get_clocks clk]
set_clock_transition [expr $PERIOD * 0.05] [get_clocks clk]
set_clock_uncertainty -hold [expr $PERIOD * 0.08] [get_clocks clk]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_CP]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_VCO]
set_input_transition [expr $PERIOD * 0.08] [get_ports REF]
set_input_transition [expr $PERIOD * 0.08] [get_ports VCO_IN]
set_input_transition [expr $PERIOD * 0.08] [get_ports VREFH]
```
Now, run the below commands:
```
cd VSDBabySoc/src
sta
read_liberty -min ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -min ./lib/avsdpll.lib
read_liberty -min ./lib/avsddac.lib
read_liberty -max ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max ./lib/avsdpll.lib
read_liberty -max ./lib/avsddac.lib
read_verilog ../output/synth/vsdbabysoc.synth.v
link_design vsdbabysoc
read_sdc ./sdc/vsdbabysoc_synthesis.sdc
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
![image](https://github.com/user-attachments/assets/2bbf0e84-5de2-4825-863e-5915ac2c1245)
![image](https://github.com/user-attachments/assets/2acf591c-7212-4380-8fc3-af80934bd4af)
</br>
**Setup:**</br>
![image](https://github.com/user-attachments/assets/4233786a-eb71-422f-ac5c-da7ff3685208)
</br>
**Hold:**</br>
![image](https://github.com/user-attachments/assets/30e02d0e-7964-4f7f-b0ed-36b093f1b2e1)

## Post Synthesis Static Timing Analysis using OpenSTA
Snapshot of the sdc file vsdbabysoc_synthesis.sdc:
```
set PERIOD 11.15
set_units -time ns
create_clock [get_pins {pll/CLK}] -name clk -period $PERIOD
set_clock_uncertainty -setup  [expr $PERIOD * 0.05] [get_clocks clk]
set_clock_transition [expr $PERIOD * 0.05] [get_clocks clk]
set_clock_uncertainty -hold [expr $PERIOD * 0.08] [get_clocks clk]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_CP]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_VCO]
set_input_transition [expr $PERIOD * 0.08] [get_ports REF]
set_input_transition [expr $PERIOD * 0.08] [get_ports VCO_IN]
set_input_transition [expr $PERIOD * 0.08] [get_ports VREFH]
```
![image](https://github.com/user-attachments/assets/5f74c182-a4bd-4aa5-b0ef-453037b4d151)

Store all the lib files in the lib folder in the VSDBabySoC/src directory.
![image](https://github.com/user-attachments/assets/065dc439-6375-4e99-907b-ab08489556ca)

Now create a sta_pvt.tcl file with the following content.
```
gedit sta_pvt.tcl
```
```
set list_of_lib_files(1) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v95.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ff_n40C_1v95_ccsnoise.lib.part1"
set list_of_lib_files(8) "sky130_fd_sc_hd__ff_n40C_1v95_ccsnoise.lib.part2"
set list_of_lib_files(9) "sky130_fd_sc_hd__ff_n40C_1v95_ccsnoise.lib.part3"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(14) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(15) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(16) "sky130_fd_sc_hd__ss_n40C_1v60.lib"
set list_of_lib_files(17) "sky130_fd_sc_hd__ss_n40C_1v60_ccsnoise.lib.part1"
set list_of_lib_files(18) "sky130_fd_sc_hd__ss_n40C_1v60_ccsnoise.lib.part2"
set list_of_lib_files(19) "sky130_fd_sc_hd__ss_n40C_1v60_ccsnoise.lib.part3"
set list_of_lib_files(20) "sky130_fd_sc_hd__ss_n40C_1v76.lib"
set list_of_lib_files(21) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(22) "sky130_fd_sc_hd__tt_100C_1v80.lib"

for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {
read_liberty ./lib/$list_of_lib_files($i)
read_verilog ../output/synth/vsdbabysoc.synth.v
link_design vsdbabysoc
read_sdc ./sdc/vsdbabysoc_synthesis.sdc
check_setup -verbose
report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} > ./sta_output/min_max_$list_of_lib_files($i).txt

}

```
![image](https://github.com/user-attachments/assets/d651bca5-837a-4b38-ae4a-516ba92c22a3)

Create a folder named sta_output in VSDBabySoC/src to store the output txt files. Now, run the following commands:
```
cd VSDBabySoC/src
sta
source sta_pvt.tcl
```
![image](https://github.com/user-attachments/assets/d455a02a-3a76-4b91-a84f-865c29bd47cf)

These .txt file will be generated as shown

![image](https://github.com/user-attachments/assets/e5f3872e-e8a4-4025-8edc-fda6983c5501)
![image](https://github.com/user-attachments/assets/20da6796-75f6-46bb-9eee-0f66668cdaf4)
![image](https://github.com/user-attachments/assets/f7cd64e7-3f07-4b66-a7a0-9024fae9cc32)

We can use this python code to store values in excel:
```
import os
import pandas as pd

def extract_words_from_file(file_path):
    """
    Extract the first word from line 27 and the first word from the last non-empty line of a file.
    Returns None if the file has less than 27 lines.
    """
    try:
        with open(file_path, 'r') as file:
            lines = file.readlines()
            
            # Ensure the file has at least 27 lines
            if len(lines) < 27:
                return None, None

            # Get the first word from line 27
            word_line_27 = lines[26].split()[0] if lines[26].split() else ""
            
            # Find the first word from the last non-empty line
            last_non_empty_line_word = lines[len(lines)-3].split()[0] if lines[len(lines)-3].split() else ""
            
            # Convert to integer if the word is numeric
            word_line_27 = int(word_line_27) if word_line_27.isdigit() else word_line_27
            last_non_empty_line_word = int(last_non_empty_line_word) if last_non_empty_line_word.isdigit() else last_non_empty_line_word
            
            return word_line_27, last_non_empty_line_word
    except Exception as e:
        print(f"Error reading {file_path}: {e}")
        return None, None

def main(folder_path, output_excel):
    # Create a list to hold the data
    data = []

    # Loop through all .txt files in the folder
    for filename in os.listdir(folder_path):
        if filename.endswith(".txt"):
            file_path = os.path.join(folder_path, filename)
            line_27_word, last_non_empty_line_word = extract_words_from_file(file_path)

            # Append data if words were found
            if line_27_word is not None and last_non_empty_line_word is not None:
                data.append({
                    "File Name": filename,
                    "Worst HoldSlack(WNS)": line_27_word,
                    "Worst Negative Slack(WNS)": last_non_empty_line_word
                })

    # Create a DataFrame and write it to an Excel file
    df = pd.DataFrame(data)
    df["Line 27 Word"] = pd.to_numeric(df["Line 27 Word"], errors="ignore")
    df["Last Non-Empty Line Word"] = pd.to_numeric(df["Last Non-Empty Line Word"], errors="ignore")
    
    # Write the DataFrame to an Excel file
    df.to_excel(output_excel, index=False)
    print(f"Data has been saved to {output_excel}")

# Set folder path and output Excel file path
folder_path = "/home/daksh/Desktop/ASIC/VSDBabySoC/src/sta_output"
output_excel = "output_data.xlsx"

```

![image](https://github.com/user-attachments/assets/3b9d7b64-b269-4498-a248-dbd2da567c88)
![image](https://github.com/user-attachments/assets/312e4753-e6ec-452c-9a5d-87f5c37bdfc5)

![image](https://github.com/user-attachments/assets/4b3fe6c5-f542-41e2-88c7-b648e01d96e0)


## Task 12: Advanced Physical Design using OpenLane using Sky130

#### Day-1: Inception of open-source EDA, OpenLane and Sky130 PDK

**QFN-48 Package:** A Quad Flat No-leads (QFN) 48 package is a leadless IC package with 48 connection pads around the perimeter. It offers good thermal and electrical performance in a compact form, making it ideal for high-density applications.

![image](https://github.com/user-attachments/assets/2237a9ef-dc38-444f-97a5-ceb4e983c8f0)


**Chip:** An integrated circuit (IC) that contains various functional blocks like memory, processing units, and I/O in a silicon substrate, typically used for specific applications in electronics.

![image](https://github.com/user-attachments/assets/7503bcea-4654-4bc9-b77a-de9ced7929be)


**Pads:** Small metallic areas on a chip or package used to connect internal circuitry to external connections, enabling signals to be transferred to and from the IC.

**Core:** The central part of a chip containing the main processing unit and functional logic, often optimized for power and performance.

**Die:** The section of a silicon wafer containing an individual IC before it is packaged, housing all active circuits and elements for the chip's functions.

![image](https://github.com/user-attachments/assets/cfa2c482-59c5-4ad5-b74c-a027bfdf16b8)


**IPs (Intellectual Properties):** Pre-designed functional blocks or modules within a chip, such as USB controllers or memory interfaces, licensed for reuse across various designs to save time and cost.

![image](https://github.com/user-attachments/assets/1039a7d9-0286-42c7-8da7-17136c3da5aa)


**From Software Applications to Hardware Flow**

To run an application on hardware, several processes take place. First, the application enters a layer known as the system software, which prepares it for execution by translating the application program into binary format, understandable by hardware. Key components within system software include the Operating System (OS), Compiler, and Assembler.

The process starts with the OS, which breaks down application functions written in high-level languages such as C, C++, Java, or Visual Basic. These functions are passed to a suitable compiler, which translates them into low-level instructions. The syntax and format of these instructions are tailored to the specific hardware architecture in use.

Next, the assembler converts these hardware-specific instructions into binary format, known as machine language. This binary code is then fed to the hardware, enabling it to perform specific tasks as defined by the received instructions.

![image](https://github.com/user-attachments/assets/14788472-7c91-4860-82c6-2bccb49f91d2)

For example, consider a stopwatch app running on a RISC-V core. Here, the OS might generate a small function in C, which is then passed to a compiler. The compiler outputs RISC-V-specific instructions, tailored to the architecture. These instructions are subsequently processed by the assembler, which converts them into binary code. This binary code then flows into the chip layout, where the hardware executes the desired functionality.

![image](https://github.com/user-attachments/assets/cabbd8f7-f0c4-4a13-bf2e-d6d67392910e)

For the above stopwatch the below figure shows the input and output of the compiler and assembler.

![image](https://github.com/user-attachments/assets/63f2e771-eed8-4953-b501-dd9ff0d209f7)

The compiler generates architecture-specific instructions, while the assembler produces the corresponding binary patterns. To execute these instructions on hardware, an RTL (written in a Hardware Description Language) is used to interpret and implement the instructions. This RTL design is then synthesized into a netlist, represented as interconnected logic gates. Finally, the netlist undergoes physical design implementation to be fabricated onto the chip.

![image](https://github.com/user-attachments/assets/ae0a287d-8bb4-4535-b6b0-5b1baf09008d)

**Components of ASIC Design**

![image](https://github.com/user-attachments/assets/560b728b-8e75-4c9d-a3c8-5b640318c4aa)

- RTL IPs: Pre-designed, verified digital circuit blocks (like adders, flip-flops, memory) in HDL (e.g., Verilog, VHDL). They save design time by providing ready-to-use components for complex circuits.

- EDA Tools: Software that automates ASIC design tasks (e.g., synthesis, optimization, placement, timing analysis). Essential for improving productivity and ensuring performance and power requirements are met.

- PDK Data: A set of files and parameters from a semiconductor foundry, detailing its manufacturing process (e.g., transistor models, design rules). PDKs ensure ASIC designs are compatible with the foundry’s fabrication process.

**Simplified RTL to GDS flow**

![image](https://github.com/user-attachments/assets/1f2a5455-d83e-46fe-92e3-531ade2a9add)

- **RTL Design:** Describes the circuit's functional behavior using HDLs like Verilog or VHDL, defining its logic and data paths.

- **RTL Synthesis:** Converts RTL code to a gate-level netlist which is a collection of standard cells like AND gates, flip-flops, and multiplexers by mapping it to standard cells and optimizing for area, power, and timing. 

- **Floor and Power Planning:** Partitions chip area, places major components, and defines power grid and I/O placement to optimize area, power distribution, and signal flow. This step optimizes the physical layout, aiming to reduce power consumption and improve signal integrity by considering the placement of I/O pads and power distribution cells

- **Placement:** Assigns physical locations to cells, aiming to minimize wirelength, reduce signal delay, and meet design constraints. The placement tool carefully arranges the cells to balance the overall chip design for optimal performance and area utilization.

- **Clock Tree Synthesis (CTS):** Clock Tree Synthesis (CTS) is a critical step that focuses on creating an optimized clock distribution network. CTS ensures the clock is distributed evenly to all flip-flops and registers. It builds an optimized clock network to balance clock signal distribution and reduce clock skew.

- **Routing:** Connects components based on placement, optimizing wire paths to ensure signal integrity, minimize congestion, and meet design rules.

- **Sign-off:** Final verification stage, ensuring the design meets functionality, performance, power, and reliability targets. Timing analysis is performed to check setup and hold times, power analysis ensures the design doesn’t exceed power limits, and physical verification checks ensure that the layout meets manufacturing rules. This stage confirms the design is ready for fabrication.

- **GDSII File Generation:** Creates the GDSII file containing the complete layout details needed for chip fabrication. This file represents the final physical design and is used by manufacturers to create the photomasks required for chip production. The GDSII file serves as the blueprint for the actual fabrication of the chip.

**OpenLane ASIC Flow:**

![image](https://github.com/user-attachments/assets/cdd04b14-fbfe-44a3-8d4e-8fbfe443bd74)

1. RTL Synthesis, Technology Mapping, and Formal Verification: The tools used are Yosys (for RTL synthesis), ABC (for technology mapping and formal verification).
2. Static Timing Analysis: The tools used are OpenSTA (for static timing analysis).
3. Floor Planning: The tools used are init_fp (initial floorplanning), ioPlacer (I/O placement), pdn (power distribution network planning), tapcell (tap cell insertion).
4. Placement: The tools used are RePLace (global placement), Resizer (optional for resizing cells), OpenPhySyn (formerly used for placement), OpenDP (detailed placement).
5. Clock Tree Synthesis: The tools used are TritonCTS (for clock tree synthesis).
6. Fill Insertion: The tools used are OpenDP (for filler placement).
7. Routing: The tools used for global routing are FastRoute or CU-GR (formerly used) and for the detailed routing , we use TritonRoute (for detailed routing) or DR-CU (formerly used).
8. SPEF Extraction: The tools used are OpenRCX (or SPEF-Extractor, formerly used) for Standard Parasitic Exchange Format (SPEF) extraction.
9. GDSII Streaming Out: The tools used are Magic and KLayout (for viewing and editing GDSII files).
10. Design Rule Checking (DRC) Checks: The tools used are Magic and KLayout (for DRC checks).
11. Layout vs. Schematic (LVS) Check: The tools used are Netgen (for LVS checks).
12. Antenna Checks: The tools used are Magic (for antenna checks).

**OpenLANE Directory structure**

``` 
├── OOpenLane             -> directory where the tool can be invoked (run docker first)
│   ├── designs          -> All designs must be extracted from this folder
│   │   │   ├── picorv32a -> Design used as case study for this workshop
│   |   |   ├── ...
|   |   ├── ...
├── pdks                 -> contains pdk related files 
│   ├── skywater-pdk     -> all Skywater 130nm PDKs
│   ├── open-pdks        -> contains scripts that makes the commerical PDK (which is normally just compatible to commercial tools) to also be compatible with the open-source EDA tool
│   ├── sky130A          -> pdk variant made especially compatible for open-source tools
│   │   │  ├── libs.ref  -> files specific to node process (timing lib, cell lef, tech lef) for example is `sky130_fd_sc_hd` (Sky130nm Foundry Standard Cell High Density)  
│   │   │  ├── libs.tech -> files specific for the tool (klayout,netgen,magic...) 
```

**Synthesis in Openlane:**

Go to VSD Virtual Box and run the following commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis

```

![Screenshot from 2024-11-13 01-48-36](https://github.com/user-attachments/assets/b98834e8-b246-4021-bd78-c8563d417c6a)
![Screenshot from 2024-11-13 01-51-07](https://github.com/user-attachments/assets/ea38179a-fbbd-4b84-90ae-2be1957f4ed6)

To view the netlist:

```
cd designs/picorv32a/runs/12-11_20-18/results/synthesis/
gedit picorv32a.synthesis.v
```
![Screenshot from 2024-11-13 01-55-01](https://github.com/user-attachments/assets/46d250b4-ab3d-497c-9f51-2e876648dfc2)
Netlist code:
![Screenshot from 2024-11-13 01-55-29](https://github.com/user-attachments/assets/8af89791-93bb-4755-aa5f-dae7c7e85100)

To view the yosys report:

```
cd ../..
cd reports/synthesis
gedit 1-yosys_4.stat.rpt
```
![Screenshot from 2024-11-13 01-56-52](https://github.com/user-attachments/assets/d2da3c7a-519e-4165-91ae-5ec90ce6d681)


Report:

```
28. Printing statistics.

=== picorv32a ===

   Number of wires:              14596
   Number of wire bits:          14978
   Number of public wires:        1565
   Number of public wire bits:    1947
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:              14876
     sky130_fd_sc_hd__a2111o_2       1
     sky130_fd_sc_hd__a211o_2       35
     sky130_fd_sc_hd__a211oi_2      60
     sky130_fd_sc_hd__a21bo_2      149
     sky130_fd_sc_hd__a21boi_2       8
     sky130_fd_sc_hd__a21o_2        57
     sky130_fd_sc_hd__a21oi_2      244
     sky130_fd_sc_hd__a221o_2       86
     sky130_fd_sc_hd__a22o_2      1013
     sky130_fd_sc_hd__a2bb2o_2    1748
     sky130_fd_sc_hd__a2bb2oi_2     81
     sky130_fd_sc_hd__a311o_2        2
     sky130_fd_sc_hd__a31o_2        49
     sky130_fd_sc_hd__a31oi_2        7
     sky130_fd_sc_hd__a32o_2        46
     sky130_fd_sc_hd__a41o_2         1
     sky130_fd_sc_hd__and2_2       157
     sky130_fd_sc_hd__and3_2        58
     sky130_fd_sc_hd__and4_2       345
     sky130_fd_sc_hd__and4b_2        1
     sky130_fd_sc_hd__buf_1       1656
     sky130_fd_sc_hd__buf_2          8
     sky130_fd_sc_hd__conb_1        42
     sky130_fd_sc_hd__dfxtp_2     1613
     sky130_fd_sc_hd__inv_2       1615
     sky130_fd_sc_hd__mux2_1      1224
     sky130_fd_sc_hd__mux2_2         2
     sky130_fd_sc_hd__mux4_1       221
     sky130_fd_sc_hd__nand2_2       78
     sky130_fd_sc_hd__nor2_2       524
     sky130_fd_sc_hd__nor2b_2        1
     sky130_fd_sc_hd__nor3_2        42
     sky130_fd_sc_hd__nor4_2         1
     sky130_fd_sc_hd__o2111a_2       2
     sky130_fd_sc_hd__o211a_2       69
     sky130_fd_sc_hd__o211ai_2       6
     sky130_fd_sc_hd__o21a_2        54
     sky130_fd_sc_hd__o21ai_2      141
     sky130_fd_sc_hd__o21ba_2      209
     sky130_fd_sc_hd__o21bai_2       1
     sky130_fd_sc_hd__o221a_2      204
     sky130_fd_sc_hd__o221ai_2       7
     sky130_fd_sc_hd__o22a_2      1312
     sky130_fd_sc_hd__o22ai_2       59
     sky130_fd_sc_hd__o2bb2a_2     119
     sky130_fd_sc_hd__o2bb2ai_2     92
     sky130_fd_sc_hd__o311a_2        8
     sky130_fd_sc_hd__o31a_2        19
     sky130_fd_sc_hd__o31ai_2        1
     sky130_fd_sc_hd__o32a_2       109
     sky130_fd_sc_hd__o41a_2         2
     sky130_fd_sc_hd__or2_2       1088
     sky130_fd_sc_hd__or2b_2        25
     sky130_fd_sc_hd__or3_2         68
     sky130_fd_sc_hd__or3b_2         5
     sky130_fd_sc_hd__or4_2         93
     sky130_fd_sc_hd__or4b_2         6
     sky130_fd_sc_hd__or4bb_2        2

   Chip area for module '\picorv32a': 147712.918400
```

```
Flop ratio = Number of D Flip flops = 1613  = 0.1084
             ______________________   _____
             Total Number of cells    14876
```


#### Day-2: Good floorplan vs bad floorplan and introduction to library cells

**Utilization Factor and Aspect Ratio**: In IC floor planning, utilization factor and aspect ratio are key parameters. The utilization factor is the ratio of the area occupied by the netlist to the total core area. While a perfect utilization of 1 (100%) is ideal, practical designs target a factor of 0.5 to 0.6 to allow space for buffer zones, routing channels, and future adjustments. The aspect ratio, defined as height divided by width, indicates the chip’s shape; an aspect ratio of 1 denotes a square, while other values result in a rectangular layout. The aspect ratio is chosen based on functional, packaging, and manufacturing needs.

```
Utilisation Factor =  Area occupied by netlist
                     __________________________
                         Total area of core
                         

Aspect Ratio =  Height
               ________
                Width
```

**Pre-placed cells** : Pre-placed cells are essential functional blocks, such as memory, custom processors, and analog circuits, positioned manually in fixed locations. These blocks are crucial for the chip’s performance and remain fixed during placement and routing to preserve their functionality and layout integrity.

**Decoupling Capacitors** : Decoupling capacitors are placed near logic circuits to stabilize power supply voltages during transient events. Acting as local energy reserves, they help reduce voltage fluctuations, crosstalk, and electromagnetic interference (EMI), ensuring reliable power delivery to sensitive circuits.

**Power Planning**: A robust power planning strategy includes creating a power and ground mesh to distribute VDD and VSS evenly across the chip. This setup ensures stable power delivery, minimizes voltage drops, and improves overall efficiency. Multiple power and ground points reduce the risk of instability and voltage drop issues, supporting the design’s power needs effectively.

**Pin Placement**: Pin placement (I/O planning) is crucial for functionality and reliability. Strategic pin assignment minimizes signal degradation, preserves data integrity, and helps manage heat dissipation. Proper positioning of power and ground pins supports thermal management and enhances signal strength, contributing to overall system stability and manufacturability.

Floorplaning using OpenLANE:

Run the following commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```

```
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
run_floorplan
```
![Screenshot from 2024-11-13 02-00-48](https://github.com/user-attachments/assets/0b008d26-b710-4782-ab1a-2fc859f868d8)

![Screenshot from 2024-11-13 02-01-08](https://github.com/user-attachments/assets/c61286b7-9665-4a48-a410-3a7d407c786b)

![Screenshot from 2024-11-13 02-01-40](https://github.com/user-attachments/assets/2bb311b1-5223-4fd6-afa9-fc07ec0a1ae4)

Now, run the below commands in a new terminal:

```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/12-11_20-30/results/floorplan/
gedit picorv32a.floorplan.def
```
![Screenshot from 2024-11-13 02-04-10](https://github.com/user-attachments/assets/b96b2916-f0a9-4049-b877-b2dcdd18ee09)

According to floorplan definition:

1000 Unit Distance = 1 Micron  

Die width in unit distance = 660685−0 = 660685 

Die height in unit distance = 671405−0 = 671405   

​Die width in microns = 660685/1000 = 660.685 Microns  

Die height in microns = 671405/1000 = 671.405 Microns  

Area of die in microns = 660.685 × 671.405 = 443587.212425 Square Microns

To view the floorplan in magic. Open a new terminal and run the below commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/12-11_20-30/results/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
![Screenshot from 2024-11-13 02-06-37](https://github.com/user-attachments/assets/eeadf620-6c88-46f9-9fbb-61e12389fc08)
Decap and Tap Cells:

![Screenshot from 2024-11-13 02-12-53](https://github.com/user-attachments/assets/cbac4ace-c34e-43ab-95b6-737a3975d90e)

Unplaces standard cells at origin:

![Screenshot from 2024-11-13 02-13-52](https://github.com/user-attachments/assets/6a7fc021-fcc3-499d-8235-5e565ee01726)

Command to run placement:

```
run_placement
```
![Screenshot from 2024-11-13 02-17-48](https://github.com/user-attachments/assets/e5f969fc-fb85-46f5-9713-48d93b17fc27)
To view the placement in magic:

```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/17-03_12-06/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![Screenshot from 2024-11-13 02-19-52](https://github.com/user-attachments/assets/20f4168d-e94d-4264-b2a2-8a2e7ba02fcd)
![Screenshot from 2024-11-13 02-19-23](https://github.com/user-attachments/assets/ed81d5ce-87bc-443b-a64e-b80e6f137fa9)
**Cell design and Characterization Flow**

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.

Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

**Standard Cell Characterization Flow**

A typical standard cell characterization flow that is followed in the industry includes the following steps:

- Read in the models and tech files
- Read extracted spice Netlist
- Recognise behavior of the cells
- Read the subcircuits
- Attach power sources
- Apply stimulus to characterization setup
- Provide neccesary output capacitance loads
- Provide neccesary simulation commands
- Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models. These .libs are classified as Timing characterization, power characterization and noise characterization.

**Timing parameters**

| Timing definition | Value |
|---|---|
| slew_low_rise_thr | 20% value |
| slew_high_rise_thr | 80% value |
| slew_low_fall_thr | 20% value |
| slew_high_fall_thr | 80% value |
| in_rise_thr | 50% value |
| in_fall_thr | 50% value |
| out_rise_thr | 50% value |
| out_fall_thr | 50% value |

**Propagation Delay**: It refers to the time it takes for a change in an input signal to reach 50% of its final value to produce a corresponding change in the output signal to reach 50% of its final value of a digital circuit.

```
rise delay =  time(out_fall_thr) - time(in_rise_thr)
```

**Transistion time**: The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.

```
Fall transition time: time(slew_high_fall_thr) - time(slew_low_fall_thr)
Rise transition time: time(slew_high_rise_thr) - time(slew_low_rise_thr)
```

#### Day-3: Design library cell using Magic Layout and ngspice characterization
**CMOS inverter ngspice simulations**

Creating a SPICE Deck for a CMOS Inverter Simulation

- Netlist Creation: Define the component connections (netlist) for a CMOS inverter circuit. Ensure each node is labeled appropriately for easy identification in the SPICE simulation. Typical nodes include input, output, ground, and supply nodes.
- Device Sizing: Specify the Width-to-Length (W/L) ratios for both the PMOS and NMOS transistors.For proper operation, the PMOS width should be larger than the NMOS width, usually 2x to 3x, to balance the drive strength
- Voltage Levels: Set gate and supply voltages, often in multiples of the transistor length. 
- Node Naming: Assign node names to each connection point around the components to clearly identify each element in the SPICE netlist (e.g., VDD, GND, IN, OUT). This helps SPICE recognize each component and simulate the circuit effectively.
  
![image](https://github.com/user-attachments/assets/b61efcf4-cd1f-4080-b4dc-4606afc3a2e5)


```
***syntax for PMOS and NMOS desription***
[component name] [drain] [gate] [source] [substrate] [transistor type] W=[width] L=[length]

 ***simulation commands***
.op --- is the start of SPICE simulation operation where Vin sweeps from 0 to 2.5 with 0.5 steps
tsmc_025um_model.mod  ----  model file which contains the technological parameters for the 0.25um NMOS and PMOS 
```
Commands to simulate in SPICE:

```
source [filename].cir
run
setplot 
dc1 
plot out vs in 
```

![image](https://github.com/user-attachments/assets/49f1ed28-c601-4954-a3aa-077a2c650650)

The switching threshold Vm is like a critical voltage level for a component called a CMOS inverter. It's the point at which this inverter switches between sending out a "0" or a "1" in a computer chip. This the point where both PMOS and NMOS is in saturation or kind of turned on, and leakage current is high. If PMOS is thicker than NMOS, the CMOS will have higher switching threshold (1.2V vs 1V) while threshold will be lower when NMOS becomes thicker.

At this point, both the transistors are in saturation region, means both are turned on and have high chances of current flowing directly from VDD to Ground called Leakage current.

To find the switching threshold

```
Vin in 0 2.5
*** Simulation Command ***
.op
.dc Vin 0 2.5 0.05
```
![image](https://github.com/user-attachments/assets/61d07ded-adf6-4b6d-8e79-936512557edd)

Transient analysis is used for finding propagation delay. SPICE transient analysis uses pulse input shown below:

![image](https://github.com/user-attachments/assets/af0c7120-e946-4c8f-95c2-c66b130ef415)


Now, we clone the custom inverter

```
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
ls
magic -T sky130A.tech sky130_inv.mag &
```
![Screenshot from 2024-11-13 02-26-33](https://github.com/user-attachments/assets/38091c9a-c78c-435c-ad01-9c3fb445009b)


**Inception of Layout CMOS fabrication process**

The 16-mask CMOS design fabrication process:

1. Substrate Preparation: The process begins with preparing a silicon wafer as the foundational substrate for the circuit.
2. N-Well Formation: The N-well regions are created on the substrate by introducing impurities, typically phosphorus, through ion implantation or diffusion
3. P-Well Formation: Similar to the N-well formation, P-well regions are created using ion implantation or diffusion with boron or other suitable dopants.
4. Gate Oxide Deposition: A thin silicon dioxide layer is deposited to form the gate oxide, which insulates the gate from the channel.
5. Poly-Silicon Deposition: A layer of polysilicon is deposited on the gate oxide to serve as the gate electrode.
6. Poly-Silicon Masking and Etching: A photoresist mask defines areas where polysilicon should remain, and etching removes exposed portions.
7. N-Well Masking and Implantation: A photoresist mask is used to define the areas where the N-well regions should be preserved. Phosphorus or other suitable impurities are then implanted into the exposed regions.
8. P-Well Masking and Implantation: Similarly, a photoresist mask is used to define the areas where the P-well regions should be preserved. Boron or other suitable impurities are implanted into the exposed regions.
9. Source/Drain Implantation: Using photoresist masks, dopants are implanted to create source and drain regions (e.g., arsenic for NMOS, boron for PMOS).
10. Gate Formation: The gate electrode is defined by etching the poly-silicon layer using a photoresist mask.
11. Source/Drain Masking and Etching: A photoresist mask is applied to define the source and drain regions followed by etching to remove the oxide layer in those areas.
12. Contact/Via Formation: Contact holes or vias are etched through the oxide layer to expose the underlying regions, such as the source/drain regions or poly-silicon gates.
13. Metal Deposition: A layer of metal, typically aluminum or copper, is deposited on the wafer surface to form the interconnects.
14. Metal Masking and Etching: A photoresist mask is used to define the metal interconnects, and etching is performed to remove the exposed metal, leaving behind the desired interconnect patterns.
15. Passivation Layer Deposition: A protective layer, often made of silicon dioxide or nitride, is deposited to isolate and shield the metal interconnects.
16. Final Testing and Packaging: The fabricated wafer undergoes rigorous testing to ensure the functionality of the integrated circuits. The working chips are then separated, packaged, and prepared for use in various electronic devices.

Inverter layout:

Identify PMOS:

![Screenshot from 2024-11-13 02-33-42](https://github.com/user-attachments/assets/f6d64e00-120a-4551-b8bb-7aadfdf1718d)

Identify NMOS:
![Screenshot from 2024-11-13 02-34-35](https://github.com/user-attachments/assets/549df076-a006-400b-8835-db6689442345)
Identify Y:
![Screenshot from 2024-11-13 02-37-56](https://github.com/user-attachments/assets/ea0692d8-af29-45a0-bc54-371c6aa152bd)

PMOS source connected to VDD:
![Screenshot from 2024-11-13 02-39-32](https://github.com/user-attachments/assets/038410bc-d538-4a4c-bdf3-d205205d10d3)
NMOS source connected to VSS:

![Screenshot from 2024-11-13 02-40-46](https://github.com/user-attachments/assets/6121d664-4485-4c6e-8c29-056b0f42e403)
Spice extraction of inverter in Magic. Run these in the tkcon window:

```
# Check current directory
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
![Screenshot from 2024-11-13 02-43-44](https://github.com/user-attachments/assets/be99783e-9239-4d15-bfdb-50f0a764a87d)
To view the spice file:
![Screenshot from 2024-11-13 02-45-33](https://github.com/user-attachments/assets/3372bca2-3ff6-4930-a8de-a15c13682f57)
The contents of spice file:

```
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=10n

.subckt sky130_inv A Y VPWR VGND
X0 Y A VGND VGND sky130_fd_pr__nfet_01v8 ad=1.37n pd=0.148m as=1.37n ps=0.148m w=35 l=23
X1 Y A VPWR VPWR sky130_fd_pr__pfet_01v8 ad=1.44n pd=0.152m as=1.52n ps=0.156m w=37 l=23
C0 VPWR Y 0.11fF
C1 A Y 0.754fF
C2 A VPWR 0.277fF
C3 Y VGND 0.279fF
C4 A VGND 0.45fF
C5 VPWR VGND 0.781fF
.ends

```

Now modify the `sky130_inv.spice` file to find the transient respone:

```
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
M1000 Y A VGND VGND nshort_model.0 w=35 l=23
+  ad=1.44n pd=0.152m as=1.37n ps=0.148m
M1001 Y A VPWR VPWR pshort_model.0 w=37 l=23
+  ad=1.44n pd=0.152m as=1.52n ps=0.156m

VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

C0 A VPWR 0.0774f
C1 VPWR Y 0.117f
C2 A Y 0.0754f
C3 Y VGND 2f
C4 A VGND 0.45f
C5 VPWR VGND 0.781f
//.ends

.tran 1n 20n
.control
run
.endc
.end
```

Now, simulate the spice netlist
```
ngspice sky130_inv.spice
```

![Screenshot from 2024-11-13 02-49-55](https://github.com/user-attachments/assets/9235f4f1-ee69-4626-9916-331576c333f5)

To plot the waveform:

```
plot y vs time a
```

![Screenshot from 2024-11-13 02-50-44](https://github.com/user-attachments/assets/8974cdfe-e2af-45a6-8aa2-fa71956b2db8)

20% Screenshots
![Screenshot from 2024-11-13 23-40-24](https://github.com/user-attachments/assets/0d17580e-b1a0-49af-bba8-75a6852d41e2)
80% Screenshot:
![Screenshot from 2024-11-13 23-43-17](https://github.com/user-attachments/assets/7a898b5f-57a2-4b2f-8f4b-bc13b45416f2)

 R i s e   t r a n s i t i o n   t i m e = 2.24638 − 2.18242 = 0.06396   n s = 63.96   p s 

Fall Time:</br>
20%:

![Screenshot from 2024-11-13 23-45-21](https://github.com/user-attachments/assets/f3f90594-76b7-44ac-8e27-bb313277efc5)

80%:
![Screenshot from 2024-11-13 23-46-12](https://github.com/user-attachments/assets/272356c2-ce69-49a1-ba08-ec24279e66c6)

Using this transient response, we will now characterize the cell's slew rate and propagation delay:

Rise Transition: Time taken for the output to rise from 20% to 80% of max value
Fall Transition: Time taken for the output to fall from 80% to 20% of max value
Cell Rise delay: difference in time(50% output rise) to time(50% input fall)
Cell Fall delay: difference in time(50% output fall) to time(50% input rise)

```
Rise Transition : 2.24638 - 2.18242 =  0.06396 ns = 63.96 ps
Fall Transition : 4.0955 - 4.05536 =  0.0419 ns = 41.9 ps
Cell Rise Delay : 2.21144 - 2.15008 = 0.06136 ns = 61.36 ps
Cell Fall Delay : 4.07807 - 4.05 =0.02 ns = 20 ps
```

Magic Tool options and DRC Rules:

Now, go to home directory and run the below commands:

```
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
ls -al
gvim .magicrc
magic -d XR &
```
![Screenshot from 2024-11-13 02-52-56](https://github.com/user-attachments/assets/fcf4477b-840a-45cc-8d08-949eda44ce52)
First load the poly file by load poly.mag on tkcon window.


![Screenshot from 2024-11-13 02-59-35](https://github.com/user-attachments/assets/26f6656d-1cd4-4cdb-a1c3-6a91090e66a0)

We can see that Poly.9 is incorrect.

Add the below commands in the sky130A.tech
![Screenshot from 2024-11-13 03-07-51](https://github.com/user-attachments/assets/e34cd3b8-69f8-43ea-bd46-d04abc031433)
![Screenshot from 2024-11-13 03-09-53](https://github.com/user-attachments/assets/0c50292b-2242-4de0-8438-735880d28459)

Run the commands in tkcon window:

```
tech load sky130A.tech
drc check
drc why
```
![Screenshot from 2024-11-13 03-13-29](https://github.com/user-attachments/assets/1178f4be-e04a-4e53-9a43-0ffea31cfbc5)

#### Day-4: Pre-layout timing analysis and importance of good clock tree

Commands to extract `tracks.info` file:

```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
cd ../../pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/
less tracks.info
```
![Screenshot from 2024-11-13 22-35-39](https://github.com/user-attachments/assets/e5394793-cabe-4563-bebb-c2f10fc53ded)

Commands for tkcon window to set grid as tracks of locali layer

```
grid 0.46um 0.34um 0.23um 0.17um
```

![Screenshot from 2024-11-13 22-42-56](https://github.com/user-attachments/assets/79a33c96-f58c-4310-8144-a4fd8331167a)
The grids show where the routing for the local-interconnet layer can only happen, the distance of the grid lines are the required pitch of the wire. Below, we can see that the guidelines are satisfied:

![Screenshot from 2024-11-13 22-44-05](https://github.com/user-attachments/assets/49abe3f9-ef63-489e-961d-6946060ce606)

Now, save it by giving a custon mae

```
save sky130_dakinv.mag
```
![Screenshot from 2024-11-13 23-02-46](https://github.com/user-attachments/assets/cd62bc6c-c3c5-4b67-9635-a5ed786206f4)

Now, open it by using the following commands:

```
magic -T sky130A.tech sky130_akainv.mag &
```
![Screenshot from 2024-11-13 23-06-11](https://github.com/user-attachments/assets/1cf07ed8-700f-4d0b-94c1-d2769304092d)



Now, type the following command in tkcon window:

```
lef write
```
![Screenshot from 2024-11-13 23-07-01](https://github.com/user-attachments/assets/dbb7040e-c5a1-4782-b423-b655f42b7ff7)
![Screenshot from 2024-11-13 23-07-45](https://github.com/user-attachments/assets/39351555-7701-4a2b-9521-a3802b6f10d9)


Modify config.tcl in /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a :


```
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILES) "./designs/picorv32a/src/picorv32a.sdc"


set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT) 


set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib "
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib "
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]   ## this is the new line added to the existing config.tcl file

set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1 } {
  source $filename
}
```

![Screenshot from 2024-11-13 22-53-56](https://github.com/user-attachments/assets/8310e73e-4cae-415f-a03d-fdad70677627)


Commands to copy necessary files to 'picorv32a' design 'src' directory
```
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```
Screenshot of commands run
![Screenshot from 2024-11-13 23-09-54](https://github.com/user-attachments/assets/d6e1faa1-d8ca-492a-a818-4a97b168ec1b)


Now, run openlane flow synthesis:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```

```
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```

![Screenshot from 2024-11-14 00-36-35](https://github.com/user-attachments/assets/1c0eb56e-f95f-463e-837a-54629c74f353)
![Screenshot from 2024-11-14 00-38-15](https://github.com/user-attachments/assets/91316e7e-8aa8-40a7-8475-6332125f4dfb)
![Screenshot from 2024-11-14 00-39-28](https://github.com/user-attachments/assets/c0e16c99-e7be-47c7-8d38-3ec036001921)
![Screenshot from 2024-11-14 00-58-04](https://github.com/user-attachments/assets/b31aa65b-95ad-4b3e-b877-6ac385225470)
![Screenshot from 2024-11-14 00-58-18](https://github.com/user-attachments/assets/7594e8b8-dfb4-4dff-8cdc-6f3df308876a)


**Delay Tables**

Delay plays a crucial role in cell timing, impacted by input transition and output load. Cells of the same type can have different delays depending on wire length due to resistance and capacitance variations. To manage this, "delay tables" are created, using 2D arrays with input slew and load capacitance for each buffer size as timing models. Algorithms compute buffer delays from these tables, interpolating where exact data isn’t available to estimate delays accurately, preserving signal integrity across varying load conditions.

![image](https://github.com/user-attachments/assets/095a59e1-158c-4870-88e3-b73cb3a3692c)

Fixing slack:

```
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a -tag 24-03_10-03 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
echo $::env(SYNTH_STRATEGY)
set ::env(SYNTH_STRATEGY) "DELAY 3"
echo $::env(SYNTH_BUFFERING
echo $::env(SYNTH_SIZING)
set ::env(SYNTH_SIZING) 1
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
![Screenshot from 2024-11-14 01-00-45](https://github.com/user-attachments/assets/c14ceb4d-e9d0-45d8-9c92-f113a5e6fa83)
![Screenshot from 2024-11-14 01-01-37](https://github.com/user-attachments/assets/fdedfb0d-daea-4c07-8752-b8d628d798af)
Now, run floorplan

```
run_floorplan
```
![Screenshot from 2024-11-14 01-02-14](https://github.com/user-attachments/assets/fc6c2a46-8fa5-4bfa-a916-6661a0775a92)

Since we are facing unexpected un-explainable error while using run_floorplan command, we can instead use the following set of commands available based on information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and also based on Floorplan Commands section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`

```
init_floorplan
place_io
tap_decap_or
```
![Screenshot from 2024-11-14 01-05-13](https://github.com/user-attachments/assets/9a2b1184-a7ca-4792-ab6b-89bc5103ad89)
![Screenshot from 2024-11-14 01-05-19](https://github.com/user-attachments/assets/1cd88896-d1a4-411d-bd43-8fe7dfa8511d)

Now, do placement

```
run_placement
```
![Screenshot from 2024-11-14 01-06-49](https://github.com/user-attachments/assets/f005f318-8243-4c71-bcca-9a6e6606227f)
Now, open a new terminal and run the below commands to load placement def in magic

```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &

```
![Screenshot from 2024-11-14 01-07-43](https://github.com/user-attachments/assets/2fa8eec7-0171-4ec2-ac7f-6fa06f1f5d39)

Custom inverter inserted in placement def



Command for tkcon window to view internal layers of cells
```
# Command to view internal connectivity layers
expand
```
Abutment of power pins with other cell from library clearly visible

**Timing analysis with ideal clocks using openSTA**

Pre-layout STA will include effects of clock buffers and net-delay due to RC parasitics (wire delay will be derived from PDK library wire model).

![image](https://github.com/user-attachments/assets/a74af227-70dd-4812-930d-b6e9e787a27f)

Since we are getting 0 wns after improved timing run, we will be doing the timing analysis on initial run of synthesis which has lots of violations and no parameters added to improve timing.

Commands to invoke the OpenLANE flow include new lef and perform synthesis:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
run_synthesis
```
![Screenshot from 2024-11-14 02-56-40](https://github.com/user-attachments/assets/a4b78ab7-65d7-430a-886e-1db4ddab9de3)

Go, to `Desktop/work/tools/openlane_working_dir/openlane` and create a file `pre_sta.conf`. The contents of the file are:

```
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -max /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -min /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/synthesis/picorv32a.synthesis.v
link_design picorv32a
read_sdc /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```

Contents of `my_base.sdc`:

```
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 12.000
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv_8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.65
create_clock [get_ports $::env(CLOCK_PORT)]  -name $::env(CLOCK_PORT)  -period $::env(CLOCK_PERIOD)
set IO_PCT  0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
puts "\[INFO\]: Setting output delay to: $output_delay_value"
puts "\[INFO\]: Setting input delay to: $input_delay_value"


set clk_indx [lsearch [all_inputs] [get_port $::env(CLOCK_PORT)]]
#set rst_indx [lsearch [all_inputs] [get_port resetn]]
set all_inputs_wo_clk [lreplace [all_inputs] $clk_indx $clk_indx]
#set all_inputs_wo_clk_rst [lreplace $all_inputs_wo_clk $rst_indx $rst_indx]
set all_inputs_wo_clk_rst $all_inputs_wo_clk


# correct resetn
set_input_delay $input_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
#set_input_delay 0.0 -clock [get_clocks $::env(CLOCK_PORT)] {resetn}
set_output_delay $output_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]

# TODO set this as parameter
set_driving_cell -lib_cell $::env(SYNTH_DRIVING_CELL) -pin $::env(SYNTH_DRIVING_CELL_PIN) [all_inputs]
set cap_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0]
puts "\[INFO\]: Setting load to: $cap_load"
set_load  $cap_load [all_outputs]
```

Commands to run STA:

```
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```
![Screenshot from 2024-11-14 02-57-18](https://github.com/user-attachments/assets/39a415e3-f76c-479d-8660-8280db9b1fb4)
![Screenshot from 2024-11-14 02-57-32](https://github.com/user-attachments/assets/fea5bc0e-1a61-4474-b833-403eb2c1035e)
![Screenshot from 2024-11-14 02-59-21](https://github.com/user-attachments/assets/39ba5e81-4f92-4f6a-891e-a24fd1444c9f)

We now try to optimise synthesis.

Go to new terminal and run the following commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
prep -design picorv32a -tag 25-03_18-52 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 4
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
![Screenshot from 2024-11-14 02-59-58](https://github.com/user-attachments/assets/389e55a9-c8cf-4587-9e92-96d340e2c3cf)


Commands to run STA:

```
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```
![Screenshot from 2024-11-14 03-01-30](https://github.com/user-attachments/assets/13adf202-1da0-4c1b-a15d-042ddde393cf)

![Screenshot from 2024-11-14 03-00-53](https://github.com/user-attachments/assets/d7f11ecd-03af-44e5-9c90-796086eae52e)
**Basic timing ECO**

NOR gate of drive strength 2 is driving 5 fanouts
![Screenshot from 2024-11-14 03-02-45](https://github.com/user-attachments/assets/bfd8197b-70db-4afa-8283-0109a4e3b6d4)

Run the following commands to optimise timing:

```
report_net -connections _13285_
replace_cell _16145_ sky130_fd_sc_hd__nor3_4  
report_checks -fields {net cap slew input_pins} -digits 4
```

![Screenshot from 2024-11-14 03-27-30](https://github.com/user-attachments/assets/70375182-c110-4bb9-99b2-354a805b846b)

We can observe that the tns has reduced to -401.17 from -403.54 and wns has reduced to -5.49 from -5.59

**Clock tree synthesis TritonCTS and signal integrity**

Clock Tree Synthesis (CTS) techniques vary based on design needs:

- Balanced Tree CTS: Uses a balanced binary-like tree for equal path lengths, minimizing clock skew but with moderate power efficiency.
- H-tree CTS: Employs an "H"-shaped structure, good for large areas and power efficiency.

   ![image](https://github.com/user-attachments/assets/d1b13f19-a87f-41b6-8f29-a4e00a8e7216)

- Star CTS: Distributes the clock from a central point, minimizing skew but requiring more buffers near the source.
- Global-Local CTS: Combines star and tree topologies, with a global tree for clock domains and local trees within domains, balancing global and local timing.
- Mesh CTS: Uses a grid pattern ideal for structured designs, balancing simplicity and skew.
- Adaptive CTS: Dynamically adjusts based on timing and congestion, offering flexibility but with added complexity.

**Crosstalk**

Crosstalk is interference from overlapping electromagnetic fields between adjacent circuits, causing unwanted signals. In VLSI, it can lead to data corruption, timing issues, and higher power consumption. Mitigation strategies include optimized layout and routing, shielding, and clock gating to reduce dynamic power and minimize crosstalk effects.

![image](https://github.com/user-attachments/assets/21df4ac0-57aa-492e-adfa-7e04ce385680)

**Clock Net Shielding**

Clock net shielding prevents glitches by isolating the clock network, using shields connected to VDD or GND that don’t switch. It reduces interference by isolating clocks from other signals, often with dedicated routing layers and clock buffers. Additionally, clock domain isolation helps prevent cross-domain interference, avoiding metastability and maintaining synchronization.

![image](https://github.com/user-attachments/assets/bf85dd84-dc29-4962-877a-ce4f535bab2c)

Now to insert this updated netlist to PnR flow and we can use write_verilog and overwrite the synthesis netlist but before that we are going to make a copy of the old old netlist:

Run the following commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/
ls
cp picorv32a.synthesis.v picorv32a.synthesis_old.v
ls
```

Commands to write verilog:

```
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis.v
exit
```
![Screenshot from 2024-11-14 03-50-53](https://github.com/user-attachments/assets/7e704cf4-6a4e-4c8c-8b34-367063b315ae)

Verified that the netlist is overwritten
![Screenshot from 2024-11-14 03-51-59](https://github.com/user-attachments/assets/5432de09-cb74-425d-8424-2e5b047b1ad3)

Now, run the following commands:

```
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
prep -design picorv32a -tag 25-03_18-52 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_STRATEGY) "DELAY 3"
set ::env(SYNTH_SIZING) 1
run_synthesis
init_floorplan
place_io
tap_decap_or
run_placement
run_cts
```
![Screenshot from 2024-11-14 03-54-05](https://github.com/user-attachments/assets/6805fcac-b744-492d-9e10-dd89739372ab)
![Screenshot from 2024-11-14 03-54-27](https://github.com/user-attachments/assets/25777a35-d8fc-4fdf-ab4c-ec80eb54c854)
![Screenshot from 2024-11-14 03-55-25](https://github.com/user-attachments/assets/5b226734-3116-43ea-81a3-674a8c63b535)
![Screenshot from 2024-11-14 03-57-12](https://github.com/user-attachments/assets/6c9bee0e-e2a1-4808-a8b5-f687c67f80ee)


Now, enter the following commands for Post-CTS OpenROAD timing analysis:

```
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/25-03_18-52/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/25-03_18-52/results/cts/picorv32a.cts.def
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
exit
```![Screenshot from 2024-11-14 04-34-45](https://github.com/user-attachments/assets/e84a29b6-e01f-492a-bb29-32ae7afa87b3)
![Screenshot from 2024-11-14 04-34-41](https://github.com/user-attachments/assets/e783307b-c3df-4a31-b744-f3828eca4213)
![Screenshot from 2024-11-14 04-34-38](https://github.com/user-attachments/assets/500a8394-dbd9-4ffd-9401-13d07c316d0b)


![Screenshot from 2024-11-14 04-34-36](https://github.com/user-attachments/assets/d71cfe80-cc42-47fb-961e-8bd897214d73)


Now, enter the following commands for exploring post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST':

```
echo $::env(CTS_CLK_BUFFER_LIST)
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
echo $::env(CTS_CLK_BUFFER_LIST)
echo $::env(CURRENT_DEF)
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/25-03_18-52/results/placement/picorv32a.placement.def
run_cts
echo $::env(CTS_CLK_BUFFER_LIST)
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/25-03_18-52/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/25-03_18-52/results/cts/picorv32a.cts.def
write_db pico_cts1.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/25-03_18-52/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew transd net cap input_pins} -format full_clock_expanded -digits 4
report_clock_skew -hold
report_clock_skew -setup
exit
echo $::env(CTS_CLK_BUFFER_LIST)
set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd__clkbuf_1]
echo $::env(CTS_CLK_BUFFER_LIST)
```


![Screenshot from 2024-11-14 04-45-20](https://github.com/user-attachments/assets/1143b1b1-b009-49d7-9400-eeb8b1a6a507)
![Screenshot from 2024-11-14 04-46-12](https://github.com/user-attachments/assets/8b774272-2ce2-4395-9136-a5b161398f58)
![Screenshot from 2024-11-14 04-46-41](https://github.com/user-attachments/assets/6fc50431-2709-45fb-9fba-135eecdd4ce2)

## Day 5 - Final steps for RTL2GDS using tritonRoute and openSTA 	
#  Maze Routing and Lee's algorithm

 Routing is the process of establishing a physical connection between two pins. Algorithms designed for routing take source and target pins and aim to find the most efficient path between them, ensuring a valid connection exists.

The Maze Routing algorithm, such as the Lee algorithm, is one approach for solving routing problems. In this method, a grid similar to the one created during cell customization is utilized for routing purposes. The Lee algorithm starts with two designated points, the source and target, and leverages the routing grid to identify the shortest or optimal route between them.

The algorithm assigns labels to neighboring grid cells around the source, incrementing them from 1 until it reaches the target (for instance, from 1 to 7). Various paths may emerge during this process, including L-shaped and zigzag-shaped routes. The Lee algorithm prioritizes selecting the best path, typically favoring L-shaped routes over zigzags. If no L-shaped paths are available, it may resort to zigzag routes. This approach is particularly valuable for global routing tasks.

However, the Lee algorithm has limitations. It essentially constructs a maze and then numbers its cells from the source to the target. While effective for routing between two pins, it can be time-consuming when dealing with millions of pins. There are alternative algorithms that address similar routing challenges.

![image](https://github.com/user-attachments/assets/417076b5-915d-45d3-8935-1ff0e05b2e35)

## Design Rule Check (DRC)

DRC verifies whether a design meets the predefined process technology rules given by the foundry for its manufacturing. DRC checking is an essential part of the physical design flow and ensures the design meets manufacturing requirements and will not result in a chip failure. It defines the Quality of chip. They are so many DRCs, let us see few of them

Design rules for physical wires

Minimum width of the wire, Minimum spacing between the wires, Minimum pitch of the wire To solve signal short violation, we take the metal layer and put it on to upper metal layer. we check via rules, Via width, via spacing.

## Power Distribution Network generation

Unlike the general ASIC flow, Power Distribution Network generation is not a part of floorplan run in OpenLANE. PDN must be generated after CTS and post-CTS STA analyses:

we can check whether PDN has been created or no by check the current def environment variable: ``` echo $::env(CURRENT_DEF)```

```
prep -design picorv32a -tag Run 12.07.10.11
gen_pdn

```


- Once the command is given, power distribution netwrok is generated.
- The power distribution network has to take the ```design_cts.def``` as the input def file.
- Power rings,strapes and rails are created by PDN.
- From VDD and VSS pads, power is drawn to power rings.
- Next, the horizontal and vertical strapes connected to rings draw the power from strapes.
- Stapes are connected to rings and these rings are connected to std cells. So, standard cells get power from rails.
- The standard cells are designed such that it's height is multiples of the vertical tracks /track pitch.Here, the pitch is 2.72. Only if the above conditions are adhered it is possible to power the standard cells.
- There are definitions for the straps and the rails. In this design, straps are at metal layer 4 and 5 and the standard cell rails are at the metal layer 1. Vias connect accross the layers as required.

![image](https://github.com/user-attachments/assets/30a07831-821e-490d-9187-32ff901060dc)

## Routing

 In the realm of routing within Electronic Design Automation (EDA) tools, such as both OpenLANE and commercial EDA tools, the routing process is exceptionally intricate due to the vast design space. To simplify this complexity, the routing procedure is typically divided into two distinct stages: Global Routing and Detailed Routing.

The two routing engines responsible for handling these two stages are as follows:

- **Global Routing**: In this stage, the routing region is subdivided into rectangular grid cells and represented as a coarse 3D routing graph. This task is accomplished by the "FASTE ROUTE" engine.

- **Detailed Routing**: Here, finer grid granularity and routing guides are employed to implement the physical wiring. The "tritonRoute" engine comes into play at this stage. "Fast Route" generates initial routing guides, while "Triton Route" utilizes the Global Route information and further refines the routing, employing various strategies and optimizations to determine the most optimal path for connecting the pins.

## Key Features of TritonRoute

- **Initial Detail Routing**: TritonRoute initiates the detailed routing process, providing the foundation for the subsequent routing steps.

- **Adherence to Pre-Processed Route Guides**: TritonRoute places significant emphasis on following pre-processed route guides. This involves several actions:

   - **Initial Route Guide Analysis**: TritonRoute analyzes the directions specified in the preferred route guides. If any non-directional routing guides are identified, it breaks them down into unit widths.

   - **Guide Splitting**: In cases where non-directional routing guides are encountered, TritonRoute divides them into unit widths to facilitate routing.

   - **Guide Merging**: TritonRoute merges guides that are orthogonal (touching guides) to the preferred guides, streamlining the routing process.

   - **Guide Bridging**: When it encounters guides that run parallel to the preferred routing guides, TritonRoute employs an additional layer to bridge them, ensuring efficient routing within the preprocessed guides.
   - Assumes route guide for each net satisfy inter guide connectivity Same metal layer with touching guides or neighbouring metal layers with nonzero  vertically overlapped area( via are placed ).each unconnected termial i.e., pin of a standard cell instance should have its pin shape overlapped by a routing guide( a black dot(pin) with purple box(metal1 layer)). 


In summary, TritonRoute is a sophisticated tool that not only performs initial detail routing but also places a strong emphasis on optimizing routing within pre-processed route guides by breaking down, merging, and bridging them as needed to achieve efficient and effective routing results.

Works on MILP(Mixed Integer linear programming) based panel routing scheme with Intra-layer parallel and Inter-layer sequential routing framework

# TritonRoute problem statement

```
Inputs : LEF, DEF, Preprocessed route guides
Output : Detailed routing solution with optimized wire length and via count
Constraints : Route guide honoring, connectivity constraints and design rules.

```
The space where the detailed route takes place has been defined. Now TritonRoute handles the connectivity in two ways.

Access Point(AP) : An on-grid point on the metal of the route guide, and is used to connect to lower-layer segments, pins or IO ports,upper-layer segments.
Access Point Cluster(APC) : A union of all the Aps derived from same lower-layer segment, a pin or an IO port, upper-layer guide.

**TritonRoute run for routing**

Make sure the CURRENT_DEF is set to pdn.def

Start routing by using

```
run_routing
```
The options for routing can be set in the config.tcl file.
The optimisations in routing can also be done by specifying the routing strategy to use different version of TritonRoute Engine. There is a trade0ff between the optimised route and the runtime for routing.

#### 1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.

Commands to perform all necessary stages up until now

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Following commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts

# Now that CTS is done we can do power distribution network
gen_pdn
```
![Screenshot from 2024-11-14 04-56-18](https://github.com/user-attachments/assets/fed8d413-740c-4b8e-a681-e350742f6914)
![Screenshot from 2024-11-14 04-57-34](https://github.com/user-attachments/assets/4decd843-dd71-4e00-ac4f-9fde1ec31b22)

Screenshots of power distribution network run
![Screenshot from 2024-11-14 04-59-03](https://github.com/user-attachments/assets/e67aa97c-58ff-4f18-a09f-c45e06cf984b)
![Screenshot from 2024-11-14 04-59-33](https://github.com/user-attachments/assets/7ff4301c-472a-4bc2-af37-3f38f4f5ad6c)



Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_00-51/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

Screenshots of PDN def

![Screenshot from 2024-11-14 05-04-28](https://github.com/user-attachments/assets/453e5e34-2e1b-497f-ad43-850478929793)
![Screenshot from 2024-11-14 05-05-10](https://github.com/user-attachments/assets/f2def395-b49b-416e-838a-145a946a6c1f)


#### 2. Perfrom detailed routing using TritonRoute and explore the routed layout.

Command to perform routing

```
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```

Screenshots of routing run
![last run routing](https://github.com/user-attachments/assets/8c1d2b90-416f-41df-8580-a3a48fa5ecf1)

![0 vio](https://github.com/user-attachments/assets/53ceef26-028d-4105-9a69-804a88e18172)

![routing done](https://github.com/user-attachments/assets/2862d3b9-3c9a-419c-b1ec-b08b86c2bd1a)


Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_00-51/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![last magic](https://github.com/user-attachments/assets/bac624a4-6258-45f8-9e22-9403f1d21902)

![last magic 1](https://github.com/user-attachments/assets/2565fb68-0ffc-4f31-adb3-7a62a3679901)

![last magic 2](https://github.com/user-attachments/assets/137b5632-e7ac-43ae-bcbd-0945b24ea31c)



Screenshot of fast route guide present in `openlane/designs/picorv32a/runs/13-11_00-51/tmp/routing` directory

![magic guide](https://github.com/user-attachments/assets/9092831d-752a-410a-903c-982e4ef7fe67)

#### 3. Post-Route OpenSTA timing analysis with the extracted parasitics of the route.

Commands to be run in OpenLANE flow to do OpenROAD timing analysis with integrated OpenSTA in OpenROAD

```
# Command to run OpenROAD tool
openroad

# Reading lef file
read_lef /openLANE_flow/designs/picorv32a/runs/13-11_00-51/tmp/merged.lef

# Reading def file
read_def /openLANE_flow/designs/picorv32a/runs/13-11_00-51/results/routing/picorv32a.def

# Creating an OpenROAD database to work with
write_db pico_route.db

# Loading the created database in OpenROAD
read_db pico_route.db

# Read netlist post CTS
read_verilog /openLANE_flow/designs/picorv32a/runs/13-11_00-51/results/synthesis/picorv32a.synthesis_preroute.v

# Read library for design
read_liberty $::env(LIB_SYNTH_COMPLETE)

# Link design and library
link_design picorv32a

# Read in the custom sdc we created
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

# Setting all cloks as propagated clocks
set_propagated_clock [all_clocks]

# Read SPEF
read_spef /openLANE_flow/designs/picorv32a/runs/13-11_00-51/results/routing/picorv32a.spef

# Generating custom timing report
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4

# Exit to OpenLANE flow
exit
```

Screenshots of commands run and timing report generated

![final openroad](https://github.com/user-attachments/assets/b8d3b9ca-af93-4203-9176-0e3c5163b560)

![final slack 1](https://github.com/user-attachments/assets/03c0e2a1-44e0-40bf-ab5e-59487af87771)

![final slack 2](https://github.com/user-attachments/assets/eabbb930-aacd-490f-934b-5650619a2ba6)

![final slack 3](https://github.com/user-attachments/assets/d87067c0-c268-4ca4-b639-ca9b16a03a29)



# ASSIGNMENT 6: Physical Design using OpenRoad

**1. FinFET Basics:**
Path to Zetta-Scale Computing

Introduction:

* **Bombe:** The Bombe was an electro-mechanical machine designed during World War II to decrypt German Enigma-encrypted messages. It was refined and built by Alan Turing and Gordon Welchman at Bletchley Park, UK. The Bombe systematically tested possible rotor settings of the Enigma machine by exploiting known plaintext patterns. Its logical operations helped narrow down the vast number of possible keys, significantly accelerating the decryption process. The Bombe played a critical role in the Allied war effort.

* **ENIAC (Electronic Numerical Integrator and Computer):** It was developed during World War II by John Presper Eckert and John Mauchly at the University of Pennsylvania, was the first general-purpose, fully electronic digital computer. Completed in 1945, it was designed to compute artillery firing tables for the U.S. Army. ENIAC used vacuum tubes instead of mechanical or electromechanical components. However, it lacked a stored-program capability, requiring manual reconfiguration for each new task. ENIAC demonstrated the immense potential of electronic computing for large-scale numerical problems.

* **EDVAC (Electronic Discrete Variable Automatic Computer):** EDVAC, also developed by Eckert and Mauchly with conceptual input from John von Neumann, was one of the first computers to implement the stored-program concept. Completed in 1949, EDVAC represented a significant improvement over ENIAC by using binary representation instead of decimal and storing both data and instructions in memory. This innovation simplified programming and laid the groundwork for the modern von Neumann architecture.

**50 Years of Microprocessor Trend Data:**

![image](https://github.com/user-attachments/assets/663e8079-4932-4856-ae71-222a6a40527a)

**The Key metrics are:**

**Transistors (Orange Triangles):** The number of transistors on a microprocessor chip (in thousands) has increased exponentially, following Moore's Law, which predicts a doubling approximately every two years. This growth enabled more complex and capable processors, reaching the range of billions of transistors by the 2020s.

**Single-Thread Performance (Blue Circles):** It is measured using SpecINT. It indicates the computational ability of a single processor core. Performance grew steadily due to improvements in architecture, instruction-level parallelism, and clock speeds, but the growth rate slowed post-2005 due to physical limitations like power and heat.

**Frequency (Green Diamonds):** Processor clock speed (in MHz) rose steadily until the early 2000s but then stagnated as increasing clock speeds became inefficient due to heat dissipation issues.

**Typical Power (Red Triangles):** Power consumption increased with transistor density and frequency, becoming a critical design challenge around the mid-2000s.

**Number of Logical Cores (Black Dots):** The transition to multi-core processors gained momentum in the mid-2000s as a response to the stagnation in single-thread performance. By increasing the number of cores, processors enabled more efficient parallel processing, leading to significant improvements in overall performance


**Path to zetta-scale computing**

![image](https://github.com/user-attachments/assets/e56e21c0-e51f-4a81-988b-d055e6c6647f)image



**Key Performance Levels**

1. Gigascale (10⁹ FLOPS): The starting point in 1984, marking the capability of early supercomputers.
2. Terascale (10¹² FLOPS): Achieved around 1997, a significant milestone where systems like Jaguar (Cray XT5) delivered teraflop performance with power consumption of 7 MW.
3. Petascale (10¹⁵ FLOPS): Achieved in 2008 with systems like Titan (Cray XK6) at 27 petaflops, consuming 9 MW. This milestone represents the era of petascale high-performance computing (HPC).
4. Exascale (10¹⁸ FLOPS): Reached by systems like Frontier (Cray Shasta) in 2021, delivering 1.5 exaflops using 4 AMD GPUs and 1 AMD CPU, consuming 29 MW of power. Exascale computing enables highly detailed simulations and large-scale AI workloads.
5. Zettascale (10²¹ FLOPS): Projected to be achieved by around 2035. At this scale, systems will handle unprecedented computational workloads, such as advanced climate modeling, AI, and large-scale simulations. Power consumption is estimated to range between 50-100 MW for a single zettascale machine.

**CMOS Evolution and Next-Gen Candidates**

![image](https://github.com/user-attachments/assets/b138fedc-ecdd-4207-8ee5-faf1ce11e4e7)


This diagram illustrates the evolving landscape of CMOS (Complementary Metal-Oxide-Semiconductor) technology and highlights emerging materials, structures, and processes being explored for next-generation semiconductor devices. These innovations aim to address the challenges of scaling CMOS technology down to the 1nm node and beyond.

**Channel Material**

*	Current Trends:
            Silicon (Si) is the primary material used for the channel in traditional CMOS transistors, with strained SiGe (Silicon-Germanium) being used in some high-performance applications to enhance carrier mobility.

*	Future Materials:
            2D materials such as MoS₂ (Molybdenum Disulfide) are being explored due to their potential for better electrical characteristics at smaller scales.
            Germanium (Ge) is gaining interest as it offers higher electron mobility, which could significantly boost transistor performance at small nodes.

**Patterning**

*	Current Techniques:
            Deep Ultraviolet (DUV) lithography is the most commonly used technique for defining transistor features, with ArF (Argon Fluoride) and KrF (Krypton Fluoride) lasers operating at different wavelengths.

*	Next-Gen:
            Extreme Ultraviolet (EUV) lithography is expected to be a key technology for sub-7nm nodes. High-NA (Numerical Aperture) EUV will further improve the resolution for even smaller transistor nodes, pushing the boundaries of Moore's Law.

**Gate Stack Material**

*	Current Materials:
            High-K metal gates (HKMG) are used in the gate stack of modern FETs to reduce gate leakage current and improve switching performance.

*	Next-Gen Candidates:
            NC-FET (Negative Capacitance FET): This is a promising transistor design that leverages ferroelectric materials to reduce power consumption by enabling lower voltage operation.
            TFET (Tunnel FET): TFETs use quantum tunneling to switch on and off, offering a significant reduction in power consumption compared to conventional FETs, especially for low-power applications.

**Interconnection Material**

*	Current Materials:
            Copper (Cu) is the primary material used for interconnects due to its low resistivity, which helps in minimizing power loss and delays in transistor connections.

*	Next-Gen Materials:
            Ruthenium (Ru) and Compound metals are being investigated for their potential to reduce resistance and improve performance in ultra-small transistors.
            Topological semi-metals may offer unique properties, such as lower resistivity and increased performance at the atomic scale.

**Device Structure**

*	Current Architectures:
            FinFET and planar transistors are used to maintain performance at smaller nodes. FinFETs, in particular, help improve control over short-channel effects by using a 3D structure.

*	Next-Gen Architectures:
            3DS-FET (3D Stacked FET): These are three-dimensional transistors where multiple layers of devices are stacked vertically, reducing footprint and improving performance.
            MBC-FET (Multi-Bridge Channel FET): This structure aims to enhance drive current by creating multiple channels within the same device.
            VFET (Vertical FET): VFETs utilize vertical channels to improve density and reduce power consumption.

**Design Co-Optimization**

*	DTCO (Design-Technology Co-Optimization):
            DTCO focuses on integrating new design techniques with advanced process technologies to maximize chip performance, often involving backside interconnects (BSI), where interconnections are made at the back of the wafer for improved signal integrity and reduced latency.

*	STCO (System-Technology Co-Optimization):
            This approach involves optimizing both the system architecture and the underlying technology. One example is the use of chiplets, which allow for modular, customized designs by integrating multiple smaller chips into one package, offering flexibility and reducing the complexity of scaling single-chip designs.

**FinFETs**

![image](https://github.com/user-attachments/assets/4b50ff18-aab0-4ac0-bb92-a5a52712fb43)


This diagram illustrates the evolution of transistor technology from planar to more advanced architectures like FinFET and Gate-All-Around (GAA):

*	Planar Transistor (Traditional):
        Early transistor design with a flat channel and gate structure.
        The gate controls the channel from one side only, leading to limited performance as scaling continues.

*	FinFET (2011):
        The channel is shaped like a vertical fin, allowing the gate to wrap around three sides of the channel.
        Provides better control over the channel, reducing leakage and improving performance at smaller sizes.

*	Gate-All-Around (GAA) Transistor (2025?):
        The gate completely surrounds the channel, typically implemented using stacked nanosheets or nanowires.
        Offers even better control over the channel compared to FinFET, allowing higher performance and efficiency with continued scaling.

Each step improves drive current capability and enhances control over the transistor's on/off states, critical for power efficiency and miniaturization in modern electronics.

Why FinFETs and Gate-All-Around Transistors?

![image](https://github.com/user-attachments/assets/febc2fa2-2898-430d-867b-4f7e8d79355d)


This diagram explains the advantages of FinFETs and Gate-All-Around (GAA) transistors compared to traditional planar structures:

**Planar Transistors:**

*	Challenges:
        Sub-channel leakage occurs where current leaks underneath the gate.
        Results in reduced efficiency.
        Increases power consumption.

*	FinFET Transistors:
        The gate wraps around the channel (fin) on three sides, providing better control over the channel.
        **Benefits:**
           - 	Reduces sub-threshold leakage.
           - 	Enhances drive current ((I_{ON})).
           - 	Allows a smaller transistor area while maintaining high performance.

*	Gate-All-Around (GAA) Transistors:
        The gate completely surrounds the channel, offering superior electrostatic control.
        **Advantages:**
            -	Improves short-channel performance by reducing drain capacitance and enhancing gate capacitance.
            -	Improves scaling efficiency as indicated by the formula (S \propto (1 + C_d / C_{ox})).
            -	Provides reduced sub-threshold slope and better performance at smaller scales.

*	Graph Comparison:
            -	Illustrates the performance advantages of FinFETs and GAA over planar transistors.
  	    -	Shows better efficiency and reduced sub-threshold slope as dimensions shrink.

![image](https://github.com/user-attachments/assets/2598f386-9ccf-455a-8744-987de8245c99)


**Reduced Leakage:** Tri-Gate transistors exhibit significantly lower leakage current compared to planar transistors at the same gate voltage. Lower leakage results in both reduced off-current at the same on-current and lower power dissipation.

**Higher Drive Current:** Tri-Gate transistors provide higher drive current compared to planar transistors at the same off-current. This results in improved circuit performance and greater efficiency in modern electronic applications.

**FEOL Innovations:**

FEOL refers to the initial stages of semiconductor manufacturing where the active devices (e.g., transistors) are built on the silicon wafer. It involves creating components such as transistors, capacitors, and isolation structures before metal interconnects are added. FEOL Innovations help drive Moore's Law forward by enabling smaller, more efficient, and more powerful transistors.

**CMOS Technology Inflection Points**

![image](https://github.com/user-attachments/assets/fb763b75-19f1-4dfd-9d52-4a7ec1bb0779)


**1. Dennard Scaling:**
-	States that power density remains constant as transistors shrink.
-	Initially allowed voltage scaling with smaller gate lengths, shown in the bottom-left graph.

**2. Technology Nodes and Innovations:**
       *	~1 µm ("End of Scaling"): Start of CMOS miniaturization.
       *	180 nm (Voltage Scaling): Start of drive voltage reduction.
       *	130 nm (Cu BEOL): Introduction of copper interconnects for better conductivity.
       *	90 nm (Uniaxial Strained Si NMOS): Strained silicon enhances electron mobility.
       *	65 nm (eSiGe CVD ULK): Embedded SiGe improves PMOS performance.
       *	45 nm (HK-first MG-last): High-k dielectrics and metal gates reduce leakage and improve gate control.
       *	32 nm (HKMG with Raised S/D NMOS): Advanced HKMG implementation and raised source/drain regions.

**2. SEM Images**

*	**Left Image:** Shows the cross-sectional view of a transistor structure with High-k materials and embedded SiGe (Silicon-Germanium).It has high-k dielectric and metal gates are used to improve performance. SiGe regions enhance PMOS performance by applying strain to the silicon channel.

*	**Right Image:** Demonstrates the raised source/drain (S/D) regions and gate channel in PMOS transistors at smaller nodes.

**3. Drive Voltage Scaling Graph (Bottom-left):** The graph shows the relationship between gate length (x-axis, logarithmic scale) and drive voltage (y-axis, logarithmic scale). The Ideal scaling behavior indicates that the voltage decreases linearly with shrinking gate length. Red and green markers show practical trends for low-power and high-performance devices, which deviate from ideal scaling due to challenges like leakage currents and increased power density.

![image](https://github.com/user-attachments/assets/2e8b56c7-cb3d-4eb4-977d-64bc6701d124)


**Key Technology Nodes and Innovations**

**1. 22 nm:**
        Introduction of FinFET (Tri-Gate) transistors, which reduce leakage and improve gate control.
        Use of self-aligned contacts (SAC) and copper interconnects (Co+Cu BEOL).

**2. 14 nm:**
        Transition to unidirectional metal routing for better density.
        Implementation of SADP (Self-Aligned Double Patterning) and SDB (Single Diffusion Break) for precise layout.

**3. 10 nm:**
        Adoption of advanced patterning techniques such as:
        -	SA-SDB (Self-Aligned SDB)
        -	LELELE (Litho-Etch-Litho-Etch-Litho-Etch)
        -	SAQP (Self-Aligned Quadruple Patterning) for tighter geometries.

**4. 7 nm:**
        -	Introduction of Extreme Ultraviolet Lithography (EUV) to simplify the patterning process and reduce overlay errors.

**5. 5 nm:**
        -	Integration of SiGe (Silicon-Germanium) channels for PMOS to enhance hole mobility.
        -	Use of EUV SA-LELE (Self-Aligned Litho-Etch-Litho-Etch).

**6. 3 nm / 2 nm / 1.4 nm:**
        -	Transition to Gate-All-Around (GAA) nanosheet transistors for improved electrostatic control.
        -	GAA stacks nanosheets or nanowires horizontally to maximize current drive.

**7. Sub-1 nm:**
        -	Development of CFET (Complementary FET), which vertically stacks NMOS over PMOS to save area.
        -	Use of 2D materials, such as MoS₂, for atomic-scale channel thickness in 2D FETs.

![image](https://github.com/user-attachments/assets/b0955ae3-73fe-441e-aa85-5e64bbbea3ce)


The image illustrates how Samsung has scaled down the size of transistors in their successive generations of nodes (10nm, 8nm, 7nm, and 5nm) using a technique called Fin Depopulation. In FinFET transistors, the "fin" is the vertical channel that carries the current. Fin Depopulation involves reducing the number of fins per transistor while keeping the fin width constant. This allows for smaller transistors without compromising performance.

    *	10nm (HD): The transistor has a fin height of 420nm and uses 10 fins.
    *	8nm (UHD): The fin height is reduced to 378nm, and the number of fins is decreased to 9.
    *	7nm (HD): The fin height remains at 27nm, but the number of fins is further reduced to 8.
    *	5nm (UHD): The fin height is maintained at 27nm, and the number of fins is decreased to 7.

![image](https://github.com/user-attachments/assets/06809cea-45b6-47c9-867c-4802faedf13e)


*	**Double Diffusion Break (DDB):** Double Diffusion Break (DDB) involves creating a gap between the source and drain regions of a transistor. This gap is filled with an insulating material, which reduces the effective width of the transistor. By doing so, DDB enables the design of smaller cell sizes, allowing for higher transistor density and improved scalability. A cross-sectional view of a transistor with DDB highlights the insulating gap between the source and drain regions.

*	**Single Diffusion Break (SDB):** Single Diffusion Break (SDB) is similar to DDB but less aggressive. It involves introducing a gap on only one side of the transistor. This approach provides a balanced trade-off between size reduction and maintaining transistor performance. A cross-section of a transistor with SDB highlights the gap on one side, showcasing its simplicity compared to DDB.

*	**Contact Over Field Gate (COFG):** Contact Over Field Gate (COFG) places the gate contact directly over the field oxide region of a transistor. This design reduces lateral spacing between adjacent transistors, enabling smaller cell sizes without significant performance loss. A cross-sectional representation of a transistor with COFG illustrates the positioning of the gate contact over the field oxide.

*	**Contact Over Active Gate (COAG):** Contact Over Active Gate (COAG) is a more aggressive technique than COFG. Here, the gate contact is placed directly over the active gate region of the transistor. This approach enables even smaller cell sizes and higher transistor density, which are critical for advanced semiconductor nodes. A cross-sectional image of a transistor with COAG highlights the gate contact placement over the active gate.

*	**Back-Side Power Delivery Network (BS-PDN):** The Back-Side Power Delivery Network (BS-PDN) is an innovative approach where power supply rails are routed on the backside of the chip. This method reduces the height of the standard cell, creating space for more transistors and improving overall transistor density. Additionally, it enhances power delivery efficiency and reduces resistance, which is crucial for high-performance applications. A schematic of a standard cell with BS-PDN illustrates the positioning of power rails on the backside of the chip.

![image](https://github.com/user-attachments/assets/b7a3fff3-158c-45ee-9f8d-cbc8638ae0d1)


*	**Planar Technology:** In early planar technology nodes (100nm and above), the Vt variability is significantly high, around 130mV. This is due to various factors like process variations, temperature fluctuations, and line-edge roughness.

*	**FinFET Technology:** With the advent of FinFET technology (around 22nm), the Vt variability reduces significantly to around 14mV. This improvement is attributed to the better control over the channel length and width in FinFETs compared to planar transistors.

*	**NW Technology (Nanowire):** In the latest nanowire technology (14nm and below), the Vt variability is even lower, around 7mV. This further reduction is due to the precise control over the nanowire dimensions and the reduced impact of process variations.

![image](https://github.com/user-attachments/assets/d52ffa2a-7dd3-4ed7-8486-8cf73fe02282)


**Planar MOSFETs**

Planar MOSFETs, the traditional architecture, have a simple structure where the gate sits above the channel. In this design, the contact width ((W_C)) and gate width ((W_G)) are nearly equal, resulting in a ratio of (W_C / W_G \approx 1). This leads to a low parasitic resistance, with (R_{EXT}) being much smaller than (R_{ch}) ((R_{EXT} / R_{ch} < 1)). As a result, planar MOSFETs suffer minimal performance degradation due to parasitic resistance.

**FinFETs**

FinFETs, a 3D transistor design, introduce vertical fins with the gate wrapping around them for improved control. However, the effective contact width decreases relative to the gate width, leading to (W_C / W_G \approx 1/3). Consequently, the parasitic resistance becomes comparable to the channel resistance ((R_{EXT} / R_{ch} \approx 1)), which begins to impact the performance of the device as it scales.

**Gate-All-Around (GAA) FETs**

Gate-All-Around (GAA) FETs, which use nanosheets or nanowires, offer even better electrostatic control by fully surrounding the channel with the gate. However, the contact width further decreases compared to the gate width, resulting in (W_C / W_G \approx 1/6). This causes a significant increase in parasitic resistance, with (R_{EXT}) being approximately three times the channel resistance ((R_{EXT} / R_{ch} \approx 3)). While GAA FETs improve transistor density, the higher parasitic resistance becomes a challenge for maintaining performance.

**Complementary FETs (CFETs)**

Complementary FETs (CFETs) take transistor stacking to the next level by vertically integrating NMOS and PMOS transistors. This approach maximizes space efficiency in advanced nodes but inherits the high parasitic resistance of GAA FETs. With (W_C / W_G) remaining small, the (R_{EXT} / R_{ch}) ratio is around 3, posing similar challenges to those faced by GAA FETs.

**Explanation of Parasitic Resistance**

![image](https://github.com/user-attachments/assets/f2d602b0-07b8-4a13-a55d-eb5fdd378ede)

The image highlights the breakdown of parasitic resistance ((R_{EXT})) and approaches for reducing it in transistors. Here is a detailed explanation:

Components of Parasitic Resistance ((R_{EXT})) The leftmost diagram illustrates the various contributors to (R_{EXT}) in a transistor:

    *	(R_{CA-BEOL}): Resistance from the contact in the Back-End-Of-Line (BEOL).
    *	(R_{CA}): Resistance at the contact area.
    *	(R_{CA-TS}): Resistance at the contact to the transition structure.
    *	(R_{TS}): Resistance in the transition structure.
    *	(R_{MOL}): Middle-Of-Line resistance (includes lateral and vertical contributions).
    *	(R_C): Contact resistance at the metal-semiconductor interface.
    *	(R_{EPI}): Epitaxial layer resistance (contributes to current spreading).
    *	(R_{FEOL}): Front-End-Of-Line resistance from the source/drain extensions.

Initial vs. Improved (R_{EXT}) Breakdown The two pie charts in the center show the contributions of different resistance components for NFETs and PFETs before and after improvements:

**NFET:**
        -	Initial: Majority of (R_{EXT}) comes from (R_C) (63%) and (R_{CA-BEOL}) (31%).
        -	Improved: Significant reduction in (R_C) (48%) and (R_{CA-BEOL}) (12%).
**PFET:**
        -	Initial: (R_{FEOL}) (50%) and (R_C) (45%) dominate.
        -	Improved: Major reduction in (R_{FEOL}) (78%) and (R_C) (16%).

Improving Ohmic/Tunneling Contacts The right section discusses methods to improve the metal-semiconductor interface:

**Key Objectives:**
        -	Low Schottky Barrier Height (SBH) ((\phi_b)): Reduces the energy barrier for carrier injection, enabling better contact conductivity.
        -	High Doping Concentration ((N_d)): Increases carrier density at the interface, reducing contact resistance.

Equation for Specific Contact Resistivity ((\rho_c)): [ \rho_c \propto \exp\left(\frac{2\phi_b}{\hbar} \sqrt{\frac{\epsilon_s m_x}{N_d}}\right) ] This equation shows how lowering (\phi_b) and increasing (N_d) can reduce (\rho_c).

**Metal-Semiconductor Energy Diagram:**
	The energy diagram shows how a reduction in (\phi_b) (Schottky Barrier Height) facilitates easier carrier injection from the metal to the semiconductor.

![image](https://github.com/user-attachments/assets/ba2911e6-0be6-4643-84b8-33745a16fbe7)


The bar chart on the left shows how the composition of (C_{eff}) evolves from 22nm to 7nm technology nodes:

*	At 22nm, the dominant contributor to (C_{eff}) is (C_{fr}) (56%), while parasitic capacitances (C_{pc-ca}) (25%) and (C_{g}) (19%) contribute less.
*	At 14nm and 10nm, parasitic capacitances ((C_{pc-ca}) and (C_{fr})) start dominating, with (C_{fr}) decreasing to 38% and 25%, respectively, while (C_{pc-ca}) increases.
*	At 7nm, (C_{g}) becomes the most significant contributor (45%), with (C_{pc-ca}) and (C_{fr}) still present but reduced. This highlights the increasing impact of parasitic capacitance as node sizes shrink.

**Plot Descriptions:**

*	The first scatter plot shows a reduction in normalized delay for a ring oscillator when using SiBCN spacers instead of SiN spacers, indicating improved performance.
*	The second scatter plot demonstrates an 8% reduction in (C_{eff}) with SiBCN spacers, which corresponds to the delay improvement observed in the first plot.
*	The rightmost figure shows the evolution of spacer materials from SiOCN to SiCO. This material transition aims to significantly reduce the gate-contact capacitance across nodes. At 14nm and beyond, low-(k) spacers improve performance by decoupling parasitic effects and maintaining capacitance at manageable levels.
*	The bottom middle image shows a cross-sectional TEM view of a transistor with air spacers around the gate: i) Air, with its extremely low dielectric constant ((k \approx 1)), significantly reduces parasitic capacitance. The adjacent plot quantifies this improvement, showing a 15% reduction in (C_{eff}) when using air spacers compared to solid spacers.

![image](https://github.com/user-attachments/assets/bb4fa79b-3767-40ca-a895-8ea4494ad8d9)


**Key Properties of 2D Layered Materials (Compared to Silicon):**

-	Uniform Atomic Scale Thickness: A single layer of MoS₂ is approximately 0.65 nm thick, offering an ideal thickness for scaling compared to silicon.
-	Higher Effective Mass (( m^ )):* MoS₂ has an effective mass of about 0.55 times the mass of a free electron (( m_0 )), whereas silicon’s effective mass is around 0.22 ( m_0 ).
-	Bandgap: Additionally, 2D materials like MoS₂ possess favorable bandgaps for electronic devices, with a monolayer bandgap of ~1.85 eV, which reduces to ~1.5 eV for a bilayer.

![image](https://github.com/user-attachments/assets/fd6b3e48-1998-461f-b54b-81b1a16aa0b4)

**Transistor Scaling:**
        To achieve smaller gate lengths, devices must address various physical and material constraints to ensure reliable operation.

**Challenges for Scaling:**
-	Direct Source-to-Drain Tunneling: As the gate length decreases, electrons can tunnel directly from the source to the drain, bypassing the gate control. To mitigate this, materials with a high effective mass (( m^* )) are needed.
-	Surface Roughness and Thickness Variations: Variability at atomic scales can cause performance issues. Uniform, atomically thin materials are essential for minimizing such variations.
-	Capacitance Ratios (( C_D ) and ( C_{ox} )): The capacitance of the depletion region (( C_D )) must remain low relative to the gate oxide capacitance (( C_{ox} )) to improve gate control. Materials with a low in-plane dielectric constant (( \epsilon )) are necessary for this.

**Diagrams:**
-	The left shows the transistor structure with key components like the source, drain, gate, oxide, and silicon substrate.
-	The right illustrates two scenarios:
        	a. Thermionic Emission: Electrons cross the energy barrier as intended.
        	b. Direct Tunneling: At extremely small gate lengths, electrons tunnel directly from source to drain, leading to leakage.

**Key Takeaway:**
        New channel materials, such as 2D materials, are required to overcome these challenges while maintaining high performance and scalability.

![image](https://github.com/user-attachments/assets/04001199-bb59-4efe-bf62-854e9ab07692)


Concept of Direct Source-to-Drain Tunneling: As the gate length ((L_G)) in MOSFETs decreases, direct tunneling of electrons from the source to the drain becomes significant, leading to increased leakage currents. This leakage is influenced by material properties, such as the effective mass ((m^*)) and the bandgap ((E_G)).

A higher (m^) in MoS₂ suppresses tunneling leakage compared to silicon. The graph shows the leakage current ((I_{SD, \text{leak}})) as a function of gate length ((L_G)) for various channel thicknesses ((T_{CH})). MoS₂ exhibits lower leakage at smaller gate lengths compared to silicon, achieving up to 100x reduction in leakage due to its higher (m^), larger (E_G), and lower dielectric constant ((\epsilon)).

The superior performance of MoS₂ in minimizing leakage currents results in significant energy savings, making it a promising material for future transistor scaling.

![image](https://github.com/user-attachments/assets/217727ce-441a-4aff-8af1-215451b3889d)


The MoS₂ transistor with a 1 nm gate length represents a breakthrough in miniaturization, featuring a thin MoS₂ channel for its excellent electronic properties. A single-walled carbon nanotube (SWCNT) serves as the ultra-small gate electrode, while Zirconium Dioxide (ZrO₂) acts as a high-k dielectric, reducing leakage and ensuring precise control. Built on a SiO₂ substrate with an n⁺ silicon back gate, the transistor uses the CNT gate to deplete a small region of the MoS₂ channel, enabling efficient modulation. This innovative design showcases the potential of 2D materials and nanoscale gates in advancing transistor technology.

![image](https://github.com/user-attachments/assets/4a685f55-49a0-4a9f-9fe1-68751dae440d)


The slide illustrates the structure and fabrication of an All-2D MOSFET (Metal-Oxide-Semiconductor Field-Effect Transistor), where all key components, including the channel, gate, and contacts, are made using two-dimensional materials. This device leverages the exceptional properties of 2D materials to improve performance and scalability. Below is a breakdown of the key components and the fabrication process:
*	Graphene Contacts (S, D, G): Graphene is used as the source, drain, and gate electrodes. Its high conductivity and 2D nature make it ideal for ensuring low-resistance electrical contacts. MoS₂ Channel:

*	Molybdenum Disulfide (MoS₂) serves as the semiconductor channel. MoS₂ is widely used in 2D MOSFETs due to its excellent on/off current ratio and atomic-scale thickness. h-BN Dielectric:

*	Hexagonal Boron Nitride (h-BN) acts as the insulating dielectric layer. It is a 2D material with excellent insulating properties and high thermal stability, making it suitable for separating the graphene gate from the MoS₂ channel. Si/SiO₂ Substrate:

The device is fabricated on a silicon dioxide (SiO₂) layer on top of a silicon substrate, which provides mechanical support and a global back gate. Fabrication Process:

*	A layer of graphene is deposited on the SiO₂ substrate, which will later serve as the gate electrode.
*	Graphene is patterned to define the source and drain regions, leaving gaps for the channel.
*	A monolayer of MoS₂ is transferred onto the patterned graphene, forming the channel region.
*	An h-BN layer is added on top of the MoS₂ as the gate dielectric.
*	A top layer of graphene is deposited and aligned as the gate electrode.
*	The completed device is contacted using metallic electrodes (Ni/Au) for testing.

![image](https://github.com/user-attachments/assets/2fc69f53-adba-4d11-99b1-2906b2745946)


**The All-2D MOSFET exhibits excellent electrical performance:**

*	Transfer Characteristics (I(_D) vs V(_G)): Achieves a high on/off current ratio (>10⁵), demonstrating strong gate control for effective switching.
*	Output Characteristics (I(D) vs V({DS})): Smooth current modulation with increasing V(G) and V({DS}), indicating good output performance.
*	Mobility: Field-effect mobility remains constant with increasing gate electric field, showing minimal scattering and high-quality interfaces in the 2D materials stack.

These results highlight the potential of 2D materials like MoS₂, graphene, and h-BN for scalable, high-performance transistor applications.

![image](https://github.com/user-attachments/assets/5e645878-5fc1-4e39-bbed-e7287002c676)


**The diagram on the top left shows a non-planar transistor with key components:**

*	Gate: Controls the flow of current through the channel.
*	Channel: Region where current flows between the source (S) and drain (D).
*	Body: Underlying region connected to the substrate.
*	STI (Shallow Trench Isolation): Insulates neighboring devices.

The biggest challenge is to form a single-crystalline semiconductors on a non-planar surface is difficult using conventional semiconductor fabrication techniques.

![image](https://github.com/user-attachments/assets/6299f7de-dcd6-4d0a-9996-5b389c879e9f)


**Single-Layer CMOS (a):**
	This is the traditional CMOS design where NMOS and PMOS transistors are fabricated on a single silicon layer. Each transistor operates in the same planar layer, with devices connected laterally.

**Monolithic 3D CMOS (b):**
	In this design, NMOS and PMOS transistors are stacked in two separate layers, enabling higher density. The P-Channel (PMOS) is placed on top of the N-Channel (NMOS), separated by an oxide layer. This approach reduces the footprint and allows better performance due to shorter interconnects.

**Single-Layer CMOS Logic (c):**	
	Shows logic gates (inverter, 2-input NAND, and 2-input NOR) built using traditional single-layer CMOS. Transistors are laid out horizontally, with interconnections taking more space.

**Monolithic 3D CMOS Logic (d):**
	Logic gates are constructed with two transistor layers (Layer 1 and Layer 2), reducing the area required for interconnections. Vertical integration improves performance and reduces delay by shortening signal paths.

![image](https://github.com/user-attachments/assets/8bd054cb-4827-4d0a-b0cc-b8a03d2ca775)



**2. Tool Installation:**

**Commands to install openroad:**
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
sudo ./setup.sh
```
![image](https://github.com/user-attachments/assets/93ea603a-6c5a-4734-b637-60bd979b20a3)

### Build tool on local machine command:
```
./build_openroad.sh --local --threads 2
```
![image](https://github.com/user-attachments/assets/464ad1c4-15b8-445f-bdb8-fed16a441451)
**Verify Installation**:
```
source ./env.sh
yosys -help
openroad -help
cd flow
make
```
![image](https://github.com/user-attachments/assets/5a749770-b023-4095-a9d0-d0df1519a973)

![image](https://github.com/user-attachments/assets/a19956e5-eb28-426f-924f-d94088a5c8c4)

![image](https://github.com/user-attachments/assets/60ad592d-6ec7-4063-a0cd-f0d6724672c3)

```
make gui_final
```
![image](https://github.com/user-attachments/assets/057be491-99e1-4336-8e5e-4bc22e42eb86)


**3. Flow Structure:**

Physical Design Flow :

![image](https://github.com/user-attachments/assets/d56698f9-2dbb-4589-affa-468219542cc3)

Physical Design is the process of translating the gate level netlist into a physical layout. This physical layout consists of various metal shapes and sizes which can be drawn onto masks and manufactured on the silicon wafer.

The Physical Design process can be broken down into multiple stages as illustrated below. It is often an iterative process where a number of optimizations are performed at each step to meet the design performance, area & power requirements.

**Floorplanning :**
	Floorplanning is the first step of physical design. The design is first partitioned into various smaller subsystems based on the system architecture and design requirements. Floorplanning determines the aspect ratio and area of the layout. Here we create the placement rows for standard cells and fix the placement of I/Os around the boundary. Any macros in the design are also placed during the floorplan stage.

Power planning is also typically done during floorplanning. The power grid network is created to distribute power to all the std cells rows, macros and all other components of the design. If there are any special IPs being used in the design then all the IP integration guidelines are also considered in floorplanning.

A good floorplan is very critical to the overall quality of your design.


**Logic Placement :**
	In this stage all the standard cells in the design are placed and assigned a legal location. After the placement EDA tools performs a number of optimizations to improve placement and congestion. A bad placement might lead to larger area utilization and timing issues.

EDA tools also use timing driven placement algorithms to optimize the placement while considering the timing requirements of the design.

**Clock Tree Synthesis :**
	During the Floorplanning & placement stage, the clock is considered as an ideal network. The optimizations in the placement stage are performed based on the assumption of an ideal clock reaching to all flops at the same time. In CTS, a clock network is created to distribute the clock to all flops. This clock tree is built using buffers or inverters along the clock paths of design in order to achieve zero/minimum skew based on design requirement. A good quality clock network is very crucial to meet the timing requirements of the design.

 **Routing :**
 	Once all the standard cells are legally placed and the clock network is synthesized, all the connecting data nets need to be laid out on the metal layers. This is done during the routing stage. After routing all the nets, a number of optimizations are performed based on the design timing requirements and analysis.

**Timing Analysis & Signoff :**
	After the design Routing, static timing analysis is performed on the design. This step is critical to analyze the performance of the design. During STA, we break down the design into timing paths and calculate the signal propagation delay along each path. Then each path is checked for violations of timing constraints. If any path is violated then these violations need to be addressed before signing off on the design.

The timing signoff ensures that all the design elements are meeting the specified timing requirements and the design is working at the desired frequency.

**Physical Verification & Signoff :**
After the routing is completed, the layout must be completely verified to ensure its correct electrical and logical functionality. The physical verification signoff ensures that the design meets all the fabrication specified rules and can be easily manufactured. Various checks that are performed at this stage includes :

    *	DRC (Design Rule Check)
    *	LVS (Layout Vs Schematic)
    *	ERC (Electrical Rule Check)
    *	Antenna check
    *	EM (Electromigration Analysis)

Once the physical verification is done, the layout is streamed out in the form of a GDSII or OASIS file for fabrication which is called design tapeout.
  
The ORFS Directory Structure and File formats:
![image](https://github.com/user-attachments/assets/1ab4548b-1811-425e-913f-e0b10f31a6b9)

```
├── OpenROAD-flow-scripts             
│   ├── docker           -> It has Docker based installation, run scripts and all saved here
│   ├── docs             -> Documentation for OpenROAD or its flow scripts.  
│   ├── flow             -> Files related to run RTL to GDS flow  
|   ├── jenkins          -> It contains the regression test designed for each build update
│   ├── tools            -> It contains all the required tools to run RTL to GDS flow
│   ├── etc              -> Has the dependency installer script and other things
│   ├── setup_env.sh     -> Its the source file to source all our OpenROAD rules to run the RTL to GDS flow
```
Now, go to flow directory
![image](https://github.com/user-attachments/assets/1ce34026-c53a-4f0b-910a-ca365253f4c8)
```
├── flow           
│   ├── design           -> It has built-in examples from RTL to GDS flow across different technology nodes
│   ├── makefile         -> The automated flow runs through makefile setup
│   ├── platform         -> It has different technology note libraries, lef files, GDS etc 
|   ├── tutorials        
│   ├── util            
│   ├── scripts             
```


## **Using above flow and commands we ran automated RTL2GDS flow for physical design of VSDbabysoc:**

Initial Steps:

*	We need to create a directory vsdbabysoc inside OpenROAD-flow-scripts/flow/designs/sky130hd
*	Now copy the folders gds, include, lef and lib from the VSDBabySoC folder in your system into this directory.
        -	The gds folder would contain the files avsddac.gds and avsdpll.gds
        -	The include folder would contain the files sandpiper.vh, sandpiper_gen.vh, sp_default.vh and sp_verilog.vh
        -	The gds folder would contain the files avsddac.lef and avsdpll.lef
        -	The lib folder would contain the files avsddac.lib and avsdpll.lib
*	Copy the constraints file(vsdbabysoc_synthesis.sdc) from the VSDBabySoC folder in your system into this directory.
*	Copy the files(macro.cfg and pin_order.cfg) from the VSDBabySoC folder in your system into this directory.
*	Create a macro.cfg file whose contents are shown below:
![image](https://github.com/user-attachments/assets/d9fe91a9-21ec-4028-9c60-441e63b4eda8)
![image](https://github.com/user-attachments/assets/b557cc55-74b8-439e-94d0-f4ce3ce6d3db)

Go to terminal and run the following commands:

```
cd OpenROAD-flow-scripts
source env.sh
cd flow
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk 
```

### Synthesis and Floorplanning

Floorplanning is a critical step in the VLSI physical design process. It involves arranging blocks and macros within the chip/core area to achieve optimal performance, power, and area efficiency while ensuring reliable routing.

## **Key Objectives**
- Minimize area, wire length, power consumption, and timing delays.
- Ensure smooth routing and chip reliability.

---

## **Inputs for Floorplanning**
- **Gate-level Netlist**: Describes the logical connectivity (`.v` file).
- **Libraries**: Physical and logical libraries (`.lefs` and `.libs`) for standard cells, macros, and IO pads.
- **Design Constraints**: Timing and power constraints (`.sdc` file).
- **RC Tech File**: (`TLU+ file`) Provides resistance-capacitance values for interconnect delays.
- **Technology File**: Describes process details (`.tf` file).
- **Partitioning Info**: Logical separation of the design.
- **Floorplanning Parameters**: Core dimensions (height, width, aspect ratio).

---

## **Outputs of Floorplanning**
- **Core/Die Area**: Defined physical layout of the design.
- **IO Placement**: Locations of input/output pins.
- **Macro Placement**: Positioned macro locations.
- **Cell Placement Areas**: Regions allocated for standard cells.
- **Power Grid**: Power distribution layout.
- **Blockages**: Restricted areas where components cannot be placed.

---

## **Control Parameters**
- **Aspect Ratio**: Determines height-to-width ratio, impacting routing and congestion.
- **Core Utilization**: Percentage of core area occupied by cells, macros, and blockages.

---

## **Floorplanning Steps**
1. **Define Dimensions**: Set core/die size.
2. **Place IO Pins**: Arrange input/output pins along chip boundaries.
3. **Power Planning**: Design power grid and distribution.
4. **Macro Placement**: Manually place macros using flylines for guidance.
5. **Create Standard Cell Rows**: Allocate areas for standard cell placement.
6. **Add Blockages**: Define regions to restrict placement or routing.

---

## **Key Concepts**
- **Standard Cell Rows**: Pre-defined areas for cell placement, organized in rows.
- **Flylines**: Virtual links guiding logical macro placement.
- **Halo (Keep-Out Margin)**: Buffer zones around macros to prevent overlap.

---

## **Impact of Poor Floorplanning**
- **Increased Area & Power**: Inefficiencies in layout can waste space and energy.
- **Timing Challenges**: Poor placement can cause timing violations.
- **Reliability Issues**: May compromise chip performance and durability.

---

## **Qualities of a Good Floorplan**
- Meets timing and congestion goals.
- Optimizes area and power usage.
- Ensures smooth routing and placement.

---

## **Automation and Tips**
- **Automatic Macro Placement**: Automated tools can generate floorplans but may need manual refinement.
- **Macro Placement Tips**:
  - Align with data flow and hierarchy.
  - Ensure macros are properly oriented with pins facing the core.
  - Maintain adequate routing channels.

---

## **Blockage Types**
- **Soft Blockages**: Can be modified during placement.
- **Hard Blockages**: Permanent restrictions.
- **Partial Blockages**: Allow some modifications to mitigate congestion.


Now run the following commands in terminal:

```

cd OpenROAD-flow-scripts

source env.sh

cd flow

```

### Commands for synthesis:

```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```
![image](https://github.com/user-attachments/assets/5b837615-65e0-47f6-a3cc-ed6a34b2d83a)

Synthesis Reports:

![image](https://github.com/user-attachments/assets/49bd00f1-d3a5-4226-909a-2eabedd4af57)
![image](https://github.com/user-attachments/assets/171b28ed-d330-4d60-88bf-3baa4284f5e3)
![image](https://github.com/user-attachments/assets/a319ae20-dbe1-467b-9bad-10590a2ed4e6)
![image](https://github.com/user-attachments/assets/729a6c42-cbf7-4219-b77a-27d1725b3290)



### Commands for floorplan:

```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```
![image](https://github.com/user-attachments/assets/a84a97e9-bed2-4492-9aa1-1ad657ffda13)
```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```

![image](https://github.com/user-attachments/assets/6c6b7640-8226-4861-ba5e-1e104399cf4c)

![image](https://github.com/user-attachments/assets/8fc92725-7fc7-4609-8681-18301a325f1a)
### Floorplan report

![image](https://github.com/user-attachments/assets/0559f6af-df78-402b-ace2-9f83aa34108b)

![image](https://github.com/user-attachments/assets/ad955d5d-5583-4202-aa80-a2cc8c6d4464)
### Commands for placement:
```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```
![image](https://github.com/user-attachments/assets/b99c7db9-9898-4fe3-a9ef-3ddc1de1eb33)


```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```
![image](https://github.com/user-attachments/assets/199f80e0-da13-4dbd-9c45-6605e9f76c4e)
![image](https://github.com/user-attachments/assets/2a5ae439-3fcc-494e-9fa1-751acd76fa0e)
![image](https://github.com/user-attachments/assets/95f7ea3e-7d88-4c4e-b970-87bff389a27f)
### place_report
![image](https://github.com/user-attachments/assets/c0a36cca-653a-4c0f-9180-ea7635f03173)
![image](https://github.com/user-attachments/assets/e3f16c6f-6dd2-4ce5-b122-06bbff990baa)

### CTS:
CTS involves connecting the clock signal from the clock port to the clock pins of sequential cells while minimizing insertion delay and balancing skew. The clock network is typically categorized as a high fanout net, which requires special handling due to its significant power consumption—often accounting for 30-40% of total chip power—and its susceptibility to electromigration (EM) effects.
Key Objectives of CTS

*	Minimize Insertion Delay: This is crucial for ensuring that the clock signal reaches all components in a timely manner, thus maintaining the overall performance of the design.

*	Balance Skew: Skew refers to the difference in arrival times of the clock signal at different sequential elements. Balancing skew is vital to ensure synchronous operation of the circuit.

*	Power Optimization: Since the clock network consumes a substantial amount of power, optimizing its design can lead to significant energy savings.

**Steps in Clock Tree Synthesis:**

The CTS process typically includes the following steps:

*	Preparation: This involves checking the legality of the design, ensuring power connections are correct, and verifying that the timing quality of results (QoR) is acceptable.

*	Clustering: Grouping sink pins based on their geometric locations to facilitate better skew management.

*	Buffer Insertion: Automatically inserting buffers and inverters along the clock paths to manage load and reduce insertion delay.

*	Balancing: Using clock buffers and inverters to achieve a balanced clock distribution across the design.

*	Post-Conditioning: Final adjustments to ensure that all design rules are met and that the clock tree operates within specified parameters for skew and insertion delay.

**Types of Clock Tree Structures:**
--------

Several structures can be utilized for building the clock tree, including:

*	H-Tree Structure: A balanced tree structure that minimizes skew.
*	X-Tree Structure: Similar to the H-tree but optimized for different geometries.
*	Geometric Matching Algorithm (GMA): A method for optimizing the layout of the clock tree.
*	Pi Tree Structure: A structure that balances loads effectively.
*	Fishbone Structure: A more complex design that can handle varying loads and distances.

**Inputs and Outputs of CTS:**
-------
**Inputs Required for CTS:**

*	Placement Database (DB): Contains the netlist after placement, including various technology files and specifications.
*	Clock Tree Specification File: Defines the requirements and constraints for the clock tree.
*	Library Files: Include information on clock buffers and inverters used in the design.

**Outputs of CTS:**

After the CTS process, the outputs typically include:

*	A netlist that reflects the clock tree configuration.
*	Timing reports detailing setup and hold times.
*	Skew and latency reports to assess clock performance.

**Quality Checks Post-CTS:**

After completing the CTS, several checks are necessary to ensure the clock tree meets design goals:

*	Insertion Delay: Must meet target values.
*	Skew Balancing: Should be within acceptable limits.
*	Signal Integrity: Ensuring minimal crosstalk and other noise effects.
*	Power Consumption: Evaluating the clock tree's power usage to ensure it aligns with design specifications.

In summary, Clock Tree Synthesis is a fundamental aspect of VLSI design that directly impacts the performance, power efficiency, and reliability of integrated circuits. Proper execution of CTS ensures that the clock signal is effectively distributed, enabling synchronous operation of all components within the design.


**Command to run Clock Tree Synthesis (CTS):**

```
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```





