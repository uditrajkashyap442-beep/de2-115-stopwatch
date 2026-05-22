# Digital Systems Design Project: Programmable Stopwatch/Timer

**Name:** Udit Raj Kashyap
**Registration Number:** 24BEC0668

## 1. Problem Identification

Digital stopwatches are essential in timing activities for sports, labs, and industrial processes. The challenge is to design and implement a programmable stopwatch/timer that displays minutes and seconds (MM:SS) and responds to start, stop, reset, and pause commands using an FPGA platform.

## 2. System Design

The system architecture uses push-buttons for user input (start, stop, reset, pause), an FPGA for digital control, clock divider logic, BCD conversion for MM:SS elaboration, and 7-segment displays for visualization. The circuit diagram includes signal connections for push buttons, display drivers, clock input, and FPGA GPIO mapping.

### Main Logic
* **Clock Divider:** Reduces 50 MHz clock to 1 Hz for second-by-second counting.
* **Counters:** Minutes (0-99) and seconds (0-59) counters update every second pulse when the stopwatch is running.
* **State Machine:** Logic processes button presses to control running, pausing, stopping, and resetting.
* **BCD Conversion:** Minute and second values are split into BCD digits for easy display.
* **Output:**
  * **7-Segment Displays:** Four onboard 7-segment displays show MM:SS, each digit driven through BCD to 7-segment decoder modules.
  * **Pin Mapping:** The pin assignment was imported from the provided CSV file for the DE2-115 FPGA BOARD.

## 3. Component Selection

For this MM:SS stopwatch project, components were chosen for optimal functionality and cost efficiency:

* **FPGA Board (Altera DE2-115, EP4CE115F29C7):**
  * **Functionality:** Provides 114,480 logic elements, integrated memory, and versatile I/O, enabling complex timer control, precise clock division, and multi-digit display management.
  * **Cost:** Cyclone IV devices offer a low price point relative to their feature set, making them ideal for academic prototyping.
  * **Flexibility:** Built-in support for push buttons, LEDs, and extensive GPIO allows direct hardware interaction.

* **Push Buttons & Switches:**
  * **Functionality:** Provide tactile user input for start, stop, pause, and reset operations.
  * **Cost:** Standard push buttons come pre-mounted on the DE2-115 board.

* **7-Segment Displays:**
  * **Functionality:** Multi-digit capability enables MM:SS visualization. BCD-to-7-segment decoding logic is implemented efficiently in FPGA fabric.
  * **Cost:** Utilizing the onboard displays eliminates the need for external modules.

* **Clock Source/Crystal Oscillator:**
  * **Functionality:** The DE2-115 features a reliable onboard oscillator delivering a fixed 50 MHz frequency, subdivided in Verilog logic to create accurate 1-second pulses.
  * **Cost:** Integrated into the board.

* **FPGA Utility Components:**
  * **Memory Blocks:** DE2-115 provides ample embedded RAM.
  * **Voltage Regulation and Interface ICs:** Pre-integrated on board for stable supply.

## 4. Implementation

The following Verilog code implements the core logic for the stopwatch:

```verilog
module stopwatch(
    input clk,
    input reset, // KEY0 (active-low)
    input start, // KEY1 (active-low)
    input pause, // KEY2 (active-low)
    input stop,  // KEY3 (active-low)
    output reg [5:0] sec,
    output reg [5:0] min,
    output [6:0] HEX0, HEX1, HEX2, HEX3
);

    reg running;
    reg [25:0] clock_divider;

    // Invert pushbutton signals for active-low logic
    wire rst_n = ~reset;
    wire start_n = ~start;
    wire pause_n = ~pause;
    wire stop_n = ~stop;

    // split into BCD digits for HEX display
    wire [3:0] sec_units = sec % 10;
    wire [3:0] sec_tens = sec / 10;
    wire [3:0] min_units = min % 10;
    wire [3:0] min_tens = min / 10;

    always @(posedge clk or posedge rst_n) begin
        if (rst_n) begin
            running <= 0;
            sec <= 0;
            min <= 0;
            clock_divider <= 0;
        end else begin
            // Control: active on button press
            if (stop_n) begin
                running <= 0;
                sec <= 0;
                min <= 0;
            end else if (start_n) begin
                running <= 1;
            end else if (pause_n) begin
                running <= 0;
            end

            // Timing
            if (clock_divider < 26'd49_999_999) begin
                clock_divider <= clock_divider + 1;
            end else begin
                clock_divider <= 0;
                if (running) begin
                    if (sec == 59) begin
                        sec <= 0;
                        if (min == 59)
                            min <= 0;
                        else
                            min <= min + 1;
                    end else begin
                        sec <= sec + 1;
                    end
                end
            end
        end
    end

    // 7-segment display decoders
    bcd_7seg display_sec_units(.bcd(sec_units), .seg(HEX0));
    bcd_7seg display_sec_tens (.bcd(sec_tens),  .seg(HEX1));
    bcd_7seg display_min_units(.bcd(min_units), .seg(HEX2));
    bcd_7seg display_min_tens (.bcd(min_tens),  .seg(HEX3));

endmodule

module bcd_7seg(
    input [3:0] bcd,
    output reg [6:0] seg
);
    always @(*) begin
        case(bcd)
            4'd0: seg = 7'b1000000;
            4'd1: seg = 7'b1111001;
            4'd2: seg = 7'b0100100;
            4'd3: seg = 7'b0110000;
            4'd4: seg = 7'b0011001;
            4'd5: seg = 7'b0010010;
            4'd6: seg = 7'b0000010;
            4'd7: seg = 7'b1111000;
            4'd8: seg = 7'b0000000;
            4'd9: seg = 7'b0010000;
            default: seg = 7'b1111111;
        endcase
    end
endmodule
```

## 5. Testing and Verification

* The code was compiled with 0 errors.
* The stopwatch FPGA implementation was manually tested.
* The assigned reset, stop, pause, and start buttons were verified along with the clock functionality on the 7-segment displays.
* The system meets the provided specifications.

## 6. Analysis and Optimization

* **Efficiency:** The Verilog design is highly efficient, utilizing minimal FPGA resources. A single clock divider and modular code structure (counters, state machine, display decoder) keeps resource and power usage low.
* **Reliability:** The clock divider and all counters are synchronous with the global clock, preventing glitches and ensuring error-free display updates. Proper timing closure was achieved, ensuring reliable operation at the 50 MHz target clock speed.

## 7. Potential Areas for Improvement

* **Debounce Logic:** Adding debounce logic to clean up noisy button presses will further improve reliability.
* **Advanced Features:** Future versions could support lap timing, a countdown mode, or an expanded display (hours:minutes:seconds).
* **Optimization:** Resource usage could be reduced by sharing display driving logic or implementing lower-power clocking strategies.
* **Scalability:** The modular and area-efficient design allows for additional features such as alarms or wireless IoT integration.
