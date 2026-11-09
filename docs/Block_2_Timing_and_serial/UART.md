# UART -  Serial communication

**Goal:** to understand and implement UART serial communication using the RP2350, learning how to configure communication settings, transmit and receive data, and establish communication between devices using the Pico SDK.

## What I Learned

I learned how to use UART communication to send signals from one circuit to another using the RP2350. I understood how to configure the communication settings and how one microcontroller can transmit data that another microcontroller receives. This practice helped me understand how two circuits can exchange information through serial communication.

## Exercise - Communication Between Two Circuits.
### What I did 
In this exercise, we created two separate circuits using UART communication. One circuit transmitted a signal, while the other received it. This allowed us to observe how two microcontrollers can exchange data through serial communication.

### Code
```
#include "pico/stdlib.h"
#include "hardware/uart.h"
#include <stdio.h>
#define UART_ID uart0
#define BAUD_RATE 115200
#define TX_PIN 0
#define RX_PIN 1
#define Button_A 5
#define Button_B 6
#define Button_C 7
#define Led_A 2
#define Led_B 3
#define Led_C 4
int main() {
   stdio_init_all();
   gpio_init(Button_A);
   gpio_init(Button_B);
   gpio_init(Button_C);
   gpio_init(Led_A);
   gpio_init(Led_B);
   gpio_init(Led_C);
   gpio_set_dir(Led_A, 1);
   gpio_set_dir(Led_B, 1);
   gpio_set_dir(Led_C, 1);
   gpio_set_dir(Button_A, 0);
   gpio_set_dir(Button_B, 0);
   gpio_set_dir(Button_C, 0);
   gpio_pull_up(Button_A);
   gpio_pull_up(Button_B);
   gpio_pull_up(Button_C);
   gpio_set_function(TX_PIN, GPIO_FUNC_UART);
   gpio_set_function(RX_PIN, GPIO_FUNC_UART);
   uart_init(UART_ID, BAUD_RATE);
   uart_set_format(UART_ID, 8, 1, UART_PARITY_NONE);
   printf("[MAIN] Sistema iniciado...\n");
   int flag_a = 0;
   int flag_b = 0;
   int flag_c = 0;
   while (true) {
       // --- BOTON A ---
       if (gpio_get(Button_A) == 0 && flag_a == 0) {
           uart_putc(UART_ID, 'A');  // Se presionó
           flag_a = 1;
       } else if (gpio_get(Button_A) == 1 && flag_a == 1) {
           uart_putc(UART_ID, 'a');  // Se soltó
           flag_a = 0;
       }
       // --- BOTON B ---
       if (gpio_get(Button_B) == 0 && flag_b == 0) {
           uart_putc(UART_ID, 'B');  // Se presionó
           flag_b = 1;
       } else if (gpio_get(Button_B) == 1 && flag_b == 1) {
           uart_putc(UART_ID, 'b');  // Se soltó
           flag_b = 0;
       }
       // --- BOTON C ---
       if (gpio_get(Button_C) == 0 && flag_c == 0) {
           uart_putc(UART_ID, 'C');  // Se presionó
           flag_c = 1;
       } else if (gpio_get(Button_C) == 1 && flag_c == 1) {
           uart_putc(UART_ID, 'c');  // Se soltó
           flag_c = 0;
       }
       // --- RECEPCION UART ---
       while (uart_is_readable(UART_ID)) {
           char c = uart_getc(UART_ID);
           // Control Led A
           if (c == 'A') {
               gpio_put(Led_A, 1);
           } else if (c == 'a') {
               gpio_put(Led_A, 0);
           }
           // Control Led B
           if (c == 'B') {
               gpio_put(Led_B, 1);
           } else if (c == 'b') {
               gpio_put(Led_B, 0);
           }
           // Control Led C
           if (c == 'C') {
               gpio_put(Led_C, 1);
           } else if (c == 'c') {
               gpio_put(Led_C, 0);
           }
       }
       sleep_ms(10);
   }
}
```

### What could have gone better
What could have gone better was our teamwork and organization. We did not work well as a team because we were not given the opportunity to participate actively in the exercise. Also, the circuit did not work as expected: it transmitted random letters instead of the intended message. As a result, we did not fully understand the process or the code used, and I cannot confidently explain everything that was done. We could have improved this experience by distributing tasks more fairly, checking the code and connections, and making sure everyone had the opportunity to participate and understand the activity.

### Conclusion
In conclusion, this practice introduced us to UART communication and how it can be used to exchange data between two circuits. Although we were able to explore the basic idea of transmitting and receiving information, we faced difficulties with the code and the communication between the circuits. This experience showed us the importance of understanding the programming process, testing connections, and working together to achieve the expected results.
