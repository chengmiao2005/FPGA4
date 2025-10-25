`timescale 1ns / 1ns

module vga_end(
    input wire vga_clk,
    input wire sys_rst_n,
    input wire [9:0] pix_x,
    input wire [9:0] pix_y,
    output reg [15:0] pix_data
);

////
//\* Parameter and Internal Signal \//
////
//parameter define
parameter H_VALID = 10'd640,     //Maximum x value
          V_VALID = 10'd480;     //Maximum y value

parameter RED     = 16'hF800,    //RED
          ORANGE  = 16'hFC00,    //Orange
          YELLOW  = 16'hFFE0,    //Yellow
          GREEN   = 16'h07E0,    //Green
          CYAN    = 16'h07FF,    //Cyan
          BLUE    = 16'h001F,    //Blue
          PURPPLE = 16'hF81F,    //Purple
          BLACK   = 16'h0000,    //Black
          WHITE   = 16'hFFFF,    //White
          GRAY    = 16'hD69A;    //Grey

// Letter parameters
parameter LETTER_WIDTH = 80;      // Width of each letter
parameter LETTER_HEIGHT = 120;    // Height of each letter
parameter LETTER_SPACING = 20;    // Spacing between letters
parameter TEXT_COLOR = WHITE;     // Text color
parameter BACKGROUND_COLOR = GREEN; // Background color

// Calculate starting position to center the text (now 3 letters: E, N, D)
parameter TEXT_TOTAL_WIDTH = 3 * LETTER_WIDTH + 2 * LETTER_SPACING;
parameter TEXT_START_X = (H_VALID - TEXT_TOTAL_WIDTH) / 2;
parameter TEXT_START_Y = (V_VALID - LETTER_HEIGHT) / 2;

// Function to check if pixel is within a rectangle
function is_in_rect;
    input [9:0] x, y, rect_x, rect_y, rect_w, rect_h;
    begin
        is_in_rect = (x >= rect_x) && (x < rect_x + rect_w) && 
                     (y >= rect_y) && (y < rect_y + rect_h);
    end
endfunction

// Function to draw letter E
function draw_E;
    input [9:0] x, y, base_x, base_y;
    reg [9:0] rel_x, rel_y;
    begin
        rel_x = x - base_x;
        rel_y = y - base_y;
        
        // Draw E shape - three horizontal lines and one vertical line
        draw_E = ((rel_y < 10) ||                           // Top horizontal
                 (rel_y >= LETTER_HEIGHT - 10) ||          // Bottom horizontal
                 (rel_y >= LETTER_HEIGHT/2 - 5 && rel_y < LETTER_HEIGHT/2 + 5) || // Middle horizontal
                 (rel_x < 10));                            // Left vertical
    end
endfunction

// Function to draw letter N
function draw_N;
    input [9:0] x, y, base_x, base_y;
    reg [9:0] rel_x, rel_y;
    begin
        rel_x = x - base_x;
        rel_y = y - base_y;
        
        // Draw N shape - two vertical lines and one diagonal line
        draw_N = ((rel_x < 10) ||                           // Left vertical
                 (rel_x >= LETTER_WIDTH - 10) ||           // Right vertical
                 ((rel_x >= 10) && (rel_x < LETTER_WIDTH - 10) && 
                  (rel_y < (LETTER_HEIGHT)) && (rel_x - 10 == (rel_y * (LETTER_WIDTH - 20)) / LETTER_HEIGHT))); // Diagonal
    end
endfunction

// Function to draw letter D - With thinner left side
function draw_D;
    input [9:0] x, y, base_x, base_y;
    reg [9:0] rel_x, rel_y;
    reg [3:0] row_idx, col_idx;
    reg [7:0] letter_d [0:11];
    begin
        rel_x = x - base_x;
        rel_y = y - base_y;
        
        // Initialize the letter D pixel matrix with thinner left side
        // Modified to have thinner left side by reducing the number of 1's in the leftmost columns
        letter_d[0] = 8'b01111100; // Thinner left side
        letter_d[1] = 8'b01111110; // Thinner left side
        letter_d[2] = 8'b01100110; // Thinner left side
        letter_d[3] = 8'b01100110; // Thinner left side
        letter_d[4] = 8'b01100110; // Thinner left side
        letter_d[5] = 8'b01100110; // Thinner left side
        letter_d[6] = 8'b01100110; // Thinner left side
        letter_d[7] = 8'b01100110; // Thinner left side
        letter_d[8] = 8'b01100110; // Thinner left side
        letter_d[9] = 8'b01100110; // Thinner left side
        letter_d[10] = 8'b01111110; // Thinner left side
        letter_d[11] = 8'b01111100; // Thinner left side
        
        // Calculate which 10x10 block the current pixel is in
        row_idx = rel_y / 10;  // 12 rows (0-11)
        col_idx = rel_x / 10;  // 8 columns (0-7)
        
        // Check if within the letter bounds and if the pixel should be drawn
        if (row_idx < 12 && col_idx < 8) begin
            draw_D = letter_d[row_idx][7-col_idx];  // Note: we reverse the column index to match the bit order
        end else begin
            draw_D = 0;
        end
    end
endfunction

// Generate color based on pixel position
always @(*) begin
    if (!sys_rst_n) begin
        pix_data = BACKGROUND_COLOR;
    end else begin
        // Check if pixel is within any letter area
        if (is_in_rect(pix_x, pix_y, TEXT_START_X, TEXT_START_Y, TEXT_TOTAL_WIDTH, LETTER_HEIGHT)) begin
            // Letter E
            if (is_in_rect(pix_x, pix_y, TEXT_START_X, TEXT_START_Y, LETTER_WIDTH, LETTER_HEIGHT)) begin
                pix_data = draw_E(pix_x, pix_y, TEXT_START_X, TEXT_START_Y) ? TEXT_COLOR : BACKGROUND_COLOR;
            end
            // Letter N
            else if (is_in_rect(pix_x, pix_y, TEXT_START_X + LETTER_WIDTH + LETTER_SPACING, TEXT_START_Y, LETTER_WIDTH, LETTER_HEIGHT)) begin
                pix_data = draw_N(pix_x, pix_y, TEXT_START_X + LETTER_WIDTH + LETTER_SPACING, TEXT_START_Y) ? TEXT_COLOR : BACKGROUND_COLOR;
            end
            // Letter D
            else if (is_in_rect(pix_x, pix_y, TEXT_START_X + 2*(LETTER_WIDTH + LETTER_SPACING), TEXT_START_Y, LETTER_WIDTH, LETTER_HEIGHT)) begin
                pix_data = draw_D(pix_x, pix_y, TEXT_START_X + 2*(LETTER_WIDTH + LETTER_SPACING), TEXT_START_Y) ? TEXT_COLOR : BACKGROUND_COLOR;
            end
            else begin
                pix_data = BACKGROUND_COLOR;
            end
        end else begin
            pix_data = BACKGROUND_COLOR;
        end
    end
end

endmodule
