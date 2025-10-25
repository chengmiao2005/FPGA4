module vga_timing(
    input wire vga_clk,
    input wire sys_rst_n,
    output reg h_sync,
    output reg v_sync,
    output reg [9:0] pix_x,
    output reg [9:0] pix_y,
    output reg video_active
);

// 640x480@60时序参数
localparam H_DISPLAY = 640;
localparam H_FRONT_PORCH = 16;
localparam H_SYNC_PULSE = 96;
localparam H_BACK_PORCH = 48;
localparam H_TOTAL = H_DISPLAY + H_FRONT_PORCH + H_SYNC_PULSE + H_BACK_PORCH;

localparam V_DISPLAY = 480;
localparam V_FRONT_PORCH = 10;
localparam V_SYNC_PULSE = 2;
localparam V_BACK_PORCH = 33;
localparam V_TOTAL = V_DISPLAY + V_FRONT_PORCH + V_SYNC_PULSE + V_BACK_PORCH;

// 水平计数器和垂直计数器
reg [9:0] h_count;
reg [9:0] v_count;

always @(posedge vga_clk or negedge sys_rst_n) begin
    if (!sys_rst_n) begin
        h_count <= 0;
        v_count <= 0;
        h_sync <= 1;
        v_sync <= 1;
        video_active <= 0;
        pix_x <= 0;
        pix_y <= 0;
    end else begin
        // 水平计数器
        if (h_count < H_TOTAL - 1) begin
            h_count <= h_count + 1;
        end else begin
            h_count <= 0;
            // 垂直计数器
            if (v_count < V_TOTAL - 1) begin
                v_count <= v_count + 1;
            end else begin
                v_count <= 0;
            end
        end

        // 水平同步信号
        if (h_count >= (H_DISPLAY + H_FRONT_PORCH) && 
            h_count < (H_DISPLAY + H_FRONT_PORCH + H_SYNC_PULSE)) begin
            h_sync <= 0;
        end else begin
            h_sync <= 1;
        end

        // 垂直同步信号
        if (v_count >= (V_DISPLAY + V_FRONT_PORCH) && 
            v_count < (V_DISPLAY + V_FRONT_PORCH + V_SYNC_PULSE)) begin
            v_sync <= 0;
        end else begin
            v_sync <= 1;
        end

        // 视频有效区域
        if (h_count < H_DISPLAY && v_count < V_DISPLAY) begin
            video_active <= 1;
            pix_x <= h_count;
            pix_y <= v_count;
        end else begin
            video_active <= 0;
            pix_x <= 0;
            pix_y <= 0;
        end
    end
end

endmodule
