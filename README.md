## Verilog Modeling of Common Modules in Neuromorphic Hardware Design
#### 1. There is a register with programmable bit width BW. Try to truncate the register from LSB to the first non-zero bit. For example, if the value of the register is 8'b1010_1000, then the truncated result would be 8'b0000_1000.
```
    genvar i;
    generate
        for(i = 0; i < LTP_WND; i = i + 1) begin: ltp_latest_one_gen
            if(i == 0) begin
                assign ltp_tdiff_near[i] = pre_sr[i];
            end
            else begin
                assign ltp_tdiff_near[i] = (~(|ltp_tdiff_near[i-1:0])) & pre_sr[i];
            end
        end
    endgenerate
```
#### 2. There is a register with programmable bit width BW. Try to truncate the lower N bits of the register. For example, if the value of the regiter is 8'b1010_1000 and N is 6, then the truncated result would be 8'b0010_1000.
```
	  assign ltp_tdiff_mask = ({{(LTP_WND - 1){1'b0}}, 1'b1} << ltp_wnd) - {{(LTP_WND - 1){1'b0}}, 1'b1};
```
#### 3. Generate constructs usually used to generate specific logic according to instantiation parameters. There are loop generate constructs and conditional generate constructs.
```
    genvar i;
    generate
        for(i = 0; i < LTP_WND; i = i + 1) begin: ltp_latest_one_gen
            if(i == 0) begin
                assign ltp_tdiff_near[i] = pre_sr[i];
            end
            else begin
                assign ltp_tdiff_near[i] = (~(|ltp_tdiff_near[i-1:0])) & pre_sr[i];
            end
        end
    endgenerate
```
#### 4. Assume there is a register with programmable bit width. Try to group registers into groups of 3 bits and sum over all groups.
```
    integer s;
    always @(*) begin
        ltp_dw_sum_near     = {(DW_BW){1'b0}}; 
        ltp_dw_sum_al2al    = {(DW_BW){1'b0}};
        for(s = 0; s < LTP_TRC_BW; s = s + 1) begin: ltp_dw_sum_gen
            ltp_dw_sum_near     = ltp_dw_sum_near  + ltp_dw_near[s*LRN_INTNSTY +: LRN_INTNSTY];
            ltp_dw_sum_al2al    = ltp_dw_sum_al2al + ltp_dw_al2al[s*LRN_INTNSTY +: LRN_INTNSTY];
        end
    end
```
