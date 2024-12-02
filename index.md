---
layout: home
title: FPGA VGA Driver Project
tags: fpga vga verilog
categories: demo
---

Welcome to my FPGA VGA Driver Project blog! Follow my progress as I learn, solve challenges and adapt a Verilog-based VGA driver for the Basys3 development board.

## Template VGA Design
### Project Set-Up
The project began with setting up Vivado and configuring the Basys3 development board for FPGA programming. I moved the necessary files from OneDrive to a local directory due to access issues and successfully set up my workspace in `C:\Temp`. 
(TALK ABOUT WHY C\temp was needed)

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/overview1.png">
The provided template code included VGA timing logic for a 640x480 resolution display and a testbench for initial verification of the design. This provided a solid foundation for development.

### Template Code
The template code was a simple colour cycle that showed 8 different colours one after another on the VGA display. It used a state machine implemented in Verilog with each state representing a specific colour. The RGB values for these colours were predefined in the code.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/sources.png">


The VGA timing logic made sure the display worked at 60 Hz and matched the 640x480 resolution. By changing (`clk_out1`) Output Freq requested in (`u_clock`) to 25.2MHz this allows the colours to change smoothly on the screen showing how to use basic timing and state machines to control a VGA display.
(talk about the timings change)
```
Pixel Clock = Total Pixels Per Frame x Refresh Rate

For 640x480 at 60 Hz:

Total pixels per frame = 800 (horizontal total) x 525 (vertical total) = 420,000
Pixel clock = 420,000 x 60 Hz =25,200,000Hz = 25.2 MHz
```

### Simulation
I simulated the provided template using the supplied Verilog testbench. The simulation outputs matched the expected timing sequences confirming the integrity of the design and that it was ready for synthesis.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/firstsim.png">

### Synthesis
The next step was synthesizing the template in Vivado. The synthesis and implementation process ran without issues and I generated the bitstream file. I successfully loaded this onto the Basys3 board confirming the example design displayed correctly on the VGA monitor.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/ezgif.com-video-to-gif-converter.gif">

### Demonstration
The template successfully displayed the example colour cycle on the monitor during the demonstration phase. This provided a great starting point for customizing and enhancing the design.

---
## My VGA Design Edit
### Introduction
My first objective was to modify the design to include an extended colour cycle. This required debugging gaining an understand of the sample code and adapting the state machine in the code to support more colours.

### Code Adaptation
I began by adding a new colour **GREY** to the existing colour cycle. During this process I discovered that RGB values in Verilog are represented from right to left in binary which was a key insight for the adaptations.

(talk about the RGB BINARY)

(TALK ABOUT 12 colour cycle gradiaent)

My initial attempt at creating a 12 colour cycle revealed a problem: the design kept resetting after the 8th colour. After debugging I realized that the state register was only 2 bits wide allowing for only 8 states. To fix this I expanded the register to 3 bits (`reg[3:0] state, state_next;`) which allowed for up to 12 states. This change successfully enabled a full 12-colour cycle without resets.

Here’s the relevant code snippet:

```verilog
// Updated COUNT_TO
module ColourCycle #(
parameter COUNT_TO = 32'b1 << 23)  // changed the 26 to 23 to make the cycle faster.

// Updated state machine for 12 colours
reg [3:0] state, state_next;

always @* begin
    case (state)
        //Define all 12 states with unique RGB combinations
        COLOUR: begin
            colour <= 12'bXxXxXxXxXxXx;  // 12 Xs represent RGB 12bit binary 
            if(count == COUNT_TO) state_next = NEXT_COLOUR;
        end
    endcase
end

```


### Simulation
The updated design was simulated using the provided testbench. The waveform confirmed smooth transitions between all 12 colours, validating the changes I made to the state machine. Debugging during this phase ensured that the design was error-free before proceeding to synthesis.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/simulation.png">

### Synthesis

Synthesising the modified design involved generating a new bitstream for the Basys3 board. The synthesis process ran successfully and I implemented the design onto the hardware. The extended 12-colour cycle displayed as intended.

### Demonstration
Here is the final demonstration of my unique 12-colour VGA design running on the Basys3 board:

(hardware demonstration photo)

### Conclusion
This project has been a rewarding learning experience. Debugging and solving challenges such as expanding the state machine developing my understanding of Verilog and hardware design principles. I look forward to further exploring SoC design and FPGA programming.





