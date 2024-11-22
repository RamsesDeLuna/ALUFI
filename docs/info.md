This project implements an Arithmetic Logic Unit (ALU) that performs basic operations between two 3-bit numbers (in1 and in2). The results are displayed on 7-segment displays. Below is a detailed explanation of each module's functionality and how they integrate.

1. ALU Module
Description: The ALU (Arithmetic Logic Unit) is the core of the system. It performs arithmetic and logic operations as defined by the operation code op. The four operations are:

SUM (Addition): Adds in1 and in2.
SUB (Subtraction): Subtracts in2 from in1 if in1 is greater than or equal to in2; if not, it activates the error signal.
MUL (Multiplication): Multiplies in1 by in2.
DIV (Division): Divides in1 by in2 if in2 is not zero; otherwise, the error signal is activated.
Internal Operation: The ALU calculates each operation's result and separates it into tens and units for easier visualization. These values are stored in dec_bin and unis_bin, which represent the tens and units in binary format, respectively.

Flags (Status Signals):
zero: Indicates if the result is zero.
error: Indicates if an error occurred (e.g., division by zero or negative subtraction).
2. Multiplexor2_1 Module
Description: This 2-to-1 multiplexer selects between two 4-bit inputs (J and K) based on the control signal EL. If EL is 0, it chooses input J; if 1, it selects K. This module is essential for alternating between tens and units on the 7-segment displays.

3. Frequency Divider Module
Description: The frequency divider adjusts the switching speed between the tens and units on the display, creating a rapid flicker so that both digits appear simultaneously to the human eye.

Internal Operation: This module counts clock pulses in a register q. The output signals q_int1 and q_int2 divide the base clock (mclk), providing a switching signal that controls the alternating display.

4. Multiplex_decoBCD Module
Description: This module connects the 2-to-1 multiplexer and the BCD (Binary-Coded Decimal) decoder to alternate between tens and units on the 7-segment display. It takes the decimal number from the ALU (separated into tens and units) and sends it to the decoder according to the SEL signal from the frequency divider. This allows both the tens and units to be shown on the same display alternately.

Multiplexor2_1 (Stage 0): Selects between the ALU’s tens and units, switching between them based on SEL.
BCD Decoder (Stage 1): Converts the BCD number (digit from 0 to 9) to the A-G segments for the display.
5. BCD Decoder Module
Description: This decoder converts a 4-bit input (BCD) to a 7-segment display format. Each input combination (W, X, Y, Z) represents a number from 0 to 9, and the decoder activates the corresponding segments to form the desired digit on the display.

Case 4'b0000: Activates segments to show "0" on the display.
Cases 4'b0001 to 4'b1001: Activates segments for numbers 1 through 9.
6. ALU_Display Module
Description: This top-level module integrates all other modules, connecting them and coordinating their functions to achieve the system's overall functionality.

Inputs:

mclk: Base clock signal.
in1 and in2: Inputs to the ALU.
op: 2-bit operation code.
Outputs:

select_disp: Display selection for showing tens and units.
AE to GE: Segments of the 7-segment display.
zero: Zero flag from the ALU.
error: Error flag from the ALU.
Internal Operation:

ALU (Stage 0): Performs the operation specified by op and splits the result into tens (num1mux) and units (num2mux).
Frequency Divider (Stage 2): Controls the alternating speed between the tens and units so that the display shows both digits in the same position.
Multiplex_decoBCD (Stage 1): Selects and decodes the numbers num1mux and num2mux into display segments (AE to GE) to display the final value.
Display Selection (select_disp): The system selects one display at a time using select_disp[0] and select_disp[1] to alternate between tens and units. Bits select_disp[2] and select_disp[3] are held high to keep the other displays off.

Summary of the Complete Operation
ALU Operation: The ALU receives two 3-bit numbers (in1 and in2) and an operation code (op) and performs the specified operation.
Result Decomposition: The ALU converts the result into tens and units, which are the digits to be displayed.
Display Alternation: The frequency divider alternates quickly between tens and units. This flicker is imperceptible to the human eye, making both digits appear to display simultaneously.
Multiplexing and BCD Decoding: The multiplexer selects between tens and units, and the BCD decoder converts the value to display segments.
Display Output: Finally, the 7-segment display shows the result, while the zero and error flags indicate if the result is zero or if there was an error in the operation.
