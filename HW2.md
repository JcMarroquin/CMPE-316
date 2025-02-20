>>> HW 2 - BN75779

## PROBLEM 1 ##
Source code:

~~~verilog
// File: problem1_if_else.v
// Description: Behavioral implementation using if-else statements
module problem1_if_else (
    input wire a, b, c,  // Inputs
    output reg u, v       // Outputs (reg because they are assigned in an always block)
);

    // Always block to describe the behavior
    always @(*) begin
        // Default values for outputs
        u = 0;
        v = 0;

        // Truth table implementation
        if (a == 1 && b == 1 && c == 1) begin
            u = 0;
            v = 1;
        end
        else if (a == 1 && b == 1 && c == 0) begin
            u = 1;
            v = 1;
        end
        else if (a == 1 && b == 0 && c == 0) begin
            u = 0;
            v = 0;
        end
        // For all other cases, u = 0 and v = Don't Care (already set by default)
    end

endmodule
~~~

~~~verilog
// File: problem1_assign.v
// Description: Implementation using concurrent continuous assignment statements

module problem1_assign (
    input wire a, b, c,  // Inputs
    output wire u, v     // Outputs
);

    // Continuous assignments for outputs
    assign u = (a & b & ~c) ? 1 : 0;  // u = 1 when a=1, b=1, c=0; otherwise u=0
    assign v = (a & b) ? 1 : 0;       // v = 1 when a=1, b=1; otherwise v=0
~~~

My test bench:

~~~verilog
// File: tb_problem.v
module tb_problem1;

    // Declare inputs and outputs
    reg a, b, c;
    wire u_if_else, v_if_else;
    wire u_assign, v_assign;

    // Instantiate both modules
    problem1_if_else uut_if_else (
        .a(a),
        .b(b),
        .c(c),
        .u(u_if_else),
        .v(v_if_else)
    );

    problem1_assign uut_assign (
        .a(a),
        .b(b),
        .c(c),
        .u(u_assign),
        .v(v_assign)
    );

    // Test all possible input combinations
    initial begin
        // Test case 1: a=1, b=1, c=1
        a = 1; b = 1; c = 1;
        #10;
        $display("Input: a=%b, b=%b, c=%b | Output (if-else): u=%b, v=%b | Output (assign): u=%b, v=%b",
                 a, b, c, u_if_else, v_if_else, u_assign, v_assign);

        // Test case 2: a=1, b=1, c=0
        a = 1; b = 1; c = 0;
        #10;
        $display("Input: a=%b, b=%b, c=%b | Output (if-else): u=%b, v=%b | Output (assign): u=%b, v=%b",
                 a, b, c, u_if_else, v_if_else, u_assign, v_assign);

        // Test case 3: a=1, b=0, c=0
        a = 1; b = 0; c = 0;
        #10;
        $display("Input: a=%b, b=%b, c=%b | Output (if-else): u=%b, v=%b | Output (assign): u=%b, v=%b",
                 a, b, c, u_if_else, v_if_else, u_assign, v_assign);

        // Test case 4: a=0, b=0, c=0
        a = 0; b = 0; c = 0;
        #10;
        $display("Input: a=%b, b=%b, c=%b | Output (if-else): u=%b, v=%b | Output (assign): u=%b, v=%b",
                 a, b, c, u_if_else, v_if_else, u_assign, v_assign);

        $finish;
    end

endmodule
~~~

Results:
~~~
Input: a=1, b=1, c=1 | Output (if-else): u=0, v=1 | Output (assign): u=0, v=1
Input: a=1, b=1, c=0 | Output (if-else): u=1, v=1 | Output (assign): u=1, v=1
Input: a=1, b=0, c=0 | Output (if-else): u=0, v=0 | Output (assign): u=0, v=0
Input: a=0, b=0, c=0 | Output (if-else): u=0, v=0 | Output (assign): u=0, v=0
~~~

Schematic (RTL View):

![screenshot of](media/Problem1_RTL.png)


## PROBLEM 2 ##
Source code:
~~~verilog
// File: p2.v
// Description: Verilog module to load a 3-bit value or perform a logical right shift.
module p2 (
    input wire clk,              // Clock signal
    input wire rst,              // Reset signal (active high)
    input wire load_shift_n,     // Control signal: 1 = load, 0 = shift
    input wire [2:0] loadValue,  // 3-bit input value to load
    output wire [2:0] q,         // 3-bit output register (declared as wire)
    output wire zero             // Zero flag (high when q == 000)
);

    // Internal register
    reg [2:0] reg_q;

    // Zero flag logic
    assign zero = (reg_q == 3'b000);

    // Update the register on the rising edge of the clock
    always @(posedge clk) begin
        if (rst) begin
            // Reset the register to 000
            reg_q <= 3'b000;
        end
        else begin
            if (load_shift_n) begin
                // Load the input value
                reg_q <= loadValue;
            end
            else begin
                // Perform a logical right shift
                reg_q <= {1'b0, reg_q[2:1]};
            end
        end
    end
    // Assign the internal register to the output
    assign q = reg_q;
endmodule
~~~

My test bench:

~~~verilog
//Filename: p2_tb.v
module p2_tb;
    // Testbench signals
    reg clk;
    reg rst;
    reg [2:0] loadValue;
    reg load_shift_n;
    wire [2:0] q;
    wire zero;

    // Instantiate the DUT
    p2 dut (.clk(clk), .rst(rst), .load_shift_n(load_shift_n), .loadValue(loadValue), .q(q), .zero(zero));

    // Clock generation
    always #5 clk = ~clk;

    initial begin
        clk = 0;
        rst = 1;
        loadValue = 3'b101;
        load_shift_n = 1;
        
        #10 rst = 0; // Release reset
        #10 load_shift_n = 1; // Load value
        #10 $display("Time: %0t | q: %b | zero: %b", $time, q, zero);
        
        #10 load_shift_n = 0; // Start shifting
        repeat (3) begin
            #10 $display("Time: %0t | q: %b | zero: %b", $time, q, zero);
        end
        
        $finish;
    end
endmodule
~~~

Results:
~~~
Time: 30000 | q: 101 | zero: 0
Time: 50000 | q: 010 | zero: 0
Time: 60000 | q: 001 | zero: 0
Time: 70000 | q: 000 | zero: 1
~~~

## PROBLEM 3 ##
Source code:
~~~verilog
//Filename: combinational_logic.v
module combinational_logic (
    input wire [3:0] dataInA,
    input wire [3:0] dataInB,
    input wire [2:0] s,
    output reg [3:0] result
);

    always @(*) begin
        case (s)
            3'b000: result = dataInA & dataInB; // AND
            3'b001: result = dataInA | dataInB; // OR
            3'b010: result = ~(dataInA & dataInB); // NAND
            3'b011: result = ~(dataInA | dataInB); // NOR
            3'b100: result = dataInA ^ dataInB; // XOR
            default: result = 4'b0000; // Default case
        endcase
    end

endmodule
~~~

~~~verilog
//Filename: combinational_logic_tb.v
module combinational_logic_tb;
    reg [3:0] dataInA;
    reg [3:0] dataInB;
    reg [2:0] s;
    wire [3:0] result;

    // Instantiate the DUT
    combinational_logic dut (
        .dataInA(dataInA),
        .dataInB(dataInB),
        .s(s),
        .result(result)
    );

    initial begin
        // Test cases
        dataInA = 4'b1010;
        dataInB = 4'b1100;
        for (s = 0; s < 5; s = s + 1) begin
            #10;
            $display("s: %b | A: %b | B: %b | Result: %b", s, dataInA, dataInB, result);
        end
        
        $finish;
    end
endmodule
~~~

Results:
~~~
s: 000 | A: 1010 | B: 1100 | Result: 1000
s: 001 | A: 1010 | B: 1100 | Result: 1110
s: 010 | A: 1010 | B: 1100 | Result: 0111
s: 011 | A: 1010 | B: 1100 | Result: 0001
s: 100 | A: 1010 | B: 1100 | Result: 0110
~~~

## PROBLEM 4 ##
Source code:
~~~verilog
module registered_combinational_logic (
    input wire clk,
    input wire [3:0] dataInA,
    input wire [3:0] dataInB,
    input wire [2:0] s,
    output reg [3:0] q
);

    reg [3:0] result;
    always @(*) begin
        case (s)
            3'b000: result = dataInA & dataInB; // AND
            3'b001: result = dataInA | dataInB; // OR
            3'b010: result = ~(dataInA & dataInB); // NAND
            3'b011: result = ~(dataInA | dataInB); // NOR
            3'b100: result = dataInA ^ dataInB; // XOR
            default: result = 4'b0000; // Default case
        endcase
    end

    always @(posedge clk) begin
        q <= result;
    end
endmodule
~~~

Results:
~~~
s: 000 | A: 1010 | B: 1100 | Result: 1000
s: 001 | A: 1010 | B: 1100 | Result: 1110
s: 010 | A: 1010 | B: 1100 | Result: 0111
s: 011 | A: 1010 | B: 1100 | Result: 0001
s: 100 | A: 1010 | B: 1100 | Result: 0110
~~~




## Appendix 
Note that on many linux systems a document converter, pandoc, can be used to render this file as an html file, which can with further effort be converted to other formats including PDF.

```bash
pandoc template.md -o template.html --standalone
```


