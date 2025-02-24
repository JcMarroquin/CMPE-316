# HW 3 - COMPLETE

## Problem 1
Source code:
Module was properly updated for Verilog, by using $reg$ instead of $logic$
As well as updating some syntax.
```verilog
module DUT (u, v, a, b, clk);
   input a, b, clk;
   output reg [1:0] u, v; 

   // Declare internal registers
   reg [1:0] _u, _v;  

   always @(posedge clk) begin
      _u = 2'b00;  

      if (a == 1) begin
         _u = 2'b00;
         _v = 2'b10;
      end 
      else if (b == 1) begin
         _u = 2'b1x;  
         _v = 2'b01;
      end 
      else begin
         _v = 2'b01;  
      end
      u <= _u;
      v <= _v;
   end
endmodule
```

My test bench:

```verilog
`timescale 1ns/1ps

module problem1_tb;
    reg a, b, clk;
    wire [1:0] u, v;
    
    // Instantiate the DUT
    DUT dut_instance (.u(u), .v(v), .a(a), .b(b), .clk(clk));
    
    // Generate clock signal (100 MHz -> 10 ns period)
    always begin
        #5 clk = ~clk;
    end
    
    initial begin
        clk = 0;
        // Initialize inputs
        a = 0; b = 0;
        #10;
        
        // Cycle through all input combinations
        a = 0; b = 0; #10;
        a = 0; b = 1; #10;
        a = 1; b = 0; #10;
        a = 1; b = 1; #10;
        
        // End simulation
        $finish;
    end
    
    // Monitor outputs using $strobe
    always @(posedge clk) begin
        $strobe("Time=%0t | a=%b b=%b | _u=%b _v=%b | u=%b v=%b", $time, a, b, dut_instance._u, dut_instance._v, u, v);
    end
    
endmodule

```

Results:
```
Time=5000 | a=0 b=0 | _u=00 _v=01 | u=00 v=01
Time=15000 | a=0 b=0 | _u=00 _v=01 | u=00 v=01
Time=25000 | a=0 b=1 | _u=1x _v=01 | u=1x v=01
Time=35000 | a=1 b=0 | _u=00 _v=10 | u=00 v=10
Time=45000 | a=1 b=1 | _u=00 _v=10 | u=00 v=10
```

## Problem 2
Source code:
Module was properly updated for Verilog, by using $reg$ instead of $logic$
As well as updating some syntax.
```verilog
module DUT (u, v, a, b, clk);
   input a, b, clk;
   output reg [1:0] u, v; 

   // Declare internal registers
   reg [1:0] _u, _v;  

   always @(posedge clk) begin
      _u = 2'b00;  

      if (a == 1) begin
         _u = 2'b00;
         _v = 2'b10;
      end 
      else if (b == 1) begin
         _u = 2'b1x;  
         _v = 2'b01;
      end 
      else begin
         _v = 2'b01;  
      end
      u <= _u;
      v <= _v;
   end
endmodule
```

My test bench:
```verilog
`timescale 1ns/1ps

module testbench;
    reg a, b, clk;
    wire [1:0] u, v;
    
    // Instantiate the DUT
    DUT dut_instance (.u(u), .v(v), .a(a), .b(b), .clk(clk));
    
    // Generate clock signal (100 MHz -> 10 ns period)
    always begin
        #5 clk = ~clk;
    end
    
    initial begin
        clk = 0;
        // Initialize inputs
        a = 0; b = 0;
        $display("Time=%0t | Assigned a=%b b=%b", $time, a, b);
        #10;
        
        // Cycle through all input combinations
        a = 0; b = 0; $display("Time=%0t | Assigned a=%b b=%b", $time, a, b); #10;
        a = 0; b = 1; $display("Time=%0t | Assigned a=%b b=%b", $time, a, b); #10;
        a = 1; b = 0; $display("Time=%0t | Assigned a=%b b=%b", $time, a, b); #10;
        a = 1; b = 1; $display("Time=%0t | Assigned a=%b b=%b", $time, a, b); #10;
        
        // End simulation
        $finish;
    end
    
    // Monitor outputs using $strobe
    always @(posedge clk) begin
        $display("Time=%0t | Before Strobe | a=%b b=%b | u=%b v=%b", $time, a, b, u, v);
        $strobe("Time=%0t | Strobe Output | a=%b b=%b | u=%b v=%b", $time, a, b, u, v);
    end
    
endmodule
```

Results:
```
Time=0 | Assigned a=0 b=0
Time=5000 | Before Strobe | a=0 b=0 | u=xx v=xx
Time=5000 | Strobe Output | a=0 b=0 | u=00 v=01
Time=10000 | Assigned a=0 b=0
Time=15000 | Before Strobe | a=0 b=0 | u=00 v=01
Time=15000 | Strobe Output | a=0 b=0 | u=00 v=01
Time=20000 | Assigned a=0 b=1
Time=25000 | Before Strobe | a=0 b=1 | u=00 v=01
Time=25000 | Strobe Output | a=0 b=1 | u=1x v=01
Time=30000 | Assigned a=1 b=0
Time=35000 | Before Strobe | a=1 b=0 | u=1x v=01
Time=35000 | Strobe Output | a=1 b=0 | u=00 v=10
Time=40000 | Assigned a=1 b=1
Time=45000 | Before Strobe | a=1 b=1 | u=00 v=10
Time=45000 | Strobe Output | a=1 b=1 | u=00 v=10
```

## Appendix 

```bash
pandoc HW3.md -o HW3.html --standalone
```
