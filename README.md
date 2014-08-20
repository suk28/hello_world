hello_world
===========

My first repository


module ssevenseg (
input clock, 
input reset, 
output a,
output b,
output c,
output d,
output e,
output f,
output g,
output [3:0] en
); 

reg [3:0] in0, in1, in2, in3;  // registers to hold the LED value i.e data to be displayed
reg [28:0] tick_tock;  // to count for every 1s i.e holds count of 50 M
wire click; 
// Now we will design a counter that will count until 50 m so that we can get our 1 sec time lapse

always @(posedge clock or posedge reset)
begin
if(reset)
tick_tock <= 0;
else if ( tick_tock==50000000)
tick_tock <= 0;
else
tick_tock <= tick_tock+1;
end

assign click = (( tick_tock==50000000)?1’b1:1’b0);  // click every second
reg [3:0] count1;  // to hold the count upto 9
