`timescale 1ns / 1ns

module tb_top_vga_controller;

    // 测试信号定义
    reg vga_clk;
    reg sys_rst_n;
    reg button;
    reg [9:0] pix_x;
    reg [9:0] pix_y;
    wire [15:0] pix_data;
    wire [1:0] current_state;

    // 实例化被测试模块
    top_vga_controller u_top_vga_controller(
        .vga_clk(vga_clk),
        .sys_rst_n(sys_rst_n),
        .button(button),
        .pix_x(pix_x),
        .pix_y(pix_y),
        .pix_data(pix_data),
        .current_state(current_state)
    );

    // 时钟生成
    initial begin
        vga_clk = 0;
        forever #10 vga_clk = ~vga_clk;  // 假设时钟周期20ns
    end

    // 复位和测试激励
    initial begin
        sys_rst_n = 0;
        button = 0;
        pix_x = 0;
        pix_y = 0;
        #200 sys_rst_n = 1;  // 释放复位

        // 模拟按钮按下
        #500 button = 1;
        #20 button = 0;
        #1000 button = 1;
        #20 button = 0;
        #1000 button = 1;
        #20 button = 0;

        #5000 $finish;
    end

endmodule
