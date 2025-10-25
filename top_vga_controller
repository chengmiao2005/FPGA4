`timescale 1ns / 1ns

module top_vga_controller(
    input wire vga_clk,
    input wire sys_rst_n,      
    input wire button,         
    input wire [9:0] pix_x,
    input wire [9:0] pix_y,
    output reg [15:0] pix_data,
    output reg [1:0] current_state  
);

    localparam STATE_COLOR_BAR = 2'b00;
    localparam STATE_MUST      = 2'b01;
    localparam STATE_END       = 2'b10;

    reg [1:0] next_state;
    reg [2:0] reset_cnt;       
    reg button_prev;           
    wire button_posedge;


    wire [15:0] pix_data_color_bar;
    wire [15:0] pix_data_must;
    wire [15:0] pix_data_end;

    always @(posedge vga_clk or negedge sys_rst_n) begin
        if (!sys_rst_n)
            reset_cnt <= 3'd0;  
        else if (reset_cnt < 3'd7)
            reset_cnt <= reset_cnt + 1'b1;  
    end
    wire reset_release = (reset_cnt == 3'd7); 

    always @(posedge vga_clk or negedge sys_rst_n) begin
        if (!sys_rst_n)
            button_prev <= 1'b0;  
        else
            button_prev <= button;
    end

    assign button_posedge = reset_release ? (button & ~button_prev) : 1'b0;

    always @(posedge vga_clk or negedge sys_rst_n) begin
        if (!sys_rst_n)
            current_state <= STATE_COLOR_BAR; 
        else
            current_state <= next_state;
    end

    always @(*) begin
        if (!reset_release) begin  
            next_state = STATE_COLOR_BAR;
        end else begin
            case (current_state)
                STATE_COLOR_BAR: next_state = button_posedge ? STATE_MUST : STATE_COLOR_BAR;
                STATE_MUST:      next_state = button_posedge ? STATE_END : STATE_MUST;
                STATE_END:       next_state = button_posedge ? STATE_COLOR_BAR : STATE_END;
                default:         next_state = STATE_COLOR_BAR;
            endcase
        end
    end


    vga_color_bar color_bar_inst(
        .vga_clk(vga_clk),
        .sys_rst_n(sys_rst_n),
        .pix_x(pix_x),
        .pix_y(pix_y),
        .pix_data(pix_data_color_bar)
    );

    vga_must must_inst(
        .vga_clk(vga_clk),
        .sys_rst_n(sys_rst_n),
        .pix_x(pix_x),
        .pix_y(pix_y),
        .pix_data(pix_data_must)
    );

    vga_end end_inst(
        .vga_clk(vga_clk),
        .sys_rst_n(sys_rst_n),
        .pix_x(pix_x),
        .pix_y(pix_y),
        .pix_data(pix_data_end)
    );

    always @(posedge vga_clk or negedge sys_rst_n) begin
        if (!sys_rst_n)
            pix_data <= 16'h0000; 
        else begin
            case (current_state)
                STATE_COLOR_BAR: pix_data <= pix_data_color_bar;
                STATE_MUST:      pix_data <= pix_data_must;
                STATE_END:       pix_data <= pix_data_end;
                default:         pix_data <= pix_data_color_bar;
            endcase
        end
    end

endmodule
