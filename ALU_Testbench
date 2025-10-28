
// ===========================================================
//  TESTBENCH for 12-bit Pipelined ALU
// ===========================================================
`timescale 1ns/1ps

module ALU_tb;

    // ----------------------------------------------
    // Parameters
    // ----------------------------------------------
    parameter WIDTH = 12;
    parameter PIPELINE_LATENCY = 2; // 1-cycle latency (matches pipelined ALU)

    // ----------------------------------------------
    // Signals
    // ----------------------------------------------
    logic signed [WIDTH-1:0] A, B;
    logic signed [3:0] SEL;
    logic CLK, RST;

  logic signed [WIDTH-1:0] Result;
    logic Z, C, V, N;

    // ----------------------------------------------
    // Instantiate DUT
    // ----------------------------------------------
    ALU #(.WIDTH(WIDTH)) uut (
        .A(A),
        .B(B),
        .SEL(SEL),
        .CLK(CLK),
        .RST(RST),
      .F(Result),
        .Z(Z),
        .C(C),
        .V(V),
        .N(N)
    );

    // ----------------------------------------------
    // Clock generation (10 ns period)
    // ----------------------------------------------
    initial CLK = 0;
    always #5 CLK = ~CLK;

    // ----------------------------------------------
    // Waveform dump 
    // ----------------------------------------------
    initial begin
        $dumpfile("ALU_tb.vcd");
        $dumpvars(0, ALU_tb);
    end

    // ----------------------------------------------
    // Task: perform and check one operation
    // ----------------------------------------------
    task automatic check_op(
        input signed [WIDTH-1:0] inA,
        input signed [WIDTH-1:0] inB,
        input signed [3:0]       inSEL,
        input signed [WIDTH-1:0] expected,
        input string      opname
    );
        begin
            @(posedge CLK);
            A = inA;
            B = inB;
            SEL = inSEL;

            // Wait for pipeline latency
            repeat (PIPELINE_LATENCY) @(posedge CLK);

          if (Result === expected)
                $display("[PASS] %-6s | A=%0d, B=%0d => F=%0d", opname, inA, inB, Result);
            else
                $display("[FAIL] %-6s | A=%0d, B=%0d => F=%0d (Expected %0d)", opname, inA, inB, Result, expected);
        end
    endtask

    // ----------------------------------------------
    // Main stimulus
    // ----------------------------------------------
    initial begin
        $display("==== Starting 12-bit Pipelined ALU Testbench ====");

        // Reset phase
        RST = 0;
        A = 0;
        B = 0;
        SEL = 0;
        repeat (2) @(posedge CLK);
        RST = 1;
        @(posedge CLK);

        // --- Arithmetic operations ---
        check_op(12'd15, 12'd1,  4'b0000, 12'd16,  "ADD");
        check_op(12'd5,  12'd15, 4'b0001, 12'd4096-10, "SUB"); // wrap-around example
        check_op(12'd3,  12'd4,  4'b0010, 12'd12,  "MUL");
        check_op(12'd8,  12'd2,  4'b0011, 12'd4,   "DIV");

        // --- Shift operations ---
        check_op(12'b0000_1111_0000, 0, 4'b0100, 12'b0001_1110_0000, "SHL");
        check_op(12'b0000_1111_0000, 0, 4'b0101, 12'b0000_0111_1000, "SHR");

        // --- Logic operations ---
        check_op(12'b0000_1111_0000, 12'b0000_0000_1111, 4'b0110, 12'b0000_0000_0000, "AND");
        check_op(12'b0000_1111_0000, 12'b0000_0000_1111, 4'b0111, 12'b0000_1111_1111, "OR");
        check_op(12'b0000_1111_0000, 12'b0000_0000_1111, 4'b1000, 12'b0000_1111_1111, "XOR");

        // --- Unary operations ---
        check_op(12'b0000_1111_0000, 0, 4'b1001, ~12'b0000_1111_0000, "NOT");
        check_op(12'd15,  0, 4'b1010, 12'd16, "INC");
        check_op(12'd15,  0, 4'b1011, 12'd14, "DEC");
        check_op(12'd15,  0, 4'b1100, 12'd0,  "ZERO");

        // --- End ---
        $display("==== Testbench Complete ====");
        $finish;
    end

endmodule
// ===========================================================

