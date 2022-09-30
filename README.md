# Mixed Signal FSM Sequence Detector with CMOS Inverter
Mixed signal circuit implemented as a part of the Mixed Signal SoC Design Hackathon by IIT Bombay and Google

## Table of Contents
- [Abstract](#abstract)
- [Reference Circuit Diagram](#reference-circuit-diagram)
- [Reference Waveform](#reference-waveform)
- [Circuit Explanation](#circuit-explanation)
- [Truth Table](#truth-table)
- [Software Used](#software-used)
  * [eSim](#esim)
  * [NgSpice](#ngspice)
  * [Makerchip](#makerchip-1)
  * [Verilator](#verilator)
- [Circuit Schematic in eSim](#circuit-schematic-in-esim)
- [Verilog Code](#verilog-code)
- [Makerchip](#makerchip)
- [Makerchip Plot](#makerchip-plot)
- [Netlists](#netlists)
- [Output Waveforms](#output-waveforms)
- [Steps to run generate NgVeri Model](#steps-to-run-generate-ngveri-model)
- [Steps to run this project](#steps-to-run-this-project)
- [Author](#author)
- [Acknowledgements](#acknowledgements)
- [References](#references)

## Abstract

## Reference Circuit Diagram
![image](https://user-images.githubusercontent.com/60297156/193205827-b9802c16-607f-4f15-949e-d03e673e867a.png)


## Reference Waveform
![image](https://user-images.githubusercontent.com/60297156/193205925-73db6ac7-634f-4d49-a776-d668945b86ca.png)

![image](https://user-images.githubusercontent.com/60297156/193205970-39a95517-af7e-4f4a-afd8-f5b9d2152ea1.png)


## Circuit Explanation

## Truth Table

## Software Used
eSim

It is an Open Source EDA developed by FOSSEE, IIT Bombay. It is used for electronic circuit simulation. It is made by the combination of two software namely NgSpice and KiCAD. For more details refer:

https://esim.fossee.in/home

NgSpice

It is an Open Source Software for Spice Simulations. For more details refer:

http://ngspice.sourceforge.net/docs.html

Makerchip

It is an Online Web Browser IDE for Verilog/System-verilog/TL-Verilog Simulation. For more details refer:

https://www.makerchip.com/

Verilator

It is a tool which converts Verilog code to C++ objects. For more details refer:

https://www.veripool.org/verilator/

## Circuit Schematic In eSim

## Verilog Code
```verilog
module Sequence_Detector_MOORE_Verilog(sequence_in,clock,reset,detector_out);
input clock; // clock signal
input reset; // reset
input sequence_in; // input
output reg detector_out; // output of the sequence detector
parameter  Zero=3'b000,
           One=3'b001, 
           OneZero=3'b011, 
           OneZeroOne=3'b010,
           OneZeroOneOne=3'b110;
reg [2:0] current_state, next_state; 
  
// sequential memory of the Moore FSM
always @(posedge clock, posedge reset)
begin
 if(reset==1) 
 current_state <= Zero;// when reset=1, reset the state of the FSM to "Zero" State
 else
 current_state <= next_state;
end 
  
// combinational logic of the Moore FSM to determine next state 
always @(current_state,sequence_in)
begin
 case(current_state) 
 Zero:begin
  if(sequence_in==1)
   next_state = One;
  else
   next_state = Zero;
 end
 One:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = One;
 end
 OneZero:begin
  if(sequence_in==0)
   next_state = Zero;
  else
   next_state = OneZeroOne;
 end 
 OneZeroOne:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = OneZeroOneOne;
 end
 OneZeroOneOne:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = One;
 end
 default:next_state = Zero;
 endcase
end
 
// combinational logic to determine the output of the Moore FSM
always @(current_state)
begin 
 case(current_state) 
 Zero:   detector_out = 0;
 One:   detector_out = 0;
 OneZero:  detector_out = 0;
 OneZeroOne:  detector_out = 0;
 OneZeroOneOne:  detector_out = 1;
 default:  detector_out = 0;
 endcase
end 
endmodule
```

## Makerchip

```verilog

\TLV_version 1d: tl-x.org
\SV

//Your Verilog/System Verilog Code Starts Here:
module ashutosh_seqdet(sequence_in,clock,reset,detector_out);
input clock; // clock signal
input reset; // reset
input sequence_in; // input
output reg detector_out; // output of the sequence detector
parameter  Zero=3'b000,
           One=3'b001, 
           OneZero=3'b011, 
           OneZeroOne=3'b010,
           OneZeroOneOne=3'b110;
reg [2:0] current_state, next_state; 
  
// sequential memory of the Moore FSM
always @(posedge clock, posedge reset)
begin
 if(reset==1) 
 current_state <= Zero;// when reset=1, reset the state of the FSM to "Zero" State
 else
 current_state <= next_state;
end 
  
// combinational logic of the Moore FSM to determine next state 
always @(current_state,sequence_in)
begin
 case(current_state) 
 Zero:begin
  if(sequence_in==1)
   next_state = One;
  else
   next_state = Zero;
 end
 One:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = One;
 end
 OneZero:begin
  if(sequence_in==0)
   next_state = Zero;
  else
   next_state = OneZeroOne;
 end 
 OneZeroOne:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = OneZeroOneOne;
 end
 OneZeroOneOne:begin
  if(sequence_in==0)
   next_state = OneZero;
  else
   next_state = One;
 end
 default:next_state = Zero;
 endcase
end
 
// combinational logic to determine the output of the Moore FSM
always @(current_state)
begin 
 case(current_state) 
 Zero:   detector_out = 0;
 One:   detector_out = 0;
 OneZero:  detector_out = 0;
 OneZeroOne:  detector_out = 0;
 OneZeroOneOne:  detector_out = 1;
 default:  detector_out = 0;
 endcase
end 
endmodule


//Top Module Code Starts here:
	module top(input logic clk, input logic reset, input logic [31:0] cyc_cnt, output logic passed, output logic failed);
		logic  clock;//input
		logic  sequence_in;//input
		logic  detector_out;//output
//The $random() can be replaced if user wants to assign values
		ashutosh_seqdet ashutosh_seqdet(.clock(clock), .reset(reset), .sequence_in(sequence_in), .detector_out(detector_out));
	
\TLV
//Add \TLV here if desired                                     
\SV
endmodule




```

## Makerchip Plot

![image](https://user-images.githubusercontent.com/60297156/193257363-136ff777-37e1-411b-a892-dc6b60a56a81.png)

## Netlists

## Output Waveforms

## Steps to run generate NgVeri Model
1. Open eSim
2. Run NgVeri-Makerchip 
3. Add top level verilog file in Makerchip Tab
4. Click on NgVeri tab
5. Add dependency files
6. Click on Run Verilog to NgSpice Converter
7. Debug if any errors
8. Model created successfully


## Steps to run this project


## Author
Ashutosh Rao, BTech (Electronics and Communication), PES University, Bangalore, Karnataka, 560085

## Acknowledgements
1.	Kunal Ghosh (Co-Founder, VLSI System Design Pvt. Ltd.)
2.	FOSSEE, IIT Bombay	
3.	Steve Hoover (Founder, Redwood EDA)
4.	Sumanto Kar (eSim Team, FOSSEE, IIT Bombay)

## References
[1] 
  
[2] 
  
[3] 
