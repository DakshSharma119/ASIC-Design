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
