# PolarFire FPGA Splash-Kit Mi-V Sample FPGA Designs
This folder contains Tcl scripts that build Libero SoC v2023.1 design projects for the PolarFire FPGA Splash-Kit. These scripts are executed in Libero SoC to generate the sample designs. All cores boot from memory at 0x8000_0000.

## Notice
1) Due to an issues found in the MIV_RV32 v3.1.100 with the MTVECs address, it is not recommended to use MIV_RV32 v3.1.100 for any FreeRTOS examples. You may continue to use MIV_RV32 v3.0 with FreeRTOS examples. 

2) There is also an issue which effects all MIV_RV32 cores, when using fast interrupts where the return address can become corrupted. There software workaround can be applied in the entry.S in MIV_RV32 HAL file as shown below untill the issue is fixed in the IP.

.macro STORE_CONTEXT
addi sp, sp, -SP_SHIFT_OFFSET*REGBYTES
SREG x1, 0 * REGBYTES(sp)
SREG x1, 0 * REGBYTES(sp) // re-write the return address to workaround
SREG x2, 1 * REGBYTES(sp)
SREG x3, 2 * REGBYTES(sp)

Please see the latest MIV_RV32 HAL available [here](https://github.com/Mi-V-Soft-RISC-V/platform/tree/main/miv_rv32_hal).

A new version of the MIV_RV32 will be released to fix both the issues mentioned above.


#### PF_Splash_Kit_MIV_RV32_BaseDesign

| Config  | Description|
| :------:|:----------------------------------------|
| CFG1    | This design uses the MIV_RV32 core configured as follows: <ul><li>RISC-V Extensions: IMC</li><li>Multiplier: MACC (Pipelined)</li><li>Interfaces: AHBL Initiator (mirrored), APB3 Initiator</li><li>Internal IRQs: 1</li><li>TCM: enabled</li><li>System Timer: Internal MTIME enabled, Internal MTIME IRQ enabled</li><li>Debug: Enabled</li><li>An example program is stored in the LSRAM to boot out the box</ul>|
| CFG2    | This design uses the MIV_RV32 core configured as follows: <ul><li>RISC-V Extensions: IM</li><li>Multiplier: Fabric</li><li>Interfaces: AXI4 Master (mirrored), APB3 Initiator</li><li>Internal IRQs: 1</li><li>TCM: Disabled</li><li>System Timer: Internal MTIME enabled, Internal MTIME IRQ enabled</li><li>Debug: Enabled</li><li>An example program is stored in the LSRAM to boot out the box</ul>|
| CFG3    | This design uses the MIV_RV32 core configured as follows: <ul><li>RISC-V Extensions: I</li><li>Multiplier: none</li><li>Interfaces: APB3 Initiator</li><li>Internal IRQs: 1</li><li>TCM: enabled</li><li>System Timer: Internal MTIME enabled, Internal MTIME IRQ enabled</li><li>Debug: Enabled</li></ul>|


#### PF_Splash_Kit_MIV_RV32IMA_BaseDesign

| Config  | Description |
| :------:|:------------|
| CFG1    |This design uses the MIV_RV32IMA_L1_AHB core with an **AHB** interface for memory and peripherals. <li>An example program is stored in the LSRAM to boot out the box|
| CFG2    |This design uses the MIV_RV32IMA_L1_AXI core with an **AXI3** interface for memory and peripherals. <li>An example program is stored in the LSRAM to boot out the box|


#### PF_Splash_Kit_MIV_RV32IMAF_BaseDesign

| Config  |Description |
| :------:|:-----------|
| CFG1    |  This design uses the MIV_RV32IMAF_L1_AHB core with an **AHB** interface for memory and peripherals. <li>An example program is stored in the LSRAM to boot out the box|


## <a name="quick"></a> Instructions

#### Running Libero SoC in GUI mode
    1. Open Libero SoC
    2. Execute the script, Project -> Execute Script
    3. Select the directory that the script is located in using the "..."
    4. Select the script and select "Open"
    5. In the arguments text box, enter the type of configuration you want e.g. "CFG1"
    6. Select the "Run" button to execute the script
    7. Once complete, a script report will be generated.

Libero executes the script and opens the Mi-V sample project targeted for a production silicon (PS) die. The script adds Timing constraints to the project for Synthesis, Place and Route, and Timing Verification. Additionally, I/O Constraints are added to the project for Place and Route. The project can now be taken through the remainder of the Libero SoC design flow.

#### Running Libero SoC in GUI mode, with Script Arguments
    1. Open Libero SoC
    2. Execute the selected script, Project -> Execute Script
    3. Select the directory that the script is located in, using the "..."
    4. Select the script and select "Open"
    5. In the arguments text box, enter "CFG1 SYNTHESIZE PS"
    6. Select the "Run" button to execute the script
    7. Once complete, a script report will be generated.

In this example, the arguments "CFG1 SYNTHESIZE PS" are entered to take the production silicon (PS) die project through to Synthesis.

Libero executes the script and opens the Mi-V sample project targeted for a production silicon (PS) die. The script adds Timing constraints to the project for Synthesis, Place and Route, and Timing Verification. Additionally, I/O Constraints are added to the project for Place and Route. The project can now be taken through the remainder of the Libero SoC design flow.

## <a name="Script arguments"></a> Script Arguments
In the examples above the arguments "CFG1" and "CFG1 SYNTHESIZE PS" were entered. The complete set of script arguments are documented here.

#### First argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| CFG1..CFGn                | Generate a sample design for the selected configuration  |


#### Second argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| SYNTHESIZE                | Run synthesis on the design  |
| PLACE_AND_ROUTE           | Run place and route on the design  |
| GENERATE_BITSTREAM        | Generate the bitstream for the design|
| EXPORT_PROGRAMMING_FILE   | Export the programming file (.job) |


#### Third argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| PS | Build a base design targeted for 'PS' die |
| ES | Build a base design targeted for 'ES' die |


## Design Features
The Libero designs include the following features:
* A soft RISC-V processor operating at 50 MHz
* A RISC-V debug block allowing on-target debug using SoftConsole
* An Extended Subsystem with integrated peripherals
* Target SRAM/TCM memory (32kB)
* User peripherals: MIV_ESS, 2 Timers, UART, 2 GPIO Inputs and 4 GPIO Outputs (GPIOs use fixed configs for simplicity and better resource utilization)


The peripherals in this design are located at the following addresses.
#### MIV_RV32 based configurations
| Peripheral (MIV_ESS)             | Address Start | Address End    |
| ------------------------------:  |:-------------:|:--------------:|
| PLIC                             | 0x7000_0000   | 0x70FF_FFFF    |
| UART                             | 0x7100_0000   | 0x71FF_FFFF    |
| Timer                            | 0x7200_0000   | 0x72FF_FFFF    |
| CoreTimer_0 / MIV_ESS_APBSLOT3   | 0x7300_0000   | 0x73FF_FFFF    |
| CoreTimer_1 / MIV_ESS_APBSLOT4   | 0x7400_0000   | 0x74FF_FFFF    |
| GPIO                             | 0x7500_0000   | 0x75FF_FFFF    |
| SPI                              | 0x7600_0000   | 0x76FF_FFFF    |
| uDMA                             | 0x7800_0000   | 0x78FF_FFFF    |
| WDOG                             | 0x7900_0000   | 0x79FF_FFFF    |
| I2C                              | 0x7A00_0000   | 0x7AFF_FFFF    |
| MIV_ESS_APBSLOTB_BASE            | 0x7B00_0000   | 0x7BFF_FFFF    |
| MIV_ESS_APBSLOTC_BASE            | 0x7C00_0000   | 0x7CFF_FFFF    |
| MIV_ESS_APBSLOTD_BASE            | 0x7D00_0000   | 0x7DFF_FFFF    |
| MIV_ESS_APBSLOTE_BASE            | 0x7E00_0000   | 0x7EFF_FFFF    |
| MIV_ESS_APBSLOTF_BASE            | 0x7F00_0000   | 0x7FFF_FFFF    |
| SRAM/TCM                         | 0x8000_0000   | 0x8000_7FFF    |


#### Legacy core based configurations:
| Peripheral (Standalone)| Address       |
| ----------------------:|:-------------:|
| CoreUARTapb            | 0x7000_1000   |
| CoreGPIO_IN            | 0x7000_2000   |
| CoreTimer_0            | 0x7000_3000   |
| CoreTimer_1            | 0x7000_4000   |
| CoreGPIO_OUT           | 0x7000_5000   |
| SRAM                   | 0x8000_0000   |
