## Timing in Pipeline Design

Timing is crucial in pipeline design as it ensures the seamless progression of instructions through different pipeline stages. The key elements of timing include:

### Global Timing Mechanism
- The **clock signal** serves as the global timing mechanism.
- It synchronizes the movement of instructions, ensuring that they proceed to the next stages simultaneously.

### Pipeline Registers (Latches)
- **Pipeline registers** are inserted between consecutive pipeline stages.
- They act as storage elements, holding the instructions temporarily during each stage.
- There are four pipeline registers in this case, named after their locations:
  - IF-OF (Instruction Fetch to Instruction Decode)
  - OF-EX (Instruction Decode to Execute)
  - EX-MA (Execute to Memory Access)
  - MA-RW (Memory Access to Register Write)
- All pipeline register is connected to the same common clock.
- They read and write data at the same time, ensuring proper synchronization.

### Instruction Progression Example
- When an instruction enters the pipeline, it first goes into the IF (Instruction Fetch) stage.
- At the end of the first cycle, it gets saved in the IF-OF register.
- In the second cycle, it moves to the OF (Instruction Decode) stage.
- At the end of the second cycle, it gets latched into the OF-EX register.
- This pattern continues as the instruction progresses through the pipeline.
- At the end of each cycle (negative edge of the clock), the instruction transfers from one pipeline register to the next.
- This process continues until the instruction reaches the end of the pipeline.

By utilizing pipeline registers and coordinating their operations with the clock signal, instructions can seamlessly move through the pipeline stages, enabling efficient parallel processing.

**Note:**
- Clock signal ensures synchronized progression.
- Pipeline registers store instructions between stages.
- Each register is connected to the clock.
- Instructions move from one register to the next at the end of each cycle.
- Instructions progress through stages until they reach the pipeline's end.

Remember:
- Clock syncs instructions.
- Registers store instructions.
- Each register connected to clock.
- Movement happens at cycle ends.
- Instructions move until pipeline end.
## The Instruction Packet

In pipeline design, we use an instruction packet to encapsulate all the necessary information and intermediate results of an instruction. This packet ensures proper handling and synchronization of multiple instructions within the processor. It contains:

1. **Instruction Details**: The packet includes all the details of the instruction, such as the opcode and operands.

2. **Intermediate Results**: Intermediate results generated during the instruction's execution are also included in the packet. These results are required by subsequent stages for further processing.

3. **Control Signals**: Control signals required to execute the instruction are part of the packet. These signals enable the correct operation of the pipeline stages.

The purpose of the instruction packet is to prevent overlap between the information needed for different instructions. By confining all the necessary information in a packet and transferring it between pipeline registers every cycle, we ensure that the information is properly synchronized and processed. Additionally, once an instruction leaves the pipeline, all its intermediate states are cleared, maintaining a clean state.

The content of the instruction packet varies as the instruction progresses through the pipeline stages. Initially, it contains the Program Counter (PC) and the instruction's contents. As it moves through the stages, it may also include other operands and control signals required by subsequent stages.

To maintain uniformity, all pipeline registers have the same size, capable of holding the entire instruction packet. Although some fields in the packet may not be used for certain instructions, this overhead is negligible.

Now, let's take a quick look at each of the pipeline stages in our pipelined data path, which follows the same design as the single-cycle processor:

1. **Instruction Fetch (IF)**: Retrieves the instruction from memory based on the PC.

2. **Operand Fetch (OF)**: Decodes the instruction and extracts the necessary operands.

3. **Execute (EX)**: Performs the actual computation or operation based on the decoded instruction.

4. **Memory Access (MA)**: If needed, accesses memory to read or write data.

5. **Register Write (RW)**: Writes the results of the instruction to the appropriate registers.

After each pipeline stage (except the last one, RW), a register is added to store the instruction packet temporarily. Additionally, connections are established to transfer data in and out of the pipeline registers.

Understanding the data path and the flow of instructions through the pipeline stages is crucial for designing an efficient and effective pipelined processor.

Remember:
- Instruction packet contains instruction details, intermediate results, and control signals.
- Ensures no overlap between instructions.
- Information transferred between pipeline registers.
- Packet content varies throughout the stages.
- Uniform size for all pipeline registers.
- Pipelined data path follows the same design as a single-cycle processor.
- Registers added after each stage (except RW).
- Connections for data transfer established.

## Pipeline stages basic
### Instruction Fetch - Fetch Unit

In the pipeline, the first stage is the instruction fetch unit, which retrieves the instruction from the main memory. The instruction in our SimpleRisc architecture is encoded as a 32-bit sequence or 4 bytes. To fetch an instruction, we need the starting address of the instruction, which is stored in a register called the program counter (pc).

Important Distinction:
- **PC**: Acronym for "program counter," a general concept representing the current instruction address.
- **pc**: A register in our pipeline that holds the contents of the program counter.

To update the pc and point it to the next instruction, we need a mechanism that considers whether the instruction is a branch or not:
- If the instruction is not a branch, the pc should point to the next instruction's starting address, which is the value of the old pc plus 4 (since each instruction is 4 bytes long).
- If the instruction is a taken branch, the new value of the pc should be equal to the address of the branch target. Otherwise, the address of the next instruction is the default value (current pc + 4).

The circuit implementation for the fetch unit involves two main operations performed in a cycle:
1. Computation of the next pc.
2. Fetching the instruction.

The next pc can be obtained from two sources in SimpleRisc:
- Incrementing the current pc by 4 using an adder.
- Obtaining the address from another unit that calculates the branch target (branchPC) when a branch is taken. A multiplexer is used to choose between these two inputs. Once the correct input is selected, it is saved in the pc register and sent to the memory system to fetch the instruction.

In this design, it uses a separate instruction memory (Harvard Machine) rather than a combined memory for both instruction and data (Von Neumann Machine). The instruction memory is typically implemented as an array of SRAM cells. The fetch unit provides the address in the SRAM array, and the 32 bits stored at that specified starting address are considered as the instruction's contents.

Before proceeding to decode the instruction, it's important to note the external inputs of the fetch unit, which include:
1. Branch target (branchPC).
2. Instruction contents.
3. Signal to control the multiplexer (isBranchTaken).

The branch target is usually provided by the decode unit or the instruction execution unit. The instruction contents are obtained from the instruction memory. The signal to control the multiplexer (isBranchTaken) determines which input is selected based on certain conditions.

Conditions for setting isBranchTaken are:  
In our processor, a dedicated branch unit generates the isBranchTaken signal. For non-branch instructions or call/ret/b instructions, isBranchTaken can be decided based on the conditions in Table 8.1. However, for conditional branch instructions (beq/bgt), analysis of the flags register is necessary. The flags register contains the results of the last compare instruction.

Remember:
- Instruction fetch is the first stage in the pipeline.
- Fetch unit retrieves the instruction from main memory.
- Instruction encoded as 32-bit sequence (4 bytes).
- Starting address of the instruction stored in the program counter (pc).
- Mechanism to update pc for the next instruction:
  - Non-branch instructions: pc = old pc + 4.
  - Taken branches: pc = branch target address.
  - Default: pc = current pc + 4.
  - Note: here 4 refers to 4 **bytes**.
- Fetch unit circuit performs next pc computation and instruction fetching.
- Two sources for the next pc: incrementing pc by 4 or obtaining address from branch target.
- Multiplexer selects the correct input for the next pc.
- pc register stores the  selected input and is used for instruction fetching.
- Separate instruction memory (Harvard Machine) commonly used.
- External inputs for the fetch unit: branch target (branchPC), instruction contents, and isBranchTaken signal.
- Conditions for setting isBranchTaken determined by the branch unit.
- Flags register analyzed for conditional branch instructions.
## Data Path and Control Path

In a processor, there are two types of elements: data elements and control elements. The data elements include registers, memories, and arithmetic/logic circuits responsible for processing data values. On the other hand, the control elements, known as the control unit, determine the flow and direction of data. The control unit generates signals to control multiplexers and other components involved in the data flow. These signals, known as control signals, regulate the flow of information within the processor.

Conceptually, a processor can be divided into two subsystems: the data path and the control path. The data path comprises elements involved in storing and processing information. This includes the data memory, instruction memory, register file, and the arithmetic logic unit (ALU). The memories and register file store information, while the ALU performs computations and operations on the data.

On the other hand, the control path directs the flow of information by generating control signals. An example of this is seen, where the control path generates a signal to select between the branch target and the default next program counter (PC) using a multiplexer controlled by the isBranchTaken signal.

To understand the relationship between the data path and control path, we can use the analogy of a city's traffic network. The roads and traffic lights represent the data path, where cars (instructions) flow. The control path is similar to the circuits that control the traffic lights. It determines the timing and transitions of the lights to manage the flow of traffic effectively. In modern smart cities, the control of traffic lights is often integrated to intelligently route cars around congested areas and accidents. Similarly, the control unit in a processor is responsible for executing instructions efficiently.

To summarize:
- **Data Path**: Consists of elements dedicated to storing, retrieving, and processing data, such as register files, memories, and ALUs.
- **Control Path**: Primarily contains the control unit, which generates signals to control the movement of instructions and data in the data path.
### OF unit
The Operand Fetch Unit is responsible for two main functions in the processor: (1) calculating the values of immediate operands and branch targets, and (2) reading the values of the source registers.

1. Computation of the Immediate Operand and Branch Target:
   - To calculate the immediate operand, the imm field (bits 1-18) is extracted from the instruction.
   - The lower 16 bits are then extracted, and a 32-bit constant is created based on the modifiers (bits 17 and 18).
   - Depending on the modifier, different operations are performed:
     - No modifier: The sign of the 16-bit number is extended to make it a 32-bit number.
     - u modifier: The top 16 bits are filled with zeros.
     - h modifier: The 16-bit number is shifted 16 positions to the left.
   - The resulting 32-bit value is referred to as immx.
   - Divide the 18 bits into two parts – 2 bits (modifier) + 16 bits (constant part of the immediate). The two modifier bits can take three values – 00 (default), 01 (‘u’), and 10 (‘h’). The remaining 16 bits are used to specify a 16-bit 2’s complement number when we are using default modifiers. For the u and h modifiers, we assume that the 16-bit constant in the immediate field is an unsigned number
   - Here's a table showing the different modifier types based on the values of the 17th and 18th bits and their corresponding interpretations:

      | 17th Bit | 18th Bit | Modifier | Interpretation                              |
      |----------|----------|----------|---------------------------------------------|
      | 0        | 0        | No       | Sign-extend the 16-bit number                |
      | 0        | 1        | u        | Fill top 16 bits with 0s (unsigned)          |
      | 1        | 0        | h        | Shift 16-bit number left by 16 positions      |
      | 1        | 1        | Reserved | Reserved (specific interpretation not defined) |

      - Please note that the "Reserved" category indicates that the specific combination of the 17th and 18th bits is not defined or reserved for future use.

   - Similarly, the branch target (branchTarget) is computed for all types of branches except the ret instruction.
   - An instruction takes 4 bytes. If we assume that all instructions are aligned to 4-byte boundaries, then all starting memory addresses of instructions will be a multiple of 4. Hence, the least two significant binary digits of the address will be 00. There is no reason for wasting bits in trying to specify them. We can assume that the 27 bits specify the offset of the address of the memory word (in units of 4-byte memory words) that contains the instruction. With this optimisation, the offset from the PC in terms of bytes becomes 29 bits.
   - The 27-bit offset (bits 1 to 27) is extracted from the instruction.
   - The offset is left-shifted by 2 bits to convert it into a 29-bit number representing memory words.
   - The sign of the offset is extended to make it a 32-bit number.
   - For PC-relative addressing, the shifted offset is added to the value of the program counter (PC) to obtain the branch target.
   - In the case of the ret instruction, the branch target is the contents of the ra register, which is obtained from the register file.
   - The branch target can be derived either from the instruction (branchTarget signal) or from the ra register. The selection is made in the next stage to compute branchPC.

   - It's important to note that although both branchTarget and immx are calculated for all instructions, each instruction type only requires one of these fields. The other field will contain junk values. However, it's faster to pre-compute both values to ensure the correct value is used in subsequent stages.

2. Reading the Registers:
   - The values of the source registers are read in parallel.
   - The register file has 16 registers, two read ports, and one write port.
   - To read the first register operand (op1), there are two choices:
     - For ALU and memory instructions, the first source register (rs1) specified by bits 19 to 22 in the instruction is read.
     - For the ret instruction, the value of the return address register (ra) is read.
     - A multiplexer is used to select between rs1 and ra, controlled by the isRet signal. If isRet is 1, ra is chosen; otherwise, rs1 is chosen.
     - The output of the first read port is denoted as op1.

   - A similar multiplexer is used for the second read port of the register file.
   - For all instructions except the store instruction, the second source register (rs2) is specified by bits 15 to 18 in the instruction.
   - However, for the store instruction, the source register is specified by the rd field (bits 23 to 26).
   - To determine whether the instruction is a store, a comparison is performed by checking if the opcode is equal to 01111.
   - The result of the comparison sets the isSt signal to indicate whether it's a store instruction.
   - The multiplexer for the second register operand (op2) is controlled by the isSt signal. If isSt is 1, rd is chosen; otherwise, rs2 is chosen.

   - The opcode (5 bits) and the immediate bit (1 bit) are also sent to the control unit to generate the necessary control signals.

## Execute Unit

In the execution stage of the pipeline, instructions are divided into two types: branch instructions and non-branch instructions.  
   - The branch instructions are handled by a dedicated branch unit, while the non-branch instructions are processed by the ALU (arithmetic logic unit).

Branch Unit:
The circuit for the branch unit is shown in Figure 8.8. It involves the following components:
- A multiplexer is used to select between the value of the return address (op1) and the branch target embedded in the instruction. The selection is controlled by the isRet signal. If isRet = 1, op1 is chosen; otherwise, the branch target is selected. The output of the multiplexer is the branchPC, which is sent to the fetch unit.
- To determine the outcome of a branch instruction, let's take the example of the beq instruction. The SimpleRisc instruction set requires a flags register that contains the result of the last compare (cmp) instruction. The flags register has two bits: E (for equality) and GT (for greater than). For the beq instruction, the isBeq signal is set to 1. We need to compute a logical AND of this signal and the value of the E bit in the flags register. If both are 1, the branch is taken. Similar logic applies to the bgt instruction. For unconditional branches (call/ret/b), the isUBranch signal is set to 1. If any of these conditions are true, the branch is taken. An OR gate is used to compute the outcome of the branch and set the isBranchTaken signal, which is used by the fetch unit to control the multiplexer generating the next PC.

ALU:
Figure 8.9 illustrates the ALU portion of the execution unit. It consists of the following components:
- The first operand (A) of the ALU is always op1 obtained from the operand fetch unit. The second operand (B) can be either a register or the sign-extended immediate value. The selection is determined by the isImmediate signal generated by the control unit. If isImmediate = 1, the immediate value (immx) is chosen as the operand. Otherwise, op2 is selected.
- The ALU takes a set of signals called aluSignals as input, which specify the type of ALU operation to be performed.
- The ALU performs various arithmetic and logical operations, such as addition, subtraction, multiplication, division, shifting, and logical operations (AND, OR, NOT). Each operation is performed by a dedicated module within the ALU. Each module has an enabling signal that determines whether it is active or disabled, conserving power when not needed.
- The output of the ALU is referred to as aluResult.

The ALU design, shown in Figure 8.10, consists of modules for different operations. Each module is enabled or disabled based on the corresponding signal. For example, the adder module is used by add, sub, and cmp instructions, as well as by load and store instructions to compute memory addresses. The multiplier and divider modules function in a similar manner. The shift unit performs left and right shifts, and the logical unit performs AND, OR, and NOT operations. The Mov unit simply outputs the value of B when the isMov signal is true.

The full design of the execution unit (branch unit and ALU) is depicted in Figure 8.12. It includes the branch unit circuitry, the ALU modules, and the output multiplexer (not shown in the figure). The ALU output, aluResult, is determined by selecting the appropriate output from the ALU modules using a multiplexer.

## Pipeline Stages Modification

### IF Stage

- Save the value of the Program Counter (PC) and the instruction contents in the IF-OF pipeline register.
- No significant changes are required in this part of the data path.

### OF Stage

- Design of the operand fetch stage.
- Extract fields (rd, rs1, rs2, immediate) from the instruction.
- Send extracted fields to the register file, immediate, and branch units.
- Send the instruction contents to the control unit to generate control signals.
- Save control signals in the OF-EX pipeline register (field: control).
- Allocate fields in the instruction packet and OF-EX register to store intermediate results (branchTarget, ALU inputs A and B, and op2).
- Ensure all necessary instruction details, intermediate results, and control signals are saved in pipeline registers.

### EX Stage

- ALU receives inputs (A and B) from the OF-EX pipeline register.
- Results generated: aluResult (ALU operation result), final branch target, and branch outcome (1 if branch taken, 0 if not).
- ALU result added to the instruction packet and saved in the EX-MA register.
- EX-MA register also contains other fields of the instruction packet (PC, instruction, control signals) and the second operand read from the register file (op2).
- Compute the final branch target by choosing between the branch target computed in the OF stage and the value of the return address register (A).
- Final branch target (branchPC) sent to the fetch unit.
- Branch unit determines the value of the isBranchTaken signal. If 1, the instruction is a branch and taken; otherwise, the fetch unit uses the default option to fetch the next PC.

### MA Stage

- Load instruction uses ALU result as the effective memory address, saved in the aluResult field of the EX-MA register.
- Data to be stored is in the rd register, read from the register file and stored in the op2 field of the instruction packet.
- Connect op2 field to the Memory Data Register (MDR) register.
- Relevant control signals (isLd and isSt) are part of the instruction packet and routed to the memory unit.
- Output of this stage is the result of the load instruction, saved in the ldResult field of the MA-RW register.

### RW Stage

- Inputs required from previous stages: ALU value (aluResult) and load operation value (ldResult) stored in their respective fields.
- Inputs, along with the default next PC (current PC + 4), connected to a multiplexer to choose the value to be written back.
- Rest of the circuit remains the same as in the single-cycle processor.
- No pipeline register at the end of the RW stage since it is the last stage in the pipeline.

> Notes Reference: Basic Architecture by Smruti R. Sarangi
