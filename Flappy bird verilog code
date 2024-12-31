module vga_demo(CLOCK_50, SW, KEY, VGA_R, VGA_G, VGA_B,
				VGA_HS, VGA_VS, VGA_BLANK_N, VGA_SYNC_N, VGA_CLK, LEDR, HEX0, HEX1, HEX2);
	
	input CLOCK_50;	
	input [7:0] SW;
	input [3:0] KEY;
	output [6:0] HEX0,HEX1, HEX2;
	output [7:0] VGA_R;
	output [7:0] VGA_G;
	output [7:0] VGA_B;
	output VGA_HS;
	output VGA_VS;
	output [3:0] LEDR;
	output VGA_BLANK_N;
	output VGA_SYNC_N;
	output VGA_CLK;	
	wire [6:0] VGA_Y;

	reg [6:0] Yc;
	wire [7:0] VGA_X;

	wire plot;
	wire [2:0] VGA_COLOR;

	
	fsm u1(!KEY[1], VGA_X, VGA_Y, plot, VGA_COLOR, KEY[0], CLOCK_50, LEDR[3:0], HEX0, HEX1, HEX2);
	

    vga_adapter VGA (
			.resetn(KEY[0]),
			.clock(CLOCK_50),
			.colour(VGA_COLOR),
			.x(VGA_X),
			.y(VGA_Y),
			.plot(plot),
			.VGA_R(VGA_R),
			.VGA_G(VGA_G),
			.VGA_B(VGA_B),
			.VGA_HS(VGA_HS),
			.VGA_VS(VGA_VS),
			.VGA_BLANK_N(VGA_BLANK_N),
			.VGA_SYNC_N(VGA_SYNC_N),
			.VGA_CLK(VGA_CLK));
		defparam VGA.RESOLUTION = "160x120";
		defparam VGA.MONOCHROME = "FALSE";
		defparam VGA.BITS_PER_COLOUR_CHANNEL = 1;
		defparam VGA.BACKGROUND_IMAGE = "black.mif";
endmodule


module fsm(w, oX, oY, plot, color, Resetn, Clock, LEDR[3:0], HEX0, HEX1, HEX2);
   input w;
	output [6:0] HEX0, HEX1, HEX2;
   output reg [7:0] oX;
   output reg [6:0] oY;
   output reg plot;
   output reg [2:0] color;
	input Clock;
	input Resetn;
	//wire [2:0] birdC; sprite nonsense
	//wire [7:0] birdPIX;
	output reg [3:0]LEDR;
	
	wire doublesixty;
	wire sixtyfps;
	reg [6:0] inc = 7'd0;
	reg [6:0] fallInc = 7'd0;
   reg [6:0] Yc = 7'd0;
   reg [7:0] Xc = 8'd0;
   reg [6:0] Ys = 7'd0;
	reg [7:0] Xs = 8'd0;
	reg [5:0] score;
	reg [7:0] PIPE_WIDTH = 8'd12;
	reg [6:0] PIPE_GAP = 7'd32, PIPE1_STARTY = 7'd0, PIPE1_LENGTH = 7'd40;
	
	reg [7:0] PIPE1_STARTX = 8'd116;
	
   reg [7:0] BOX_STARTX = 8'd35;
	reg [6:0] BOX_STARTY = 7'd55;
	localparam BOX_HEIGHT = 7'd8, BOX_WIDTH = 8'd8;

	localparam PIXEL_HEIGHT = 7'd119, PIXEL_WIDTH = 8'd159;

    reg [1:0] nextState, curState;
    localparam Default = 2'b00, Fly = 2'b01, Fall = 2'b10;

	slowcounter u11(Clock, sixtyfps);


	//block for box flying
	always @ (posedge Clock) 
	begin
	if(curState == Default)
		begin
		
			BOX_STARTY <= 7'd55;
			BOX_STARTX <= 8'd35;
			
		end
	else if(curState == Fly)
	begin
	
			if(sixtyfps && ((BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX + BOX_WIDTH < PIPE1_STARTX) || 
				(BOX_STARTY > PIPE1_STARTY + PIPE1_LENGTH && BOX_STARTY + BOX_HEIGHT < PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP) ||
				(BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX > PIPE1_STARTX + PIPE_WIDTH)))
			
				BOX_STARTY <= BOX_STARTY - 7'd1	; //changing boxstarty itself with increment
				
		end
	else if(curState == Fall)
		begin
	
			if(sixtyfps && ((BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX + BOX_WIDTH < PIPE1_STARTX) || 
				(BOX_STARTY > PIPE1_STARTY + PIPE1_LENGTH && BOX_STARTY + BOX_HEIGHT < PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP) ||
				(BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX > PIPE1_STARTX + PIPE_WIDTH)))
		
				BOX_STARTY <= BOX_STARTY + 1;
					
		end
	end
	
	always @ (posedge Clock) 
	begin
	if(curState == Default)
		begin
		
			score <= 4'b0;
			LEDR[2:0] <= 3'b001;
			PIPE1_STARTX <= 8'd116;
	
		end
	else if(curState == Fly)
	begin
		LEDR[2:0] <= 3'b010;
	
			if(sixtyfps && ((BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX + BOX_WIDTH < PIPE1_STARTX) || 
				(BOX_STARTY > PIPE1_STARTY + PIPE1_LENGTH && BOX_STARTY + BOX_HEIGHT < PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP) ||
				(BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX > PIPE1_STARTX + PIPE_WIDTH)))
				begin
					PIPE1_STARTX <= PIPE1_STARTX - 1;
					if(BOX_STARTX == PIPE1_STARTX + PIPE_WIDTH)
						score <= score + 1;
				end
						
				
		end
	else if(curState == Fall)
		begin
		LEDR[2:0] <= 3'b100;

			if(sixtyfps && ((BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX + BOX_WIDTH < PIPE1_STARTX) || 
				(BOX_STARTY > PIPE1_STARTY + PIPE1_LENGTH && BOX_STARTY + BOX_HEIGHT < PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP) ||
				(BOX_STARTY + BOX_HEIGHT < PIXEL_HEIGHT && BOX_STARTX > PIPE1_STARTX + PIPE_WIDTH)))
				
				begin
					PIPE1_STARTX <= PIPE1_STARTX - 1;
					if(BOX_STARTX == PIPE1_STARTX + PIPE_WIDTH)
						score <= score + 1;
				end
					
			
		end
	end
	
	wire [3:0] ones = score%10;
	wire [3:0] tens = (score/10)%10;
	wire [3:0] hundreds = (score/100)%10;
	
	score_to_hex blockA(ones, HEX0);
	score_to_hex blockB(tens, HEX1);
	score_to_hex blockC(hundreds, HEX2);
 // handle outputs
	always @(*) //since all three states do the same thing here, this iteration we get rid of the cases
   
     
		 begin
            plot = 1'b1;
				
					begin
					if(Yc >= BOX_STARTY && Yc <= BOX_STARTY + BOX_HEIGHT &&
						Xc >= BOX_STARTX && Xc <= BOX_STARTX + BOX_WIDTH)
						
						color = 3'b100;  // Red for box
						
					else if ((Yc >= PIPE1_STARTY && Yc < PIPE1_STARTY + PIPE1_LENGTH && Xc > PIPE1_STARTX && Xc < PIPE1_STARTX + PIPE_WIDTH) || 
					(Yc > PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP && Yc <= PIXEL_HEIGHT && Xc > PIPE1_STARTX && Xc < PIPE1_STARTX + PIPE_WIDTH))
					
						color = 3'b010; // pipe color
						
					else if(/*((Xc == PIPE1_STARTX || Xc == PIPE1_STARTX + PIPE_WIDTH) && Yc < PIPE1_STARTY + PIPE1_LENGTH) ||*/ 
					(Yc == PIPE1_STARTY + PIPE1_LENGTH && Xc >= PIPE1_STARTX && Xc <= PIPE1_STARTX + PIPE_WIDTH) || 
					(Yc == PIPE1_STARTY + PIPE1_LENGTH + PIPE_GAP && Xc >= PIPE1_STARTX && Xc <= PIPE1_STARTX + PIPE_WIDTH))// || (Yc >=PIPE1_STARTY && Yc <= PIPE1_STARTY + PIPE1_LENGTH && Xc == PIPE1_STARTX + PIPE_WIDTH))
						
						color = 3'b000; // pipe outlines
						
					else
						color = 3'b011;  // Background color
					end
         end
    
   
// handle state change
	always @ (*)
	begin
      case(curState) 
		Default: 
			begin // REMOVING && sixtyfps this time
				if(w)
					nextState = Fly;
				else if(!w)
					nextState = Default;
			end
		
		Fly:
			if(w)
				nextState = Fly;
		
			else if(!w)
				nextState = Fall;
	
			
		Fall:
			if(w)
				nextState = Fly;
			else if(!w)
				nextState = Fall;
        endcase
	end
	

	always @(posedge Clock)
		begin
			if(!Resetn)
				curState <= Default;
			else
				curState <= nextState;
		end

	// counter to go through each pixel
 always @(posedge Clock)
   begin
		if(Xc <= PIXEL_WIDTH)
			Xc <= Xc + 1;
		else if(Xc > PIXEL_WIDTH && Yc <= PIXEL_HEIGHT)
		begin
			Xc <= 8'd0;
			Yc <= Yc + 1;
		end
		else if(Xc > PIXEL_WIDTH & Yc > PIXEL_HEIGHT)
		begin
			Xc <= 8'd0;
			Yc <= 7'd0;
      end
      // Update output coordinates
      oX <= Xc + Xs;
      oY <= Yc + Ys;
   end
		
endmodule 

module slowcounter(CLOCK_50, frame); // 45 fps now
	input CLOCK_50;
	reg [20:0] counter = 21'd0;
	output reg frame;
	always @ (posedge CLOCK_50)
		begin
			if(counter <= 21'd1111111)
				begin
					counter <= counter + 1;
					frame <= 1'b0;
				end
			else if(counter > 21'd1111111)
				begin
					counter <= 21'd0;
					frame <= 1'b1;
				end
		end
			
		
endmodule 

module score_to_hex(inp, hex);
	input [3:0] inp;
	output reg [6:0] hex;
	
	always @ (*)
		begin
			case(inp)
				4'h0: hex = 7'b1000000; //0
				4'h1: hex = 7'b1111001;
				4'h2: hex = 7'b0100100; //0
				4'h3: hex = 7'b0110000;
				4'h4: hex = 7'b0011001; //0
				4'h5: hex = 7'b0010010;
				4'h6: hex = 7'b0000010; //0
				4'h7: hex = 7'b1111000;
				4'h8: hex = 7'b0000000; //0
				4'h9: hex = 7'b0011000; //0
				default: hex = 7'b1111111;
			endcase
		end
endmodule
				
