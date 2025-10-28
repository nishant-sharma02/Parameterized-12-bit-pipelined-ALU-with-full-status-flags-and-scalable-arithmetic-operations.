 
// ==================== 12-bit Pipelined ALU ==================== //
`timescale 1ns/1ps
module ALU #(
    parameter int WIDTH = 12
)(
    input  logic [WIDTH-1:0] A,     // Operand A
    input  logic [WIDTH-1:0] B,     // Operand B
    input  logic [3:0]       SEL,   // Operation select
    input  logic             CLK,   // Clock signal
    input  logic             RST,   // Active-low synchronous reset

    output logic [WIDTH-1:0] F,     // Final output result
    output logic             Z,     // Zero flag
    output logic             C,     // Carry flag
    output logic             V,     // Overflow flag
    output logic             N      // Negative flag
);

    // ---- Stage 1 pipeline registers ----
    logic [WIDTH-1:0] A_reg, B_reg;
    logic [3:0]       SEL_reg;
    logic [WIDTH:0]   temp_result_stage1;  // One extra bit for carry
    logic             C_stage1, V_stage1, N_stage1, Z_stage1;

    // ================= STAGE 1: Compute (Execute) ================= //
    always_ff @(posedge CLK) begin
        if (!RST) begin
            A_reg              <= '0;
            B_reg              <= '0;
            SEL_reg            <= '0;
            temp_result_stage1 <= '0;
            C_stage1           <= '0;
            V_stage1           <= '0;
            N_stage1           <= '0;
            Z_stage1           <= '0;
        end 
        else begin
            // Capture inputs
            A_reg   <= A;
            B_reg   <= B;
            SEL_reg <= SEL;

            // Perform operation
            unique case (SEL)
                4'b0000: temp_result_stage1 = A + B;                    // ADD
                4'b0001: temp_result_stage1 = A - B;                    // SUB
                4'b0010: temp_result_stage1 = A * B;                    // MUL
                4'b0011: temp_result_stage1 = (B != 0) ? A / B : '0;    // DIV (safe)
                4'b0100: temp_result_stage1 = A << 1;                   // SHIFT LEFT
                4'b0101: temp_result_stage1 = A >> 1;                   // SHIFT RIGHT
                4'b0110: temp_result_stage1 = A & B;                    // AND
                4'b0111: temp_result_stage1 = A | B;                    // OR
                4'b1000: temp_result_stage1 = A ^ B;                    // XOR
                4'b1001: temp_result_stage1 = ~A;                       // NOT
                4'b1010: temp_result_stage1 = A + 1;                    // INC
                4'b1011: temp_result_stage1 = A - 1;                    // DEC
                4'b1100: temp_result_stage1 = '0;                       // ZERO
                default: temp_result_stage1 = '0;
            endcase

            // Compute flags (Stage 1)
            Z_stage1 <= (temp_result_stage1[WIDTH-1:0] == 0);
            N_stage1 <= temp_result_stage1[WIDTH-1];
            C_stage1 <= temp_result_stage1[WIDTH];
            V_stage1 <= (A[WIDTH-1] == B[WIDTH-1]) &&
                        (temp_result_stage1[WIDTH-1] != A[WIDTH-1]);
        end
    end

    // ================= STAGE 2: Output Register ================= //
    always_ff @(posedge CLK) begin
        if (!RST) begin
            F <= '0;
            Z <= '0;
            C <= '0;
            V <= '0;
            N <= '0;
        end 
        else begin
            F <= temp_result_stage1[WIDTH-1:0];
            Z <= Z_stage1;
            C <= C_stage1;
            V <= V_stage1;
            N <= N_stage1;
        end
    end

endmodule
// ================================================================================ //

