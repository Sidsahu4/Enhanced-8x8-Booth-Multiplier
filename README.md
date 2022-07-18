# Enhanced-8x8-Booth-Multiplier
Enhanced the combinational circuit of conventional Booth multiplier by replacing some components of various blocks covered under it.
Low cost and high performance 8x8 Booth Multiplier taking integers from -128 to 127.
// Code your design here
// Code your design here
module B2C1(x,y8);
  input [7:0]x;
  wire [7:0]yx;
  output [8:0]y8;
  assign yx= ~x+1;
  assign y8={yx[7],yx[7:0]};
endmodule

module mux4x1(I0,I1,I2,I3,S0,S1,out);
  inout [8:0]I0,I1,I2,I3;
  input S0,S1;
  output reg[9:0]out;

    always @ (I0 or I1 or I2 or I3 or S0 or S1)
    begin
      case ({S1,S0})
        2'b00: out<={I0[8],I0};
        2'b01: out<={I1[8],I1};
        2'b10: out<={I2[8],I2};
        2'b11: out<={I3[8],I3};
	endcase
    end
endmodule


module boolean(Y0,Y1,Y2,P,Q,R,S);
  input Y0,Y1,Y2;
  output reg[8:0]P,Q,R,S;
  always@(Y0 or Y1 or Y2)
    begin
      P={9{~((Y0~^Y1)|Y2)}};
      Q={9{~((Y0~&Y1)|Y2)}};
      R={9{((Y0~|Y1)&Y2)}};
      S={9{((Y0^Y1)&Y2)}};
      
    end
endmodule


//2X=A,-X=B,-2X=C
module mainckt(Y0,Y1,Y2,P,Q,R,S,X,A,B,C,PP,l);
  input Y0,Y1,Y2;
  input [7:0]X;
  output reg[8:0]A,C;
  inout [8:0]B,P,Q,R,S,l;
  output reg[8:0]PP;
  boolean b1(Y0,Y1,Y2,P,Q,R,S);
  B2C1 b2(X,B);
  assign l={X[7],X};
  always@(l or A or B or C)
    begin
    
    A=l<<<1;
    C=B<<<1;
    end  
  
  always@(*)
    begin
      
      PP= ((P&l)|(Q&A)|(R&C)|(S&B));
    end
endmodule

module CLA #(parameter N=2) (Ax,Bx,Cin,S,Cx,Cxx);
  //parameter N;
  input [N-1:0]Ax,Bx;
  input Cin;
  output reg[N:0]Cxx;
  output reg[N-1:0]S;
  reg [N-1:0]Px;
  reg [N-1:0]G;
  output reg Cx;
  integer i;
  always@(*)
    begin
      
     //integer i;
      for(i=0;i<(N+1);i=i+1)
        begin 
          Cxx[0]=Cin;
          Px[i]=Ax[i]^Bx[i];
          G[i]=Ax[i]&Bx[i];
          Cxx[i+1]=G[i]+(Px[i]&Cxx[i]);
          S[i]=Px[i]^Cxx[i];
        end
       Cx=Cxx[N]; 
        end
      // Cx=[N-1]C;
      endmodule
 
module mux2x1 #(parameter N=1) (I0,I1,S0,out);
 
  input [N:0]I0;
  input [N:0]I1;
  input S0;
  output reg[N:0]out;
  //output reg[N-1:0]S;
  //output reg c;
always @ (I0 or I1 or S0 )
begin
case (S0)
0:out<=I0;
1:out<=I1;
endcase
// S<=out[N-1:0];
// c<=out[N];
end
endmodule
 
module mainckt1(.A0(A0),.B0(B0),.Cin0(Cin0),.S0(S0),.C0(C0),.A1(A1),.B1(B1),.Cin1(Cin1),.S1(S1),.C1(C1),.A2(A2),.B2(B2),.Cin2(Cin2),.S2(S2),.C2(C2),.out0(out0),.c0(c0),.A3(A3),.B3(B3),.Cin3(Cin3),.S3(S3),.C3(C3),.A4(A4),.B4(B4),.Cin4(Cin4),.S4(S4),.C4(C4),.out1(out1),.cout(cout),.Sout(Sout),.Cout(Cout),.c1(c1),.Cx0(Cx0),.Cx1(Cx1),.Cx2(Cx2),.Cx3(Cx3),.Cx4(Cx4));
  input [1:0]A0,B0;
  output reg[2:0]Cx0;
  output reg[3:0]Cx1,Cx2;
  output reg[4:0]Cx3,Cx4;
  input [2:0]A1,B1,A2,B2;
  input [3:0]A3,B3,A4,B4;
  input Cin0,Cin1,Cin2,Cin3,Cin4;
  //parameter N0=2,N1=3,N2=3,N3=4,N4=4;
  output reg[8:0]Sout;
  output reg Cout;
  inout [1:0]S0;
  inout [2:0]S1,S2;
  inout [3:0]S3,S4;
  
  inout cout;
  inout [3:0]out0;
  inout [4:0]out1;
  inout C0,C1,C2,C3,C4;
  //output S8;
  inout c0;
  //output S10;
  inout c1;
 
 
  defparam a0.N=2;
  defparam a1.N=3;
  defparam a2.N=3;
  defparam b0.N=3;
  defparam a3.N=4;
  defparam a4.N=4;
  defparam b1.N=4;
  CLA a0 (A0, B0, Cin0, S0, C0, Cx0);
  //defparam CLA_a0.N0=3;
  CLA a1 (A1,B1,Cin1,S1,C1, Cx1);
// defparam CLA_a1.N1=3;
  CLA a2 (A2,B2,Cin2,S2,C2, Cx2);
  //defparam CLA_a2.N2=3;
  mux2x1 b0({C1,S1},{C2,S2},C0,out0);
  //defparam mux2x1_b0.N1=3;
  //assign S8<=out0[N1-1:0];
  parameter N=4;
  assign c0=out0[N-1];
  CLA a3 (A3,B3,Cin3,S3,C3, Cx3);
  //defparam CLA_a3.N3=4;
  CLA a4 (A4,B4,Cin4,S4,C4, Cx4);
  //defparam CLA_a4.N4=4;
  mux2x1 b1({C3,S3},{C4,S4},c0,out1);
  //defparam mux2x1_b1.N3=4;
  //assign S10<=out1[N3-1:0];
  parameter N1=5;
  assign c1=out1[N1-1];
  assign Sout={out1[N1-2:0],out0[N-2:0],S0};
  assign Cout=c1;
endmodule

module mainckt2(.A0(A0),.B0(B0),.Cin0(Cin0),.S0(S0),.C0(C0),.A1(A1),.B1(B1),.Cin1(Cin1),.S1(S1),.C1(C1),.A2(A2),.B2(B2),.Cin2(Cin2),.S2(S2),.C2(C2),.out0(out0),.c0(c0),.A3(A3),.B3(B3),.Cin3(Cin3),.S3(S3),.C3(C3),.A4(A4),.B4(B4),.Cin4(Cin4),.S4(S4),.C4(C4),.out1(out1),.cout(cout),.Sout(Sout),.Cout(Cout),.c1(c1),.Cx0(Cx0),.Cx1(Cx1),.Cx2(Cx2),.Cx3(Cx3),.Cx4(Cx4));
  input [2:0]A0,B0;
  output reg[3:0]Cx0;
  output reg[3:0]Cx1,Cx2;
  output reg[4:0]Cx3,Cx4;
  input [2:0]A1,B1,A2,B2;
  input [3:0]A3,B3,A4,B4;
  input Cin0,Cin1,Cin2,Cin3,Cin4;
  //parameter N0=2,N1=3,N2=3,N3=4,N4=4;
  output reg[9:0]Sout;
  output reg Cout;
  inout [2:0]S0;
  inout [2:0]S1,S2;
  inout [3:0]S3,S4;
  
  inout cout;
  inout [3:0]out0;
  inout [4:0]out1;
  inout C0,C1,C2,C3,C4;
  //output S8;
  inout c0;
  //output S10;
  inout c1;
 
 
  defparam a0.N=3;
  defparam a1.N=3;
  defparam a2.N=3;
  defparam b0.N=3;
  defparam a3.N=4;
  defparam a4.N=4;
  defparam b1.N=4;
  CLA a0 (A0, B0, Cin0, S0, C0, Cx0);
  //defparam CLA_a0.N0=3;
  CLA a1 (A1,B1,Cin1,S1,C1, Cx1);
 // defparam CLA_a1.N1=3;
  CLA a2 (A2,B2,Cin2,S2,C2, Cx2);
  //defparam CLA_a2.N2=3;
  mux2x1 b0({C1,S1},{C2,S2},C0,out0);
  //defparam mux2x1_b0.N1=3;
  //assign S8<=out0[N1-1:0];
  parameter N=4;
  assign c0=out0[N-1];
  CLA a3 (A3,B3,Cin3,S3,C3, Cx3);
  //defparam CLA_a3.N3=4;
  CLA a4 (A4,B4,Cin4,S4,C4, Cx4);
  //defparam CLA_a4.N4=4;
  mux2x1 b1({C3,S3},{C4,S4},c0,out1);
  //defparam mux2x1_b1.N3=4;
  //assign S10<=out1[N3-1:0];
  parameter N1=5;
  assign c1=out1[N1-1];
  assign Sout={out1[N1-2:0],out0[N-2:0],S0};
  assign Cout=c1;
endmodule

module mainckt3(.A0(A0),.B0(B0),.Cin0(Cin0),.S0(S0),.C0(C0),.A1(A1),.B1(B1),.Cin1(Cin1),.S1(S1),.C1(C1),.A2(A2),.B2(B2),.Cin2(Cin2),.S2(S2),.C2(C2),.out0(out0),.c0(c0),.A3(A3),.B3(B3),.Cin3(Cin3),.S3(S3),.C3(C3),.A4(A4),.B4(B4),.Cin4(Cin4),.S4(S4),.C4(C4),.out1(out1),.cout(cout),.Sout(Sout),.Cout(Cout),.c1(c1),.Cx0(Cx0),.Cx1(Cx1),.Cx2(Cx2),.Cx3(Cx3),.Cx4(Cx4));
  input [2:0]A0,B0;
  output reg[3:0]Cx0;
  output reg[4:0]Cx1,Cx2;
  output reg[4:0]Cx3,Cx4;
  input [3:0]A1,B1,A2,B2;
  input [3:0]A3,B3,A4,B4;
  input Cin0,Cin1,Cin2,Cin3,Cin4;
  //parameter N0=2,N1=3,N2=3,N3=4,N4=4;
  output reg[10:0]Sout;
  output reg Cout;
  inout [2:0]S0;
  inout [3:0]S1,S2;
  inout [3:0]S3,S4;
  
  inout cout;
  inout [4:0]out0;
  inout [4:0]out1;
  inout C0,C1,C2,C3,C4;
  //output S8;
  inout c0;
  //output S10;
  inout c1;
 
 
  defparam a0.N=3;
  defparam a1.N=4;
  defparam a2.N=4;
  defparam b0.N=4;
  defparam a3.N=4;
  defparam a4.N=4;
  defparam b1.N=4;
  CLA a0 (A0, B0, Cin0, S0, C0, Cx0);
  //defparam CLA_a0.N0=3;
  CLA a1 (A1,B1,Cin1,S1,C1, Cx1);
 // defparam CLA_a1.N1=3;
  CLA a2 (A2,B2,Cin2,S2,C2, Cx2);
  //defparam CLA_a2.N2=3;
  mux2x1 b0({C1,S1},{C2,S2},C0,out0);
  //defparam mux2x1_b0.N1=3;
  //assign S8<=out0[N1-1:0];
  parameter N=5;
  assign c0=out0[N-1];
  CLA a3 (A3,B3,Cin3,S3,C3, Cx3);
  //defparam CLA_a3.N3=4;
  CLA a4 (A4,B4,Cin4,S4,C4, Cx4);
  //defparam CLA_a4.N4=4;
  mux2x1 b1({C3,S3},{C4,S4},c0,out1);
  //defparam mux2x1_b1.N3=4;
  //assign S10<=out1[N3-1:0];
  parameter N1=5;
  assign c1=out1[N1-1];
  assign Sout={out1[N1-2:0],out0[N-2:0],S0};
  assign Cout=c1;
endmodule


 
//2X=a,-X=b,-2X=c,0X=d
module mainCKT(x,a,b,c,d,y,PP1,PP2,PP3,PP4,PP5,PP6,PP11,PP22,PP33,PP44,Cin0,S01,C01,Cin1,S11,C11,Cin2,S21,C21,out01,c01,Cin3,S31,C31,Cin4,S41,C41,out11,cout1,Cout1,c11,Cx01,Cx11,Cx21,Cx31,Cx41,Sout1,Sout2,Sout3,P1,Q1,R1,S1,P2,Q2,R2,S2,P3,Q3,R3,S3,p,l);
  input [7:0]x,y;
  input [8:0]d;
  input Cin0,Cin1,Cin2,Cin3,Cin4;
  inout [9:0]PP1;
  inout [8:0]PP2,PP3,PP4;
  inout [9:0]PP11,PP22;
  inout [8:0]PP33,PP44;
  inout [10:0]PP5,PP6;
  inout [8:0]Sout1;
  inout [9:0]Sout2;
  inout [10:0]Sout3;
  inout Cout1,Cout2,Cout3;
  inout C01,C11,C21,C31,C41;
  inout C02,C12,C22,C32,C42;
  inout C03,C13,C23,C33,C43;
  inout [1:0]S01;
  inout [2:0]S11,S21;
  inout [3:0]S31,S41;
  inout [2:0]S02;
  inout [2:0]S12,S22;
  inout [3:0]S32,S42;
  inout [2:0]S03;
  inout [3:0]S13,S23;
  inout [3:0]S33,S43;
  inout [3:0]out01;
  inout [4:0]out11;
  inout [3:0]out02;
  inout [4:0]out12;
  inout [4:0]out03;
  inout [4:0]out13;
  output [8:0]l;
  inout cout1,cout2,cout3;
  inout c01,c11,c02,c12,c03,c13;
  output [2:0]Cx01;
  output [3:0]Cx11,Cx21;
  output [4:0]Cx31,Cx41;
  output [3:0]Cx02;
  output [3:0]Cx12,Cx22;
  output [4:0]Cx32,Cx42;
  output [3:0]Cx03;
  output [4:0]Cx13,Cx23;
  output [4:0]Cx33,Cx43;
  output [8:0]a,c;
  output [8:0]b;
  output [8:0]P1,Q1,R1,S1;
  output [8:0]P2,Q2,R2,S2;
  output [8:0]P3,Q3,R3,S3;
  output [14:0]p;
  B2C1 a1(x,b);
  assign l={x[7],x};
  assign a=l<<<1;
  assign c=b<<<1;
  mux4x1 a2(d,l,c,b,y[0],y[1],PP1);
  assign PP11={{2{PP1[9]}},PP1[9:2]};
  assign p[1:0]= {PP1[1],PP1[0]};
  mainckt b1(y[1],y[2],y[3],P1,Q1,R1,S1,x,a,b,c,PP2,l);
  mainckt b2(y[3],y[4],y[5],P2,Q2,R2,S2,x,a,b,c,PP3,l);
  mainckt b3(y[5],y[6],y[7],P3,Q3,R3,S3,x,a,b,c,PP4,l);
  assign PP22={{1{PP2[8]}},PP2};
  assign PP33={{2{PP3[8]}},PP3[8:2]};
  assign PP44=PP4[8:0];
  
  mainckt1 d1(PP44[1:0],PP33[1:0],Cin0,S01,C01,PP44[4:2],PP33[4:2],Cin1,S11,C11,PP44[4:2],PP33[4:2],Cin2,S21,C21,out01,c01,PP44[8:5],PP33[8:5],Cin3,S31,C31,PP44[8:5],PP33[8:5],Cin4,S41,C41,out11,cout1,Sout1,Cout1,c11,Cx01,Cx11,Cx21,Cx31,Cx41);
  
  mainckt2 d2(PP22[2:0],PP11[2:0],Cin0,S02,C02,PP22[5:3],PP11[5:3],Cin1,S12,C12,PP22[5:3],PP11[5:3],Cin2,S22,C22,out02,c02,PP22[9:6],PP11[9:6],Cin3,S32,C32,PP22[9:6],PP11[9:6],Cin4,S42,C42,out12,cout2,Sout2,Cout2,c12,Cx02,Cx12,Cx22,Cx32,Cx42);
  
  assign p[3:2]=Sout2[1:0];
  assign PP5={Sout1,PP3[1:0]};
  assign PP6={{3{Sout2[9]}},Sout2[9:2]};
  
  mainckt3 d3(PP5[2:0],PP6[2:0],Cin0,S03,C03,PP5[6:3],PP6[6:3],Cin1,S13,C13,PP5[6:3],PP6[6:3],Cin2,S23,C23,out03,c03,PP5[10:7],PP6[10:7],Cin3,S33,C33,PP5[10:7],PP6[10:7],Cin4,S43,C43,out13,cout3,Sout3,Cout3,c13,Cx03,Cx13,Cx23,Cx33,Cx43);
  
 assign p[14:4]=Sout3;
  
endmodule
