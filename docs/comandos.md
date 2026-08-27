# Block 1 - GPIO 

**Goal:** to understand and control GPIO peripherals through memory-mapped registers, bitwise operations, masks, and RP2350 SIO registers.

## What I Learned

sdjbfhjhegfjhvvberiberi

## Circuit assembly for the exercises
(imagen simulación)

## Exercise 1 — 4 bit binary counter
Use the four LEDs as a 4-bit binary number.

`The program must count: 1 → 2 → 3 → ... → 15 → 0`

### What I did 


Code gived by Chatgpt:
```  

```  

Code made by ourselfs:
```  
sio_hw->gpio_set = (counter);
sleep_ms(200);
sio_hw->gpio_clr = (0b1111<<0);
sleep_ms(200);
counter++; 
```  

### **Video**
(Video del C1 funcionando)

## Exercise 2 — Bouncing light
Create a light that moves across the four LEDs and then returns. 

### What I did

Code: 
``` 
   while (true) 
   {for(pos=0;pos<=3;pos++)
    {  sio_hw->gpio_set = (1u<<pos);
       sleep_ms(200);
       sio_hw->gpio_clr = (1u<<pos);
       sleep_ms(200);
    }
    for(pos=3;pos>=0;pos--)
    {  sio_hw->gpio_set = (1u<<pos);
       sleep_ms(200);
       sio_hw->gpio_clr = (1u<<pos);
       sleep_ms(200);
    }
    }
``` 
### **Video**
(video del C2 funcionando)

## Exercise 3 — Fill and empty animation
Create an animation that progressively fills all four LEDs and then progressively empties them.

### What I did

Code:
``` 
sio_hw->gpio_set = 0b0001;
sleep_ms(200);
sio_hw->gpio_set = 0b0010;
sleep_ms(200);
sio_hw->gpio_set = 0b0100;
sleep_ms(200);
sio_hw->gpio_set = 0b1000;
sleep_ms(200);
 
sio_hw->gpio_clr = 0b1000;
sleep_ms(200);
sio_hw->gpio_clr = 0b0100;
sleep_ms(200);
sio_hw->gpio_clr = 0b0010;
sleep_ms(200);
sio_hw->gpio_clr = 0b0001;
sleep_ms(200);
``` 

### **Video**
(video del C3 funcionando)

## Exercise 4 — Fill from the outside inward
Create an animation that progressively fills all four LEDs and then progressively empties them.

### What I did 

Code:
``` 
todavia no lo hacemos jiji
``` 

### **Video**
(video del C4 funcionando)



