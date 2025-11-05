# Running a LibreLane Flow

## Logic Circuit: 32-Bit Parallel Multiplier

We use a simple design: a 32-bit parallel multiplier that performs a simple multiplication between MP and MC and outputs the product on a bus P.

The MP32 uses a serial-to-parallel multiplier, a serializer, a deserializer, and a control unit, see the following figure.

![Multiplier](Bilder5/Multiplier.jpg)

The serial-to-parallel 32-bit multiplier SPM block in the PM32 executes a simple shift-add algorithm, multiplying the parallel input x by each bit of the serial input y as it is shifted in. The product is generated and output serially on line p.

## Hardware Design and Hardware Description Language (HDL)

1. The RTL source code design consists of two files: pm32.v and spm.v.

_pm32.v file:_


```
// A signed 32x32 Multiplier utilizing SPM
// 
// Copyright 2016, mshalan@aucegypt.edu

`timescale		    1ns/1ps
`default_nettype    none

module pm32 (
    input wire          clk,
    input wire          rst,
    input wire          start,
    input wire  [31:0]  mc,
    input wire  [31:0]  mp,
    output reg  [63:0]  p,
    output wire         done
);
    wire        pw;
    reg [31:0]  Y;
    reg [7:0]   cnt, ncnt;
    reg [1:0]   state, nstate;

    localparam  IDLE=0, RUNNING=1, DONE=2;

    always @(posedge clk or posedge rst)
        if(rst)
            state  <= IDLE;
        else 
            state <= nstate;
    
    always @*
        case(state)
            IDLE    :   if(start) nstate = RUNNING; else nstate = IDLE;
            RUNNING :   if(cnt == 64) nstate = DONE; else nstate = RUNNING; 
            DONE    :   if(start) nstate = RUNNING; else nstate = DONE;
            default :   nstate = IDLE;
        endcase
    
    always @(posedge clk)
        cnt <= ncnt;

    always @*
        case(state)
            IDLE    :   ncnt = 0;
            RUNNING :   ncnt = cnt + 1;
            DONE    :   ncnt = 0;
            default :   ncnt = 0;
        endcase

    always @(posedge clk or posedge rst)
        if(rst)
            Y <= 32'b0;
        else if((start == 1'b1))
            Y <= mp;
        else if(state==RUNNING) 
            Y <= (Y >> 1);

    always @(posedge clk or posedge rst)
        if(rst)
            p <= 64'b0;
        else if(start)
            p <= 64'b0;
        else if(state==RUNNING)
            p <= {pw, p[63:1]};

    wire y = (state==RUNNING) ? Y[0] : 1'b0;

    spm #(.SIZE(32)) spm32(
        .clk(clk),
        .rst(rst),
        .x(mc),
        .y(y),
        .p(pw)
    );

    assign done = (state == DONE);

endmodule
```

_spm.v file:_

```
/*
    A Serial-Parallel Multiplier (SPM)
    Modeled after the design outlined by ATML for their 
    AT6000 FPGA in application notes DOC0716 and DOC0529:
        - https://ww1.microchip.com/downloads/en/AppNotes/DOC0529.PDF
        - https://ww1.microchip.com/downloads/en/AppNotes/DOC0716.PDF
    
    Implemented by mshalan@aucegypt.edu, 2016
*/

`timescale		    1ns/1ps
`default_nettype    none

module spm #(parameter SIZE = 32)(
    input wire              clk, 
    input wire              rst,
    input wire              y,
    input wire [SIZE-1:0]   x,
    output wire             p
);
    wire [SIZE-1:1]     pp;
    wire [SIZE-1:0]     xy;

    genvar i;

    CSADD csa0 (.clk(clk), .rst(rst), .x(x[0]&y), .y(pp[1]), .sum(p));
    
    generate 
        for(i=1; i<SIZE-1; i=i+1) begin
            CSADD csa (.clk(clk), .rst(rst), .x(x[i]&y), .y(pp[i+1]), .sum(pp[i]));
        end 
    endgenerate
    
    TCMP tcmp (.clk(clk), .rst(rst), .a(x[SIZE-1]&y), .s(pp[SIZE-1]));

endmodule


// Carry Save Adder
module CSADD(
    input wire  clk, 
    input wire  rst,
    input wire  x, 
    input wire  y,
    output reg  sum
);

    reg sc;

    // Half Adders logic
    wire hsum1, hco1;
    assign hsum1 = y ^ sc;
    assign hco1 = y & sc;

    wire hsum2, hco2;
    assign hsum2 = x ^ hsum1;
    assign hco2 = x & hsum1;

    always @(posedge clk or posedge rst) begin
        if (rst) begin
            sum <= 1'b0;
            sc <= 1'b0;
        end
        else begin
            sum <= hsum2;
            sc <= hco1 ^ hco2;
        end
    end
endmodule

// 2's Complement
module TCMP (
    input wire  clk, 
    input wire  rst,
    input wire  a, 
    output reg  s
);
  
    reg z;

    always @(posedge clk or posedge rst) begin
        if (rst) begin
            s <= 1'b0;
            z <= 1'b0;
        end
        else begin
            z <= a | z;
            s <= a ^ z;
        end
    end

endmodule
```

Online tutorials on the Verilog Hardware Description Language:

- <http://www.asic-world.com/verilog/veritut.html>

- <http://www.ece.umd.edu/courses/enee359a/verilog_tutorial.pdf>

Books:

- HDL Chip Design, Douglas J. Smith, Doone Publications, 1996_

- The Verilog Hardware Description Language, Donald E. Thomas, Philip R. Moorby, Springer, 2002_

IEEE Standard for Verilog, Hardware Description Language:

- <https://www.eg.bucknell.edu/~csci320/2016-fall/wp-content/uploads/2015/08/verilog-std-1364-2005.pdf>

## Configuration files for circuit synthesis in LibreLane

2. Designs using the Hardware Description Language (HDL) already available as Verilog files (*.v) require a configuration file in LibreLane to be converted into GDSII files (*.gds) for production in semiconductor fabs using

    - RTL synthesis and
    - layout synthesis processes.


    The following configuration file

    _config.json_

    is used to synthesize the pm32 design and contains the minimum of all variables required for a config.json file, namely: DESIGN_NAME, VERILOG_FILES, CLOCK_PERIOD, CLOCK_PORT! The inclusion of the two Verilog files pm32.v and spm.v is also immediately apparent.

```
{
"DESIGN_NAME": "pm32",
"VERILOG_FILES": \["dir::pm32.v", "dir::spm.v"\],
"CLOCK_PERIOD": 25,
"CLOCK_PORT": "clk"
}
```

The following link gives an overview to further variables:

<https://librelane.readthedocs.io/en/latest/reference/flows.html#flow-classic>

## Preparing and Starting a LibreLane Flow

3. Make sure you have started the Windows Subsystem Linux (WSL)
and are in the nix shell.

    If not, start WSL and invoke the nix shell to make all packages bundled with LibreLane available.

    _wsl -d Ubuntu_

    _nix-shell --pure ~/librelane/shell.nix_

    ![WSLnix](Bilder5/WSLnix.jpg)
    
4. Create a directory where you want to save the source files and change to it:

    _mkdir -p_ _/mnt/c/Users/User/LibreLane/Designs/pm32_

    _cd /mnt/c/Users/User/LibreLane/Designs/pm32_

    Use a directory that you also have easy access to via Microsoft Explorer.

5. Create the config.json file

    _touch config.json_

    ![config](Bilder5/config.jpg)

    Since we didn't install an editor in our nix shell, we'll add the above configuration content to the config.json file using Visual Studio Code, which is installed on Windows:

    To do this, open the config.json file using Windows Explorer and copy and paste the configuration content.

    ![config](Bilder5/config2.jpg)
    ![config](Bilder5/config3.jpg)


6. In the same way, create the files pm32.v and spm.v in this directory, using

    _touch pm32.v_

    _touch spm.v_

    and insert the RTL (Register Transfer Level) contents shown above into them.

    ![pm32spm](Bilder5/pm32spm.jpg)

7. Now use the following command to run the config.json file in your directory with librelane:

    _librelane /mnt/c/Users/User/LibreLane/Designs/pm32/config.json_

    Librelane accesses the Verilog files contained in the specified path (linting), which were named in the config.json file under the following variable:

    _"VERILOG_FILES": \["dir::pm32.v", "dir::spm.v"\],_

    and creates a subfolder named run in this path during the first run.

    _/mnt/c/Users/User/LibreLane/Designs/pm32/runs/_

    In this subfolder, LibreLane creates a subfolder with the date and time information for each run performed.

    This contains a separate folder for each of the more than 70 process steps,

    ![run](Bilder5/run1.jpg)
    ...
    ![run](Bilder5/run2.jpg)

as well as the folders

- final: This folder contains, among other things, the GDSII files, with all the information required for production in a semiconductor fab.
- tmp: Temporary files

and the files

- error.txt: It is important that this file is empty. Otherwise, the integrated circuit cannot be manufactured in the semiconductor fab or will ultimately exhibit fundamental functional failures.
- flow.txt: The flow.txt file contains a clear list of the steps during the librelane run.
- resolved.json: A JSON file that enables a more efficient rerun.
- warning.txt: The warnings listed here do not prevent the circuit from being manufactured in a fab, but may identify quality deficiencies.

These process steps will be discussed below...
