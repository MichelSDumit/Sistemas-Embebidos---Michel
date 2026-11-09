# ADC - Analog-to-Digital Converter

**Goal:** to understand and implement Analog-to-Digital Conversion (ADC) on the RP2350, read analog voltage signals using the Raspberry Pi Pico 2, and explore how sampling, resolution, quantization, and filtering affect measurement accuracy.

## What I Learned

In this practice, I learned how to use the ADC of the RP2350 to read analog signals from different components. We worked with a light sensor, which allowed us to observe changes in light intensity, and a potentiometer, which changed the input voltage as we turned it. We also learned how to convert digital readings into voltage values and interpret the results. Although we were unable to complete the temperature sensor experiment because it burned due to an incorrect connection, and the microphone activity due to lack of time, we gained practical experience understanding how analog inputs work and how the Pico SDK can be used to read them.

## Exercises 

### Components
- Light sensor
- Temperature sensor
- Potentiometer
- Microphone

## Light sensor Circuit 
![Circuito](recursos/imgs/light sersor.png)

### Code
```
#include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/adc.h"

// Configure the ADC channel to use
#define ADC_INPUT 0 // channel 0
#define ADC 3.3f    
#define min 0.0f
#define max 165.0f

int main() {
    stdio_init_all();
    adc_init();
    // Configure the corresponding GPIO pin as an ADC input
    adc_gpio_init(26); // GPIO26 usually maps to ADC0 on the Pico 2
    // Select the channel
    adc_select_input(ADC_INPUT);

    // Optional: adjust the clock divider if you need to limit Fs
    // adc_set_clkdiv(div);

    while (true) {
        uint16_t adc = adc_read(); // 12 bits aligned to 0..4095
        double luz = ((adc) * 100.0f) / (165.0f);
        //float luz = ( ) ;
        printf("%u\n", adc);
        printf("%f\n", luz);
        sleep_ms(500);
    }
```

## Temperature sensor Circuit 

### Code
```
#include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/adc.h"

// Configure the ADC channel to use
#define ADC_INPUT 0 // channel 0
#define ADC      3.3f      

int main() {
    stdio_init_all();
    adc_init();
    // Configure the corresponding GPIO pin as an ADC input
    adc_gpio_init(26); // GPIO26 usually maps to ADC0 on the Pico 2
    // Select the channel
    adc_select_input(ADC_INPUT);

    // Optional: adjust the clock divider if you need to limit Fs
    // adc_set_clkdiv(div);

    while (true) {
        uint16_t adc = adc_read(); // 12 bits aligned to 0..4095
        float v = (adc * ADC) / 4095.0f;
        float temp = (v * 1.0f) / 0.01f;
        printf("%u\n", adc);
        sleep_ms(500);
    }
}
```
## Potentiometer Circuit 

### Code
```
#include <stdio.h>
#include "pico/stdlib.h"
#include "hardware/adc.h"

// Configure the ADC channel to use
#define ADC_INPUT 0 // channel 0
#define ADC 3.3f  

int main() {
    stdio_init_all();
    adc_init();
    // Configure the corresponding GPIO pin as an ADC input
    adc_gpio_init(26); // GPIO26 usually maps to ADC0 on the Pico 2
    // Select the channel
    adc_select_input(ADC_INPUT);

    while (true) {
        uint16_t adc = adc_read(); // 12 bits aligned to 0..4095
        float porcentaje = ((adc - 1.0f) * 180.0f) / (3374.0f - 1.0f);
        //float luz = ( ) ;
        printf("%u\n", adc);
        printf("%.1f\n", porcentaje);
        sleep_ms(500);
    }
}
```

## Microphone Circuit 

### Code
```

```

### What could have gone better
What could have gone better was our time management and the way we organized the activities. We spent more time than expected working on the components, and unfortunately, the temperature sensor burned due to a connection issue. We also could not complete the microphone activity because we ran out of time. We could have improved our process by checking the connections more carefully and organizing our time better so we could complete all four activities.

### Conclusion
This practice allowed us to explore how analog components interact with the RP2350 through ADC. By working with different sensors and a potentiometer, we gained practical experience reading analog signals and observing how voltage changes are converted into digital values. Although we could not complete all four activities, we were able to understand the basic process of analog-to-digital conversion and its applications in embedded systems.