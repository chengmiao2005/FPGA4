`timescale 1ns / 1ns

module clk_divider(
    input wire clk_in,   // 50MHz输入
    output reg clk_out   // 25MHz输出
);

reg counter;

always @(posedge clk_in) begin
    counter <= ~counter;
    clk_out <= counter;
end

endmodule
