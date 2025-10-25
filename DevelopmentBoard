module DevelopmentBoard(
    input wire clk, // 50MHz
    input wire reset, B2, B3, B4, B5,
    // reset is "a"
    // B2 is "s"
    // B3 is "d"
    // B4 is "f"
    // B5 is "g"
    output wire h_sync, v_sync,
    output wire [15:0] rgb,
    output wire led1,
    output wire led2,
    output wire led3,
    output wire led4,
    output wire led5
);

// 内部信号定义
wire vga_clk;
wire [9:0] pix_x;
wire [9:0] pix_y;
wire [15:0] pix_data;
wire video_active;
wire [1:0] current_state;

// 调试寄存器
reg [1:0] prev_state;
reg B2_dly, reset_dly;
reg [31:0] counter;

// 时钟分频
clk_divider clk_div_inst(
    .clk_in(clk),
    .clk_out(vga_clk)
);

// VGA时序控制器
vga_timing vga_timing_inst(
    .vga_clk(vga_clk),
    .sys_rst_n(reset),  // 修改：直接连接，不移除取反
    .h_sync(h_sync),
    .v_sync(v_sync),
    .pix_x(pix_x),
    .pix_y(pix_y),
    .video_active(video_active)
);

// 顶层VGA状态机控制器
top_vga_controller top_vga_inst(
    .vga_clk(vga_clk),
    .sys_rst_n(reset),  // 修改：直接连接，不移除取反
    .button(B2),
    .pix_x(pix_x),
    .pix_y(pix_y),
    .pix_data(pix_data),
    .current_state(current_state)
);

// RGB输出
assign rgb = video_active ? pix_data : 16'h0000;

// LED显示当前状态
assign led1 = (current_state == 2'b00);//gai
assign led2 = (current_state == 2'b01);
assign led3 = (current_state == 2'b10);
assign led4 = 1'b0;
assign led5 = 1'b0;

// ==================== 调试逻辑 ====================

initial begin
    $display("==========================================");
    $display("VGA State Machine Simulator Started");
    $display("Control Keys: 'a'=RESET, 's'=Switch State");
    $display("==========================================");
end

// 状态变化监控
always @(posedge vga_clk) begin
    if (current_state != prev_state) begin
        case (current_state)
            2'b00: $display("[$time] === STATE CHANGED to COLOR_BAR ===");
            2'b01: $display("[$time] === STATE CHANGED to MUST ===");
            2'b10: $display("[$time] === STATE CHANGED to END ===");
        endcase
        prev_state <= current_state;
    end
end

// 按钮监测
always @(posedge vga_clk) begin
    if (B2 && !B2_dly) 
        $display("[$time] >>> State Switch Button PRESSED");
    
    if (reset && !reset_dly) 
        $display("[$time] >>> RESET Button PRESSED");
    
    B2_dly <= B2;
    reset_dly <= reset;
end

// 定期状态报告
always @(posedge vga_clk) begin
    counter <= counter + 1;
    if (counter == 32'd12500000) begin // 每0.5秒
        counter <= 0;
        case (current_state)
            2'b00: $display("[$time] Status: COLOR_BAR");
            2'b01: $display("[$time] Status: MUST");
            2'b10: $display("[$time] Status: END");
        endcase
    end
end

endmodule
