# Interrupts

**Goal:** to understand how interrupts work on the RP2350, including IRQs, ISRs, GPIO interrupts, priorities, and how they allow the microcontroller to respond to events without constantly checking the inputs.

---

## What I learned

In this practice, I learned how interrupts allow the RP2350 to respond to events without continuously checking the GPIO inputs. I also learned how ISRs, IRQs, and the NVIC work together to handle interrupts, and how GPIO interrupts can be triggered by rising or falling edges. Finally, I understood how to configure GPIO interrupts using the Pico SDK and why it is important to acknowledge the interrupt and keep the ISR as short as possible.

---

## Circuit assembly for the exercises

![Circuito ensamblado](recursos/imgs/b1/circuito3.png)

## Roulette 

### What I did 

Code

```  
 
volatile int pos = 0;
volatile int vel=200;
const uint32_t MASK = (1u<<led1) | (1u<<led2) | (1u<<led3) | (1u<<led4)  | (1u<<led5) ;
 
static void FUNCION_STOP(uint GPIO, uint32_t event){
    if(GPIO == Btn1 && event==GPIO_IRQ_EDGE_RISE ){
        printf("botonok \n");
        if(pos==2){
            printf("ganaste\n");
            for(int i=0;i<3;i++){
                sio_hw->gpio_set = MASK;
                busy_wait_ms(100);
                sio_hw->gpio_clr =MASK;
                busy_wait_ms(100);
            }
        }
    }
     if(GPIO == Btn2 && event == GPIO_IRQ_EDGE_RISE){
        vel -= 30;
        if(vel < 40) vel = 40; // límite para que no quede absurdo
        printf("velocidad: %d\n", vel);
    }
        if(GPIO == Btn3 && event == GPIO_IRQ_EDGE_RISE){
        vel += 30;
        if(vel > 500) vel = 500; // límite para que no quede eterno
        printf("velocidad: %d\n", vel);
    }
    gpio_acknowledge_irq(GPIO, event);
}

   int pause=0;
   int counter =0;
   int dir = 1;       // Dirección: 1→derecha, -1→izquierda
   //FUNCIO NDE INTERR
   gpio_set_irq_enabled_with_callback(Btn1,GPIO_IRQ_EDGE_RISE,true,&FUNCION_STOP);
   gpio_set_irq_enabled(Btn2, GPIO_IRQ_EDGE_RISE, true);
   gpio_set_irq_enabled(Btn3, GPIO_IRQ_EDGE_RISE, true);
   
   while (true) {
        for(pos=0;pos<=4;pos++)
      { sio_hw->gpio_set = (1u<<pos+9);
       sleep_ms(vel);
       sio_hw->gpio_clr = (1u<<pos+9);
       sleep_ms(vel);}
    for(pos=4;pos>=0;pos--)
      { sio_hw->gpio_set = (1u<<pos+9);
       sleep_ms(vel);
       sio_hw->gpio_clr = (1u<<pos+9);
       sleep_ms(vel);}
   }
}

```  

### **Video**

<iframe width="560" height="315" src="https://www.youtube.com/embed/HhzLsmht66k?si=Mi76epTDMk2EnSfF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### What could have gone better

### Conclusion
