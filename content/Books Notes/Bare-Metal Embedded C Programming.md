[[Embedded glossary]]

STM32 microcontroller (f446re)

To obtain the RCC_AHB1ENR Address we use this formula:
`RCC_AHB1ENR Address = RCC_BASE + 0x30 = 0x40023830`

Setting the GPIOAEN bit to 1 or 0 enable or disable the GPIOA Clock

##### Setting a bit
`register |= 1 << bit_position;`
##### Clearing a bit
`register &= ~(1 << bit_position);`

*" The MODER registry is composed of a pairs of bits, designated as 2y:2y+1 MODER\[1:0], where y ranges from 0 to 15, representing each of the 16 pins in the port (PIN0 to PIN15). \[...] The bit positions for MODER5 are calculated as 2\*y and (2\*y)+1
"*
`2*5 = 10 and 2*5 + 1 = 11`
Bits 10 and 11 in the MODER registry control the mode of the pin 5.


### GPIO
Stand for General Purpose Input/Output.
Very important part of the embedded programming.

GPIO have different ports, GPIOA, GPIOC, C, etc...
To name a pin we use this syntax:
PA5 -> port a, pin 5
PC12 -> port c, pin 12
This peripheral have many registers to interact with all of the pin in different ways.

##### GPIO_BSRR Register

*"The GPIO bit-set/register (GPIOx_BSRR) is a crucial register for controlling the state of GPIO pins. It provides atomic bitwise operations to set or reset individual bits, which ensures that no interrupts can disrupt the operations to set or reset individual bits, which ensures that no interrupts can disrupt the operation, maintaining data register integrity during modifications"*

- Bits 15:0 (BSy): Used to set the corresponding GPIO pin
- Bits 31:16 (BRy): Used to reset the corresponding GPIO pin

The next step is to turn the LED on when the user button is pressed.

The button is control by the port C of the GPIO.
We then need to set the button associated pin to input mode:

```c
void butto_init(void){
	RCC->AHB1ENR |= GPIOCEN // Set the clock for the GPIOC port
	
	// Set the PC13 pin as an input mode (00)
	GPIOC->MODER &= ~(1U<<26);
	GPIOC->MODER &= ~(1U<<27);
}
```


Then, check if the button is pressed or not, to do that we need the IDR register.
```c
#define BTN_PIN (1U << 13)

if (GPIOC->IDR & BTN_PIN)
	led_on();
else
	led_off();

```

The IDR register hold all the current state of the pin in the port.

### System Tick (SysTick) Timer

The SysTick is a peripheral used for task scheduling, system monitoring, time tracking.

Key-features:
- **24-bit Reloadable Counter**: Decrement from a specified value to 0, then reloads automatically to provide a continous timing operation
- **Core Integration**: Being part of the core, it requires minimal configuration and offers low-latency interrupt handling
- **Configurable Clock Source**: Systick can also operate on an external reference clock
- **Interrupt Generation**: When the counter reaches zero it can trigger an interrupt
Use-cases:
- OS Tick Generation
- Periodic Task Execution
- Time Delay Functions

##### SysTick Timer Register
Consists on four primary registers:
- Control and Status (SYST_CSR)
	- This register controls the SysTick timer's operation and provides status information
	- ENABLE (Bit 0): Enable or disable the SysTick counter
	- TICKINT (Bit 1): Enables or disables the SysTick interrupt
	- CLKSOURCE (Bit 2): Selects the clock source (0/1 external/processor clock)
	- COUNTFLAG (Bit 16): Indicates whether the counter has reached zero since the last read (1 = yes, 0 = no)
	- Every bits is reserved.
	
- Reload Value Register (SYST_RVR)
	- This register specifies the start value to load into the SysTick Current Value register
	- Bits \[31:24] Reserved
	- Bits \[23:0] RELOAD: This field specifies the value that the SysTick timer will load into the SYST_CVR register when the counter is enabled and when it reached zero
- Current Value (SYST_CVR)
- Calibration Value (SYST_CALIB)


### USART Protocol

USART stands for Universal Synchronous/Asynchronous Receiver Transmitter. It is a serial communication peripheral used to exchange data between electronic devices. USART and UART are widely used in embedded systems, allowing microcontrollers and other hardware components to communicate with each other.

Writing a UART driver is an important step for the next stages of this project, since serial communication is commonly used for debugging, logging, and communication between different pieces of hardware.

How does it work?

Before communication can happen, two devices need to establish a connection. UART communication typically requires two wires:

TX (Transmit)
RX (Receive)

To communicate correctly:

TX1 → RX2
RX1 → TX2

Both devices must also share a common ground connection to ensure the same electrical reference level.

Data transmitted through UART is organized into frames. A frame usually contains:

1 start bit
5 to 9 data bits (commonly 8)
An optional parity bit
1 or more stop bits

UART communication is asynchronous, meaning both devices do not share a clock signal. Instead, both sides must agree beforehand on communication parameters such as baud rate and frame format.

USART peripherals can also operate in synchronous mode, where an additional clock signal is transmitted alongside the data.

Baud Rate

The baud rate is one of the most important parameters in serial communication. It represents the number of signal changes transmitted per second.

It is important to distinguish baud rate from bit rate.

Baud rate → number of signal changes per second
Bit rate → number of bits transmitted per second

In UART communication, one signal change usually represents one bit, so baud rate and bit rate are often identical.

The baud rate matters a lot. A common analogy is two people trying to communicate while speaking at very different speeds. One person may send information faster than the other can process it, creating misunderstandings and communication errors.

Electronic devices behave similarly. If two devices are not configured with the same baud rate, data can become corrupted or lost entirely.

Some common baud rates:

300 bps → Very slow, historically used for long-distance communication
9600 bps → A very common default speed for embedded devices
19200 bps → Faster communication for applications exchanging more data
115200 bps → High-speed communication commonly used for debugging and logging on microcontrollers

For this project, USART2 is configured at 115200 baud, allowing debug messages to be sent from the STM32 to a serial terminal running on the computer.
