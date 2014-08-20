hello_world
===========

My first repository !!


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

always @ (*)
begin
case (count1)
8'b00000000 :
begin
in3 = 4'b0001;  // H
in2 = 4'b0010;  // E
in1 = 4'b0011;  // L
in0 = 4'b0011;  // L
end
8'b00000001 :
begin
in3 = 4'b0010;  // E
in2 = 4'b0011;  // L
in1 = 4'b0011;  // L
in0 = 4'b0100;  // O
end
8'b00000010 :
begin
in3 = 4'b0011;  // L
in2 = 4'b0011;  // L
in1 = 4'b0100;  // O
in0 = 4'b1000;  // blank
end
8'b00000011 :
begin
in3 = 4'b0011; // L
in2 = 4'b0100;  // O
in1 = 4'b1000;  // blank
in0 = 4'b0101;  // 3
end
8'b00000100 :
begin
in3 = 4'b0100;  // O
in2 = 4'b1000;  // blank
in1 = 4'b0101;  // 3
in0 = 4'b0100;  // 0
end
8'b00000101 :
begin
in3 = 4'b1000;  // blank
in2 = 4'b0101;  // 3
in1 = 4'b0100;   // 0
in0 = 4'b0110;  // r
end
8'b00000110 :
begin
in3 = 4'b0101; //3
in2 = 4'b0100; //o
in1 = 4'b0110; //r
in0 = 4'b0011; //l
end
8'b00000111 :
begin
in3= 4'b0100; //o
in2= 4'b0110; //r
in1= 4'b0011; //l
in0= 4'b0111; //d
end
default:
begin
in3 = 4'b1000; //blank
in2 = 4'b1000; //blank
in1 = 4'b1000; //blank
in0 = 4'b1000; //blank
end
endcase
end
// Now we are going to design an 18 bit counter because we have 4 displays and we need 2^4=16 bits for sequence and other 2 bits will act as enable bits.
 
localparam N = 18;
 
reg [N-1:0]count;  // the 18 bit counter that allows us to multiplex at 1000Hz

always @ (posedge clock or posedge reset )
begin
if (reset)
count <= 0;
else
count <= count +1;
end

reg [3:0] display;
reg [3:0] temp_en;

always @ (*)
begin
case(count[N-1:N-2])    // this section tells us about which display will be enabled and what input has to be displayed on the selected segment

2'b00 : 
    begin
    display = in3;
    temp_en = 4'b1110;  // this value in temp_en will enable the first display by providing active low
    end
    
2'b01: 
     begin 
     display = in2;
     temp_en = 4'b1101;
     end
    
   2'b10: 
    begin
    display = in1;
    temp_en = 4'b1011;
    end
     
   2'b11:  
    begin
    display = in0;
    temp_en = 4'b0111;
    end
  endcase
 end   // end of begin

assign en = temp_en;

// Now we will make a register temp_display to store the binary code for the data to be displayed

reg [6:0] temp_display;

// This section consists of the binary code of the data that we wish to display

always @(*)
begin

case (display)

4'b0001 : temp_display = 7'b1001000;  // This will display 'H'
4'b0010 : temp_display = 7'b0110000;  // to display 'E'
4'b0011 : temp_display = 7'b1110001; // to display 'L'
4'b0100 : temp_display = 7'b0000001; // to display 'O'

4'b0101 : temp_display = 7'b0000110; // to display 3
4'b0110 : temp_display = 7'b1111010; // to display 'r'
4'b0111 : temp_display = 7'b1000010; // to display 'd'
4'b1000 : temp_display = 7'b1111111; // to display 'blank'


endcase
end
 assign {a,b,c,d,e,f,g} = temp_display;  // concatenation operator - bit by bit assignment of values

endmodule

