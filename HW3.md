# HW 3

## Problem 1

My source code:

Module was properly updated for Verilog, by using $reg$ instead of 'logic'
```verilog
module DUT (u, v, a, b, clk);
   input a, b, clk;
   output reg [1:0] u, v;  // Use 'reg' since we assign in always block

   reg [1:0] _u, _v;  // Declare persistent registers

   always @(posedge clk) begin
      _u = 2'b00;  // Explicit initialization

      if (a == 1) begin
         _u = 2'b00;
         _v = 2'b10;
      end 
      else if (b == 1) begin
         _u = 2'b11;  // Avoid 'x' values for predictable output
         _v = 2'b01;
      end 
      else begin
         _v = 2'b00;  // Explicitly set v in the default case
      end

      u <= _u;
      v <= _v;
   end
endmodule


```

My test bench:

```verilog
//Filename: p1_tb.sv
module....
...
alawys_ff @ ...begin
...
end

endmodule
```

Results:
```
time: 10 a: 10 b: 10...
time: 20 a: 11 b: 10...
time: 30 a: 11 b: 10...
```

We expect $\sum homework = 100$

Screenshot:

![screenshot of](media/placeholder.png)

Schematic (RTL View):
    
<img src=media/placeholder.svg width=400px>


## Appendix 


Note that on many linux systems a document converter, pandoc, can be used to render this file as an html file, which can with further effort be converted to other formats including PDF.

```bash
pandoc template.md -o template.html --standalone
```


