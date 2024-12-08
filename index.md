---
layout: home
title: FPGA VGA Driver Project by Andrew Fox
tags: fpga vga verilog
categories: demo
---


Welcome to my FPGA VGA Driver Project blog! Follow my progress as I learn, solve challenges and adapt a Verilog-based VGA driver for the Basys3 development board.

## **Template VGA Design**
### **Project Set-Up**
The project began with setting up Vivado and configuring the Basys3 development board for FPGA programming. I moved the necessary files from OneDrive to a local directory due to access issues and successfully set up my workspace in `C:\Temp`. 
While using `C:\Temp` I had to keep in mind that this directory might get wiped each week so uploading the changed code back to OneDrive each week was necessary.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/overview1.png">
The provided template code included VGA timing logic for a 640x480 resolution display and a testbench for initial verification of the design. This provided a solid foundation for further development.

### **Template Code**
The template code was a simple colour cycle that showed 8 different colours one after another on the VGA display. It used a state machine implemented in Verilog with each state representing a specific colour. The RGB values for these colours were predefined in the code.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/sources.png">


The VGA timing logic made sure the display worked at 60 Hz and matched the 640x480 resolution. By changing (`clk_out1`) Output Freq Requested in (`u_clock`) to 25.2MHz this allows the colours to change smoothly on the screen showing how to use basic timing and state machines to control a VGA display.
Below are the equations I did to find the Pixel Clock
```
Pixel Clock = Total Pixels Per Frame x Refresh Rate

For 640x480 at 60 Hz:

Total pixels per frame = 800 (horizontal total) x 525 (vertical total) = 420,000
Pixel clock = 420,000 x 60 Hz =25,200,000Hz = 25.2 MHz
```
<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/clock.png">

### **Simulation**

I simulated the provided template using the supplied Verilog testbench. The simulation outputs matched the expected timing sequences confirming the integrity of the design and that it was ready for synthesis.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/firstsim.png">

### **Synthesis**
The next step was synthesizing the template in Vivado. The synthesis and implementation process ran without issues and I generated the bitstream file. I successfully loaded this onto the Basys3 board confirming the example design displayed correctly on the VGA monitor.
<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/Screenshot%202024-12-02%20173115.png">


### **Demonstration**
The template successfully displayed the example colour cycle on the monitor during the demonstration phase. This provided a great starting point for customizing and enhancing the design.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/ezgif.com-video-to-gif-converter.gif">

---

## My VGA Design Edit
### **Introduction**
My first objective was to modify the design to include an extended colour cycle. This required debugging gaining an understand of the sample code and adapting the state machine in the code to support more colours.

### **Code Adaptation**
I began by adding a new colour **GREY** to the existing colour cycle. During this process I discovered that RGB values in Verilog are represented from right to left in binary. This means that the least significant bits represents red, followed by green and then blue Understanding this order was important for correctly setting the RGB values for the new colours. To extend the colour cycle I decided to implement a 12-colour gradient moving from red to green. These colours are defined using parameters for clarity in the code:
```verilog
parameter Bright_Red = 0, Vermilion = 1, Orange_Red = 2, Bright_Orange = 3, Golden_Orange = 4, Mustard_Yellow = 5, Goldenrod = 6, Olive_Green = 7, Lime_Yellow = 8, Bright_Lime_Green = 9, Electric_Green = 10, Pure_Green = 11;
```
Each colour smoothly transitions from one shade to the next creating a visually appealing gradient. This experience helped me learn how to work with parameters in Verilog to manage complex designs.

My initial attempt at creating a 12 colour cycle revealed a problem: the design would reset after the 8th colour each time. After debugging I realized that the state register was only 2 bits wide allowing for only 8 states. To fix this I expanded the register to 3 bits (`reg[3:0] state, state_next;`) which allowed for up to 12 states. This change successfully enabled a full 12-colour cycle without resets.

To adjust the speed of the colour transitions I modified the (`COUNT_TO`) parameter which determines the delay for state changes. Originally set to (`32'b1 << 26`) I reduced it to (`32'b1 << 23`). This reduced the delay and made the colour changes faster making it appear that the colours are smoothly shifting along a gradient.

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

### **Simulation**

The updated design was simulated using the provided testbench. The waveform confirmed a smooth transitions between all 12 colours validating the changes I made to the state machine. Debugging during this phase ensured that the design was error-free before proceeding to synthesis.

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/simulation.png">

### **Synthesis**

Synthesising the modified design involved generating a new bitstream for the Basys3 board. The synthesis process ran successfully and I implemented the design onto the hardware. The extended 12-colour cycle displayed as intended.
Since (`vgaBLUE`) is not used in my design it is cut off from the rest of the Schematic
<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/schematic.png">

### **Demonstration**
Here is the final demonstration of my unique 12-colour red-to-green  VGA design running on the Basys3 board:

<img src="https://raw.githubusercontent.com/AndrewFoxATU/SoC-Project/main/docs/assets/images/IMG_8215-ezgif.com-video-to-gif-converter.gif">

### **Conclusion**
This project has been a rewarding learning experience. Debugging and solving challenges such as expanding the state machine developing my understanding of Verilog and hardware design principles. I look forward to further exploring SoC design and FPGA programming.

### **References**
[1] M. Lynch, "Provided template code and System on Chip Design and Verification classes," lecture, ATU, Galway, 2024.

[2] OpenAI, "ChatGPT: Language model for debugging code," Online. Available: [https://chat.openai.com/](https://chatgpt.com/). [Accessed: Nov 2024]. 

[3] ᴀꜱʜᴇᴇꜱʜ ᴍɪꜱʜʀᴀ Youtube, "VGA timing | FPGA implementation | Vivado," Online. Available: [https://stackoverflow.com/questions/34523091/how-do-vga-control-signals-work-in-verilog-hdl](https://www.youtube.com/watch?v=P9EP-D6Z85A&t=324s&ab_channel=%E1%B4%80%EA%9C%B1%CA%9C%E1%B4%87%E1%B4%87%EA%9C%B1%CA%9C%E1%B4%8D%C9%AA%EA%9C%B1%CA%9C%CA%80%E1%B4%80),  [Accessed: Nov 2024].




