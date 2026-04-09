
* * *

FY6900 / FY6800 Serial Protocol & Command Reference
===================================================

This documentation describes the serial communication with the FeelElec FY6900 signal generator (and largely identical models like the FY6800).

**Serial Parameters:**

*   **Baud rate:** 115200
*   **Data bits:** 8
*   **Parity:** None (8N1)
*   **Termination:** Every command strictly requires a newline character (`\n` or `LF`) at the end.

* * *

1\. System & Global Commands
----------------------------

These commands control general device settings and memory management.

| Set Command | Parameter / Range | Read Command | Function |
| --- | --- | --- | --- |
| \-  | \-  | `UMO` | Read device model (e.g., `FY6900-60M`) |
| \-  | \-  | `UVE` | Read firmware version |
| `UBZ[x]` | `1` (On) / `0` (Off) | `RBZ` | Buzzer (Key tones) on/off |
| `UMS[x]` | `0` (Master) / `1` (Slave) | `RMS` | Uplink Mode |
| `UUL[x]` | `1` (On) / `0` (Off) | `RUL` | Uplink Status |
| `WUL` | \-  | \-  | Unlock keypad on the device |
| `USN[x]` | `0` to `19` | \-  | Save current setup to memory slot `[x]` |
| `ULN[x]` | `0` to `19` | \-  | Load setup from memory slot `[x]` |

> **Hardware Quirk: Return values for System Status** When using the read commands `RBZ`, `RMS`, and `RUL`, the device does _not_ return `1` or `0` as expected!
> 
> *   **Buzzer (`RBZ`):** `255` = On, `0` = Off
> *   **Uplink (`RUL`):** `255` = On, `0` = Off
> *   **Master/Slave (`RMS`):** `0` = Master, `255` = Slave
>     

* * *

2\. Channel Control (CH1 & CH2)
-------------------------------

Commands for CH1 start with **WM** (Write Main) or **RM** (Read Main). Commands for CH2 start with **WF** or **RF** (likely "Frequency" or "Follower").

_Example: `WMA...` sets the amplitude on CH1, `WFA...` sets it on CH2._

| CH1 Set | CH2 Set | Read Command | Function | Format / Scaling |
| --- | --- | --- | --- | --- |
| `WMF` | `WFF` | `RMF` / `RFF` | Frequency | In µHz (14 digits). _Example: 1 kHz = 1000000000_ |
| `WMA` | `WFA` | `RMA` / `RFA` | Amplitude | Value \* 10000 (e.g., 5V = 50000) |
| `WMO` | `WFO` | `RMO` / `RFO` | Offset | Value \* 1000 (e.g., 2V = 2000) |
| `WMD` | `WFD` | `RMD` / `RFD` | Duty Cycle | Value \* 1000 (e.g., 50% = 50000) |
| `WMP` | `WFP` | `RMP` / `RFP` | Phase | Value \* 1000 (e.g., 180° = 180000) |
| `WMW` | `WFW` | `RMW` / `RFW` | Waveform | Index number of the waveform (Sine = 0, Arb 1 = 36) |
| `WMN` | `WFN` | `RMN` / `RFN` | Output On/Off | `1` = On, `0` = Off |

> **Hardware Quirk: The CH2 Waveform Index Shift** The index numbers for the waveforms are shifted for CH2 starting from index 5! While the "Adj-Pulse" waveform is located at index 5 for CH1, it is entirely missing for CH2 (since CH2 hardware doesn't support this waveform). _Workaround when sending to CH2:_ If the selected waveform index is `>= 5`, the command sent to CH2 must be decreased by 1 (`WFW + (Index - 1)`).

> **Hardware Quirk: Duty Cycle "Ghost Values"** Technically, the Duty Cycle is only relevant for the **Rectangle (Index 2)** and **CMOS (Index 4)** waveforms. For all other waveforms, the physical LCD often displays a constant "50.0%", but attempting to change this value has no physical effect on the output signal.

* * *

3\. Special Waveforms: Trapezoid & Adj-Pulse
--------------------------------------------

These parameters apply **only to CH1** and suffer from severe firmware inconsistencies. Sending and reading are partially cross-mapped or completely broken in the native hardware.

| Waveform | Parameter | Send Cmd | Read Cmd | Firmware Bug / Workaround |
| --- | --- | --- | --- | --- |
| **Trapezoid** _(Index 3)_ | Rise (ns) | `WMH` | `RMH` | Parameter labels and underlying serial commands are inconsistent compared to the official Windows software. |
| **Adj-Pulse** _(Index 5)_ | Pulse / Phas (ns) | `WMS` | `RMS` | **Reading is broken!** The read command `RMS` constantly returns an invalid value of `255`. This value must be cached locally in your controlling software; reading the actual value back from the hardware is impossible. |

* * *

4\. Modulation & Sweep
----------------------

### Modulation Center

| Command | Read | Function | Values |
| --- | --- | --- | --- |
| `WPF` | `RPF` | Modulation Mode | 4=AM, 5=FM, 6=PM, 0=FSK, 1=ASK, 2=PSK, 3=Burst |
| `WPM` | `RPM` | Modulation Source | 0=CH2, 1=VCO/EXT AC, 2=Manual, 3=EXT DC |
| `WPR` | `RPR` | AM Modulation (%) | Value (e.g., 100% = 100) |
| `WFM` | `RFM` | FM / FSK Deviation | Frequency in Hz (zero-padded) |
| `WPP` | `RPP` | PM Phase (°) | Value (e.g., 180° = 180) |
| `WPN` | `RPN` | Burst Counter | Number of bursts |
| `WMN1` | \-  | Mod. & Sweep Stop | Immediately turns off active modulation/sweep |

### Sweep / VCO Control

| Command | Function | Values |
| --- | --- | --- |
| `SXY` | Mode | 2=Sweep, 1=VCO |
| `SOB` | Sweep Object | 0=Freq, 1=Amp, 2=Offset, 3=Duty |
| `SST` | Sweep Start Value | Value (Scaling depends on the selected Object) |
| `SEN` | Sweep Stop Value | Value (Scaling depends on the selected Object) |
| `STI` | Sweep Time (Seconds) | 1 to 999 |
| `SMO` | Scaling | 0=Linear, 1=Logarithmic |
| `SMD` | Direction | 0=FWD, 1=BACK, 2=FWD/BACK |
| `KEY17` | Sweep Run/Stop Toggle | Simulates pressing the physical Run/Stop button |

* * *

5\. Arbitrary Waveform Upload (Memory 1 to 64)
----------------------------------------------

Uploading custom arbitrary waveforms is the most time-critical and error-prone part of the serial communication. A waveform always consists of **8192 points** with a **14-bit resolution** (value range `0` to `16383`). The transmission must strictly be sent as raw binary data in **Little Endian** format (Least Significant Byte first).

### Protocol Flow:

1.  **Initiate Handshake:** Send the string `DDS_WAVE[Slot]\n` (Slot = `01` to `64`).
2.  **Wait for Handshake:** The hardware will respond with `W\n` when it is ready to receive data. _(Note: This can take up to 1-2 seconds. It is highly recommended to retry sending the handshake up to 3 times if the device times out)._
3.  **Transfer Payload:** The exact **16,384 Bytes** (8192 points \* 2 bytes) must be sent in **4 chunks of 4096 bytes** each.
4.  **Observe Flash Pauses:** A mandatory pause of approx. **300 to 400 ms** must be implemented between sending the individual 4096-byte chunks. The device needs this time to write the buffer to its internal flash memory. Sending the data too fast will crash the hardware or corrupt the waveform.
5.  **Completion:** After successfully writing all 4 chunks, the device will output the characters `H` and `N` to confirm completion.

* * *
