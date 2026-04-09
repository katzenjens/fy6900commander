* * *

FY6900 / FY6800 Web Commander – User Manual
===========================================

This HTML-based application provides a complete remote control and user interface for the FeelElec FY6900 (and largely compatible FY6800) signal generator directly via your web browser.

The connection to the device is established via USB. The application runs entirely statically and locally within your computer's browser – **no data is transmitted externally!**

> ⚠️ **Important Note:** The ability to communicate directly with USB-connected devices via the browser (Web Serial API) is currently **only supported by Chromium-based browsers** (Google Chrome, Microsoft Edge, Opera). Mozilla Firefox or Apple Safari (iOS/macOS) unfortunately do not support this feature!

* * *

1\. Preparation & Drivers
-------------------------

Internally, the FY6900 is equipped with a **CH340 USB-to-Serial converter**. Before using the device, you may need to install the appropriate drivers for your operating system.

*   You can find the latest driver on the website of the chip manufacturer, WCH: 
    [Download CH341SER\_EXE](https://wch-ic.com/downloads/CH341SER_EXE.html)

Following successful driver installation, connect the FY6900 to your computer using a USB cable. To prevent connection issues, avoid using a USB hub if possible and connect directly to a port on your computer. Data transmission occurs at **115200 baud**.

* * *

2\. Installation & Launch
-------------------------

The Web Serial API strictly requires a secure context (`https://` or `localhost`). Simply double-clicking the `index.html` file from your local hard drive (`file://`) will be blocked by the browser for security reasons!

**You have two options:**

1.  **Use online:** Access the Commander directly via this website: `[Insert Link to GitHub Pages site here]` (No installation required).
2.  **Host locally:** Clone the repository and serve the files using existing webspace or a local web server (e.g., VS Code Live Server, Python `http.server`, etc.).

* * *

3\. Establishing a Connection
-----------------------------

Upon initially loading (or reloading) the page, the status will show as "Offline".

1.  Click the orange **"CONNECT SERIAL"** button in the top right corner.
2.  A browser dialogue will open, allowing you to select the appropriate COM port for your device. (If you are unsure, simply try the available ports one by one).
3.  The computer usually connects within seconds. Once the connection is established, "CONNECTED" will appear in green in the top left corner, along with the recognised device model and firmware version. The connect button will change into a grey **"REFRESH ALL"** button.

* * *

4\. Operating the Main Channels (CH1 & CH2)
-------------------------------------------

### Frequency Selection

Both channels offer extremely fine resolution down to the microhertz range.

*   Select your desired **step size** (from "MHz" down to "μHz") in the top bar.
*   Hover your mouse over the graphical **rotary knob** and use your **mouse scroll wheel** to conveniently increase or decrease the frequency. Changes are sent to the device _in real time_!
*   For larger jumps, use the small **\[ - \] and \[ + \] (x100) buttons** located beneath the knob to instantly add or subtract exactly one hundred times your selected step size.
*   You can toggle the outputs on or off live using the respective **CH ON / OFF** buttons to the right of the knob.

### Voltages and Waveform (Safety Concept)

The recommended procedure is: First select the desired waveform (Wave), then adjust the amplitude, offset, and (if applicable) parameters like Duty Cycle.

**Caution:** Not all parameters are available for every waveform (the software automatically greys out unnecessary fields). For safety reasons (to prevent frying connected, sensitive components via accidental scroll wheel movements resulting in excessively high voltages), these settings are **not applied in real time**!

*   Manually altered values are **highlighted in orange**.
*   Only clicking the green **"SET"** button transmits these parameters to the device.
*   Clicking "REFRESH ALL" in the top right discards any unsaved inputs and reads the current, actual state fresh from the hardware.

* * *

5\. Modulation Center
---------------------

To generate modulated signals, proceed step-by-step:

1.  Set the **carrier frequency** and waveform (usually Sine) on **CH1**.
2.  Set the **modulation frequency** on **CH2**. Click "SET" for both channels and toggle both outputs to "ON".
3.  Expand the **"MODULATION CENTER"** accordion menu.
4.  Work your way from left to right: **1\. Mode** -\> **2\. Source** -\> **3\. Parameters**.
5.  In the **4\. Modulation** section, click **"START"**. The modulated signal is now output on CH1, whilst the pure modulation signal (e.g., for triggering an oscilloscope) is output on CH2.
6.  Clicking **"STOP"** ends the modulation and returns CH1 to outputting the unmodulated carrier frequency.

* * *

6\. Sweep / VCO Control
-----------------------

*   Prepare CH1 (apply amplitude, offset, and waveform via "SET").
*   Expand the **"SWEEP / VCO CONTROL"** menu and enter your Mode, Object, Direction, as well as Start, Stop, Time, and Scaling values.
*   _Note:_ These sweep parameters are not permanently stored in the hardware and must be entered here in the software.
*   Click **"SET VALUES"** to load the parameters into the device's temporary memory.
*   Use **"START / STOP"** to activate or pause the sweep.
*   **"RETURN"** completely exits the sweep mode and switches the device back to normal operation.

* * *

7\. System & ARB Wave Editor
----------------------------

This menu is divided into two sections:

**Device Control & Memory (Left):** Control the buzzer, uplink modes (Master/Slave), or load/save complete device setups to the internal memory slots (Slot 0-19) of the FY6900.

**Arb Waveform Editor (Right):** Draw and modify your own arbitrary waveforms using your mouse directly on the canvas.

*   **Tools:** Generate standard waves (Sine, Square, etc.), smooth out hand-drawn curves using **"Smooth"**, or zero the signal.
*   **Files:** **"SAVE"** your creation as an `.fy` file to your hard drive, or **"LOAD"** existing `.fy` files into the editor.
*   **Upload:** Select one of the 64 Arb memory slots and click **"UPLOAD"** to permanently flash the drawn waveform into the FY6900's flash memory. A progress bar indicates the status.

* * *

8\. Notes & Debugging
---------------------

This tool was specifically developed and tested for an **FY6900 running firmware version 1.3**. It should also work with newer versions and the predecessor, the FY6800, but a 100% guarantee for all revisions cannot be given. The layout is optimised for desktop use.

Should you encounter incompatibilities or wish to investigate the protocol, expand the **"DEBUG TERMINAL"** right at the bottom. Here, you can view the raw serial RX/TX traffic and manually send commands (e.g., from the accompanying command reference) to the device.

Have fun with your "supercharged" FY6900!
