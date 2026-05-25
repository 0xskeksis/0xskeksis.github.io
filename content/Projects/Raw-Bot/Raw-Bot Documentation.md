Sources:
- [[Bare-Metal Embedded C Programming]]


Soo i've got this project in my head, i want to make a robot that can walk like a spider but with 4 legs.
But i don't know anything in embedded programming.
So i buy a STM32f446RE, a book on the C embedded programming and start learning.
Oh! And i want to code all the robot in C without HAL or LL, only me and my code !

### Basic information
When we work with board like this, we have to know all of the addresses of the components and ressources, also all of the memory layout of the different registers, and the memory mapping in general. We have 4 main ressources to play with:
1. reference manual (register/peripherals)
2. user manual (board-specific)
3. data sheet (electrical + pinout)

There are so much things to learn when you start this field of study, that quite impressive BUT very exciting !

### The Bootloader
To start, i need to:
- configure the runtime startup
- initialize `.data` section
- clear the `.bss` section
- load the vector table
- setup the runtime C


### Vrac
PC13 pin is connected to the user push button B1

