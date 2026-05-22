DSD project:

Stopwatch/Timer-(MM:SSformat) with start/stop/reset/pause inputs

NAME:Udit Raj Kashyap

REG NO.: 24BEC0668

SUBMITTED TO: Vishal Gupta

1. PROBLEM IDENTIFICATION

Digital stopwatches are essential in timing activities for sports, labs, and industrial processes. The challenge is to design and implement a programmable stopwatch/timer that displays minutes and seconds (MM:SS) and responds to start, stop, reset, and pause commands using an FPGA platform

2. SYSTEM DESIGN

The system architecture uses push-buttons for user input (start, stop, reset, pause), an FPGA for digital control, clock divider logic, BCD conversion for MM:SS elaboration, and 7-segment displays for visualization. The circuit diagram includes signal connections for push buttons, display drivers, clock input, and FPGA GPIO mapping

Main Logic:

Clock Divider: Reduces 50 MHz clock to 1 Hz for second-by-second counting.

Counters: Minutes (0–99) and seconds (0–59) counters are used, updating every second pulse when the stopwatch is running.

State Machine: Logic processes button presses to control running, pausing, stopping, and resetting.

BCD Conversion: Minute and second values are split into BCD digits for easy display.

Output:

7-Segment Displays: Four onboard 7-segment displays show MM:SS, each digit driven through BCD to 7-segment decoder modules.

Pin Mapping:

The pin assignment was imported from the already provided csv file pin assignment of the DE2-115 FPGA BOARD

3. COMPONENT SELECTION

For this MM:SS stopwatch project, each component was chosen for optimal functionality and cost efficiency:

FPGA Board (Altera DE2-115, EP4CE115F29C7):

Functionality: The DE2-115 provides a large number of logic elements (114,480 LEs), integrated memory, and versatile I/O, enabling complex timer control, precise clock division, and multi-digit display management on a single board.​

Cost: Cyclone IV devices are known for a low price point relative to their feature set, making them ideal for academic and prototyping use. The board consolidates many interfaces, reducing the need for extra modules and saving further cost.​

Flexibility: Built-in support for push buttons, LEDs, and extensive GPIO allows direct hardware interaction, crucial for stopwatch controls and feedback signals.

Push Buttons & Switches:

Functionality: These provide tactile user input for start, stop, pause, and reset operations. They are simple to integrate and debounce in FPGA logic.

Cost: Standard push buttons are low-cost components and come pre-mounted on the DE2-115 board.

7-Segment Displays:

Functionality: Readily available on the DE2-115 board. Their multi-digit capability enables MM:SS visualization appropriate for human users. BCD-to-7-segment decoding logic can be implemented efficiently in FPGA fabric.​

Cost: Including them in FPGA design eliminates the need for external display modules or costly screens. Multiplexing techniques enable efficient use of board pins without increasing component count.​

Clock Source/Crystal Oscillator:

Functionality: DE2-115 features a reliable onboard oscillator, delivering a fixed frequency (e.g., 50 MHz or 100 MHz), which is subdivided in Verilog logic to create accurate 1-second pulses for timekeeping.​

Cost: Integrated into the board, no extra expenditure required.

FPGA Utility Components:

Memory Blocks: Needed for buffering settings or storing time/histories; DE2-115 provides ample embedded RAM.

Voltage Regulation and Interface ICs: Pre-integrated on board for stable supply and cross-device communication.

4. IMPLEMENTATION:

5. TESTING AND VERIFICATION :

THE code is being compiled with 0 errors and the 

Manually the stop watch fpga was tested as seen in the video attached below:

The assigned reset , stop, pause and start buttons were tested along with the clk function in the 7 bit binary segment display.

As seen the stop watch was working according to the specifications given.

6.ANALYSIS AND OPTIMIZATION

Efficiency

Mention that the Verilog design is highly efficient, using minimal FPGA resources (logic elements and I/O pins).​

The use of a single clock divider and modular code structure (counters, state machine, display decoder) keeps resource usage and power low.

Reliability

State that the clock divider and all counters are synchronous with the global clock, which prevents glitches, ensuring error-free counting and display updates.

Proper timing closure was achieved (no timing violations), making the circuit operate reliably even at the target clock speed.

7.Potential Areas for Improvement

adding debounce logic to clean up noisy button presses and further improve reliability.​

implementing advanced features in future versions: support for lap timing, countdown mode, or expanded display (hours:minutes:seconds).

further reducing resource usage by sharing display driving logic or exploring lower-power clocking strategies.

Propose that the design can be scaled for additional functions (alarms, wireless/IoT features) due to its modular and area-efficient nature

Additional photo/photos: