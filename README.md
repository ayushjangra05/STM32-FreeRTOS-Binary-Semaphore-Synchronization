# STM32 FreeRTOS Binary Semaphore Synchronization

A FreeRTOS-based embedded systems project implemented on the STM32F446RE Nucleo Board to demonstrate external interrupt handling and task synchronization using binary semaphores.

---

## Overview

This project demonstrates Deferred Interrupt Processing using FreeRTOS and CMSIS-RTOS V2.

The application uses:

- External interrupt (EXTI) from the user push button
- Binary semaphore synchronization
- RTOS task scheduling
- SWV ITM debugging

When the user button is pressed, an interrupt is generated which releases a binary semaphore.  
The LED control task acquires the semaphore and blinks the onboard LED five times.

---

## Features

- External interrupt handling using EXTI
- Binary semaphore synchronization
- Deferred interrupt processing
- FreeRTOS task scheduling
- LED control task
- SWV ITM trace debugging
- CMSIS-RTOS V2 implementation

---

## Hardware Requirements

- STM32 Nucleo-F446RE Development Board
- USB Type-A to Mini-B Cable

---

## Software Requirements

- STM32CubeIDE
- STM32CubeMX
- FreeRTOS Middleware

---

## Peripheral Configuration

| Peripheral | Configuration |
|------------|----------------|
| PA5 | GPIO Output (Onboard LED) |
| PC13 | External Interrupt Input |
| EXTI Line | EXTI15_10 |
| SYS Debug | Trace Asynchronous SW |
| Timebase Source | TIM6 |
| RTOS Interface | CMSIS_V2 |

---

## RTOS Components

| Component | Purpose |
|------------|----------|
| LED_Control Task | Controls LED blinking |
| Binary Semaphore | Synchronization between ISR and task |
| EXTI Interrupt | Detects button press event |

---

## Working Principle

### Application Flow

1. User presses the push button
2. EXTI interrupt is triggered
3. ISR releases the binary semaphore
4. LED_Control task acquires the semaphore
5. LED blinks five times
6. Task blocks again waiting for next interrupt

This demonstrates synchronization between hardware interrupts and RTOS tasks.

---

## Binary Semaphore Logic

The semaphore is initialized with count `0`.

```c
myBinarySem01Handle =
    osSemaphoreNew(1, 0,
    &myBinarySem01_attributes);
```

- Semaphore unavailable → Task blocked
- Button interrupt occurs → Semaphore released
- Task becomes ready and executes

---

## Source Code

### LED Control Task

```c
void StartDefaultTask(void *argument)
{
    uint8_t i;

    for(;;)
    {
        if(osSemaphoreAcquire(
            myBinarySem01Handle,
            100) == osOK)
        {
            printf("Inside LEDControl Task\n");

            i = 0;

            while(i < 10)
            {
                HAL_GPIO_TogglePin(
                    GPIOA,
                    GPIO_PIN_5);

                HAL_Delay(250);

                i = i + 1;
            }
        }
    }
}
```

---

### External Interrupt Callback

```c
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    osSemaphoreRelease(
        myBinarySem01Handle);
}
```

---

## SWV ITM Trace Debugging

The SWV ITM Data Console is used for:

- Real-time task monitoring
- Viewing task execution logs
- Debugging RTOS behaviour
- Verifying interrupt handling

---

## Build and Run

1. Open the project in STM32CubeIDE
2. Configure GPIO and EXTI interrupt
3. Configure FreeRTOS middleware
4. Add binary semaphore
5. Build the project
6. Connect STM32 board via USB
7. Start debugging mode
8. Enable SWV ITM Data Console
9. Press the user button to trigger LED blinking

---

## Expected Output

- LED remains OFF initially
- Pressing the button triggers LED blinking
- LED blinks five times with 250 ms delay
- SWV console displays:

```text
Inside LEDControl Task
```

- Task blocks again after execution

---

## Learning Outcomes

- Understanding RTOS synchronization
- Binary semaphore usage
- External interrupt handling
- Deferred interrupt processing
- Task blocking and scheduling
- SWV ITM debugging techniques

---

## Future Improvements

- Add multiple synchronized tasks
- Implement counting semaphores
- Add queue-based communication
- Replace HAL_Delay with osDelay
- Integrate sensor-triggered interrupts

---

## Author

**Ayush Jangra**  
ECE Student | Chitkara University
