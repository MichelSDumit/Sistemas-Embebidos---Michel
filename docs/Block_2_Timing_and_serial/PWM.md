# PWM - Pulse Width Modulation

**Goal:** to understand and implement Pulse Width Modulation (PWM) on the RP2350, control signal frequency and duty cycle, configure PWM slices and channels, and analyze how TOP and clock dividers affect signal resolution and output behavior.


## What I Learned

In this practice, I learned how to use PWM to generate different sound frequencies and create musical notes using the RP2350. I also learned how changing the frequency and duty cycle affects the output signal and how PWM can be configured through the Pico SDK. Finally, I understood how a buzzer can be controlled to reproduce different tones, allowing us to build a simple electronic piano.

## code

```
#include "pico/stdlib.h"
#include "hardware/clocks.h"
#include "hardware/pwm.h"
 
#define LED_PIN 15
#define dO  1046.50f
#define re  1174.66f
#define mi  1318.51f
#define fa  1479.98f
#define sol 1567.98f
#define la  1760.00f
#define si  1975.53f
#define Do2  2093.00f
 
#define B1 0
#define B2 1
#define B3 2
#define B4 3
#define B5 4
#define B6 5
#define B7 6
#define B8 7
#define B9 8
#define B10 9
 
#define VOLUME_UP_PIN 16
#define VOLUME_DOWN_PIN 17
 
#define TOP 4095
#define d0 100
#define d1 125
#define d2 200
#define d3 350
#define s 100
#define s1 125
#define s3 500
 
int main() {
    stdio_init_all();
 
    gpio_set_function(LED_PIN, GPIO_FUNC_PWM);
    uint slice = pwm_gpio_to_slice_num(LED_PIN);
    uint chan  = pwm_gpio_to_channel(LED_PIN);
 
    pwm_set_wrap(slice, TOP);
    pwm_set_chan_level(slice, chan, 0);
    pwm_set_enabled(slice, true);
 
    const uint note_buttons[10] = {B1, B2, B3, B4, B5, B6, B7, B8, B9, B10};
    const float note_hz[10] = {
        1046.50f, 1174.66f, 1318.51f, 1479.98f, 1567.98f, 1760.00f, 1975.53f, 2093.00f
    };
 
    for (uint i = 0; i < 10; ++i) {
        gpio_init(note_buttons[i]);
        gpio_set_dir(note_buttons[i], GPIO_IN);
        gpio_pull_up(note_buttons[i]);
    }
 
    // Botones de volumen (entrada con pull-up)
    gpio_init(VOLUME_UP_PIN);   gpio_set_dir(VOLUME_UP_PIN, GPIO_IN);   gpio_pull_up(VOLUME_UP_PIN);
    gpio_init(VOLUME_DOWN_PIN); gpio_set_dir(VOLUME_DOWN_PIN, GPIO_IN); gpio_pull_up(VOLUME_DOWN_PIN);
 
    const uint32_t f_clk = clock_get_hz(clk_sys);
    uint volume = 60;
 
    while (true) {
        // Volumen: sube o baja de 10 en 10 (el sleep sirve de antirrebote)
        if (!gpio_get(VOLUME_UP_PIN) && volume < 100) {
            volume += 10;
            sleep_ms(200);
        }
        if (!gpio_get(VOLUME_DOWN_PIN) && volume > 0) {
            volume -= 10;
            sleep_ms(200);
        }
 
        int selected_note = -1;
        for (uint i = 0; i < 10; ++i) {
            if (!gpio_get(note_buttons[i])) {
                selected_note = (int)i;
                break; // Si se mantienen varias teclas, tiene prioridad la de GPIO más bajo.
            }
        }
 
        if (selected_note >= 0) {
            float divider = (float)f_clk / (note_hz[selected_note] * (TOP + 1));
            uint16_t level = (uint16_t)(((TOP + 1) / 2) * volume / 100);
            pwm_set_chan_level(slice, chan, 0);
            pwm_set_clkdiv(slice, divider);
            pwm_set_chan_level(slice, chan, level);
        } else {
            pwm_set_chan_level(slice, chan, 0);
        }
 
        sleep_ms(5);
    }
}
```

### **Video**

<iframe width="560" height="315" src="https://www.youtube.com/embed/SCXRfL5FgE4?si=-cqBUJHs0xxtVU7N" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


### What could have gone better
It took us longer than expected because we struggled to understand how to program it correctly and how to set the appropriate frequencies for each musical note. We could have improved our process by spending more time understanding the code and testing different frequencies before putting everything together.

### Conclusion
this practice helped us understand how PWM works and how it can be used to generate different sound frequencies to create musical notes. By developing our electronic piano, we learned how to configure PWM using the Pico SDK and how frequency and duty cycle affect the output signal. Although programming the piano and finding the correct frequencies was challenging and took us longer than expected, we were able to apply the concepts we learned and understand how PWM can be used in practical applications. Overall, this activity helped us improve our programming skills and gain a better understanding of the RP2350's PWM capabilities.
