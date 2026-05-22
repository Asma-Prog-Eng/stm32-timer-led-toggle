## Technical Implementation

### Clock Tree & Timer Configuration
The microcontroller is configured using the Internal High-Speed oscillator (**HSI**) multiplied via the **PLL** to achieve a **36 MHz System Clock (SYSCLK)**. 

With the APB divider set to 4, the Timer 2 internal clock operates at **18 MHz** (due to the automatic x2 multiplier applied to timer peripherals when the APB divider > 1).

The timer calculations follow the standard formula:

$$Update\_Event = \frac{TIM\_CLK}{(PSC + 1) \times (ARR + 1)}$$

### Register Configurations (18 MHz Input Clock)

To achieve precise timing intervals without CPU-blocking loops, the 16-bit **TIM2** registers are configured as follows:

*   **For 100 ms Toggle Interval (5 Hz Square Wave):**
    *   **Prescaler (PSC):** `47` (Clock is divided by $47 + 1 = 48$, resulting in a $375\text{ kHz}$ counter frequency)
    *   **Auto-Reload Register (ARR):** `37500` ($37,500 \text{ ticks} \div 375,000 \text{ Hz} = 0.1 \text{ s}$)

*   **For 10 ms Toggle Interval (50 Hz Square Wave):**
    *   **Prescaler (PSC):** `47` (Maintains the same $375\text{ kHz}$ counter frequency)
    *   **Auto-Reload Register (ARR):** `3750` ($3,750 \text{ ticks} \div 375,000 \text{ Hz} = 0.01 \text{ s}$)

### Features
*   **Hardware-Based Timing:** Uses TIM2 registers rather than CPU-blocking loops for precise intervals.
*   **Dual Frequency Modes:** Demonstrates clock configurations for both 100 ms and 10 ms toggle rates.
*   **Register-Level / HAL Understanding:** Showcases standard initialization sequences for STM32 GPIO and timer peripherals.

---

## Hardware Verification & Visuals

To verify the precision of the hardware timer, the output waveforms were captured and analyzed using a hardware logic analyzer and **PulseView** open-source software. 

### Timing Diagrams
The captures confirm that the GPIO toggles precisely at the expected intervals:
*   **10 ms Mode:** Generates a clean square wave with a 10 ms high/low pulse width ((50% duty cycle, 50 Hz frequency).
<img width="1918" height="740" alt="10ms" src="https://github.com/user-attachments/assets/2498d952-156e-40d4-b6ad-c75221431a0c" />
*   **100 ms Mode:** Generates a precise 100 ms high/low pulse width (50% duty cycle, 5 Hz frequency).
<img width="1916" height="740" alt="100ms" src="https://github.com/user-attachments/assets/3dca6771-99bf-4fd0-a828-e2f0a741683c" />

