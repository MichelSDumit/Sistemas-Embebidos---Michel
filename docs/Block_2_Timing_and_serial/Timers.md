# Timers

**Goal:** to understand and implement timer-based events in the RP2350, analyze timing characteristics, configure hardware alarms and interrupts, and use absolute deadlines to achieve precise and reliable periodic events.


## What I Learned

In this practice, I learned how to use repeating timers in the RP2350 and how to measure their behavior using an oscilloscope. I also learned how period, frequency, duty cycle, latency, jitter, and drift affect a waveform. Finally, I understood how hardware alarms and interrupts work, how to configure them using registers, and why cumulative deadlines help maintain accurate periodic events.

## Part A - Predict first, measure second

In this activity, we programmed the RP2350 to toggle GPIO pin GP15 every 250 ms using a repeating timer from the Pico SDK. Before running the program, we calculated the expected timing characteristics of the signal, including the HIGH and LOW times, period, frequency, and duty cycle. Then, we used an oscilloscope to observe and measure the actual waveform, comparing the results with our theoretical calculations. As evidence, we recorded our measurements in a comparison table and captured videos of the oscilloscope displaying the generated signal.

| **Quantity**    | **Predicted** | **Measured** | **Difference** |
|-----------------|---------------|--------------|----------------|
| Toggle interval | 250 ms        | 249.8 ms     | 0.2 ms         |
| HIGH time       | 250 ms        | 249.8 ms     | 0.2 ms         |
| LOW time        | 250 ms        | 249.8 ms     | 0.2 ms         |
| Period          | 500 ms        | 499.8 ms     | 0.2 ms         |
| Frequency       | 2 Hz          | 2 Hz         | -              |
| Duty cycle      | 50%           | 50%          | -              |

### **Video**

<iframe width="560" height="315" src="https://www.youtube.com/embed/-eu8608X8I8?si=BpqEdDHRNpw9oyGp" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Part B -  Change only one variable

In this activity, we changed the `TOGGLE_MS` value to 100 ms, 50 ms, and 10 ms while keeping the rest of the program unchanged. Before each test, we calculated the expected frequency and waveform period, then used the oscilloscope to measure and compare the results. This allowed us to observe how changing the timer interval affects the signal frequency, HIGH and LOW times, and how the duty cycle remains approximately 50%. We also compared the usefulness of an LED and an oscilloscope when observing faster signals.

1. Does TOGGLE_MS represent the waveform period?
a. No, it represents the time between every status update
2. What mathematical relationship connects TOGGLE_MS and the measured signal frequency?
a. T = 2 × TOGGLE_MS and f = T/1
3. Does changing TOGGLE_MS change the duty cycle? Why?
a. No, it's still 50% because the time in Low and High is the same.
4. At what point does observing the signal with an LED become less useful than observing it with an oscilloscope?
a. When the signal changes too fast for us to notice the LED blinking.

## Part C -  Can the callback duration change the timing?

In this activity, we modified the repeating timer callback to measure how much time it spends executing. We used two GPIO pins: GP15 to display the timed output and GP14 to mark the beginning and end of the callback, allowing us to observe its execution time through the oscilloscope. We performed four experiments by changing the callback workload and switching between negative and positive timer delays. By comparing the waveforms on both channels, we observed how callback execution time affects the intervals between events and understood the importance of keeping callbacks and interrupts short.

1. What does the width of the CH2 pulse represent?
a. It represents the time that takes to execute the callback.
2. What changes when the repeating-timer delay changes from negative to positive?
a. With negative (-100 ms), the next callback is scheduled based on the start of the callback. With positive (+100 ms), the 100 ms start counting after the callback ends.
3. Why should callbacks and ISRs normally be short?
a. Because while they run, they use the CPU and can delay other tasks or events.
4. What do you expect if the callback takes longer than the requested interval?
a. The callback can take almost all the available time and events might get delayed or not run exactly at the requested interval.
5. Does a longer callback necessarily change the HIGH/LOW duty cycle of CH1? Explain from the measured edges rather than from the source code alone.
a. Not necessarily, you have to check it on the oscilloscope. If the HIGH and LOW changes still have roughly the same duration, the duty cycle will stay around 50%. The important thing is to measure the edges of CH1, not just assume it from the code.

## Part D — Make main() visibly do something else

In this activity, we kept the repeating timer running on GP15 while using the main() function to perform an independent task, such as detecting push button presses. We made sure that the timer callback did not handle the button, allowing both tasks to run independently. Using the oscilloscope, we verified that interacting with the button did not significantly affect the timing of the output waveform. This helped us understand how hardware timers keep track of time independently of the main program, allowing the CPU to perform other tasks without interrupting periodic events.

## What could have gone better

Although all the experiments worked correctly, we could improve our work by performing more measurements to obtain more accurate results and comparing them with the theoretical values. We could also test different timer intervals and callback workloads to better understand the limits of the RP2350 timer. Additionally, we could improve our documentation by explaining the small variations observed in the oscilloscope and analyzing how latency and jitter affect signal accuracy.

## Conclusion

In conclusion, this practice helped us understand how timers work in the RP2350 and how they can generate periodic events without stopping the main program. Through the different experiments, we learned how to configure repeating timers, measure waveforms using an oscilloscope, and analyze characteristics such as period, frequency, duty cycle, latency, and jitter. We also explored how callback execution time and timer delays affect the generated signals, as well as how hardware alarms and interrupts work. Overall, we gained a better understanding of how hardware and software work together to manage time efficiently, allowing the microcontroller to perform multiple tasks while maintaining accurate timing.




