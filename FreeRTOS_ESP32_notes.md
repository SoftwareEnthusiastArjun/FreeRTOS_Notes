# FreeRTOS for beginners  by Arjun
## 📌 What is FreeRTOS?

**FreeRTOS** is an open-source **real-time operating system (RTOS)** designed specifically for **embedded systems**. It allows microcontrollers to run **multiple tasks seemingly in parallel**, providing a lightweight and efficient way to build more complex applications.

### ✅ Key Concepts:

| Term                                  | Meaning                                                                                                           |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **RTOS (Real-Time Operating System)** | A system where **timing and predictability** are crucial. Tasks must run within defined time constraints.         |
| **Embedded Systems**                  | Devices with a dedicated function, using microcontrollers like AVR, ARM, or ESP32.                                |
| **Preemptive Multitasking**           | Higher-priority tasks can interrupt (preempt) lower-priority tasks. FreeRTOS uses this to manage task scheduling. |

---

### 👨‍🔧 Origin and Usage

* Developed by **Richard Barry** in the early 2000s.
* Freely **available**, **modifiable**, and **redistributable** under the MIT License.
* Supports many architectures: **AVR**, **ARM**, **ESP32**, **x86**, **STM32**, etc.
* Backed by **Amazon Web Services (AWS)** since 2017 as part of Amazon FreeRTOS.

---

## ⚙️ Why Use FreeRTOS?

### Without RTOS:

* All code runs in a **loop**, and timing between functions must be **manually handled** using `delay()` or `millis()`.

### With FreeRTOS:

* Your code is **broken into tasks**, each handling a specific job.
* The **scheduler** manages task execution based on **priority** and **timing**.
* You can build scalable, reliable systems like:

  * Sensor monitoring + data logging + communication
  * Home automation systems
  * Robotics, IoT, and more

---

## 🧵 Is it Real Multitasking?

### On Single-Core Microcontrollers (e.g., AVR, ATmega328):

* **Not true multitasking**, but **time slicing** gives the illusion.
* FreeRTOS switches between tasks very fast (every few milliseconds).

### On Multi-Core Microcontrollers (e.g., ESP32):

* **True parallelism** is possible.
* You can assign tasks to **specific cores**, making multitasking real.

---

## 🔄 How Does FreeRTOS Work?

* The **Scheduler** decides which task runs.
* Tasks can be in different **states**: Ready, Running, Blocked, Suspended.
* You define tasks using `xTaskCreate()` and let FreeRTOS handle the switching.

---

## 📦 FreeRTOS on Different Boards

| Platform                     | Library / Integration                                        |
| ---------------------------- | ------------------------------------------------------------ |
| **ESP32 (Arduino)**          | FreeRTOS is **built-in**. No need to install anything extra. |
| **AVR (UNO, Nano, etc.)**    | Install **`Arduino_FreeRTOS_Library`** via Library Manager.  |
| **SAMD21/SAMD51/STM32/etc.** | Require specific FreeRTOS implementations and setup.         |

---

## 🧾 Final Summary

### 🔑 FreeRTOS gives you:

* Real-time, preemptive multitasking
* Clean separation of program logic into tasks
* Built-in support for synchronization (mutexes, semaphores)
* Time-based features (delays, timeouts)

### 🔑 Stack monitoring helps:

* Avoid crashes due to memory overflow
* Save RAM by trimming oversized tasks
* Build reliable systems that run for months/years

---

## 📚 Recommended Next Steps

* Explore the FreeRTOS API reference:
  [https://www.freertos.org/a00106.html](https://www.freertos.org/a00106.html)
* Learn about:

  * `vTaskDelay()`, `xTaskCreatePinnedToCore()`, `vTaskDelete()`
  * Semaphores, Queues, and Task Notifications
* Try writing FreeRTOS tasks for:

  * Sensors (temperature, humidity)
  * Display updates
  * WiFi communication
  * Data logging

---
===========================================================================================================================
# FreeRTOS ESP32 basic code structure and explanation:

```cpp

#define LED1 25
#define LED2 26
#define LED3 17

void setup() {
    pinMode(LED3, OUTPUT);
  
    xTaskCreate(
        blink1,      // Function name of the task
        "Blink 1",   // Name of the task (e.g. for debugging)
        2048,        // Stack size (bytes)
        NULL,        // Parameter to pass
        1,           // Task priority
        NULL         // Task handle
    );

    xTaskCreate(
        blink2,     // Function name of the task
        "Blink 2",  // Name of the task (e.g. for debugging)
        2048,       // Stack size (bytes)
        NULL,       // Parameter to pass
        1,          // Task priority
        NULL        // Task handle
    );
}

void blink1(void *parameter) {
    pinMode(LED1, OUTPUT);
    while(1){
        digitalWrite(LED1, HIGH);
        delay(500); // Delay for Tasks 
        digitalWrite(LED1, LOW);
        delay(500);
    }
}

void blink2(void *parameter) {
    pinMode(LED2, OUTPUT);
    while(1) {
        digitalWrite(LED2, HIGH);
        delay(333);
        digitalWrite(LED2, LOW);
        delay(333);
    }
}

void loop(){
    digitalWrite(LED3, HIGH);
    delay(1111);
    digitalWrite(LED3, LOW);
    delay(1111);
}
```

---
### Code Explanation:

### 🟩 **Constants (Pin Definitions)**

```cpp
#define LED1 25
#define LED2 26
#define LED3 17
```

* These lines define **constants** using the C preprocessor `#define`.
* `LED1`, `LED2`, and `LED3` are names for digital GPIO pins.
* For example, `LED1` is connected to GPIO pin 25.
* This makes your code more readable—you can refer to `LED1` instead of remembering it's pin `25`.

---

### 🟩 **setup() function**

```cpp
void setup() {
```

* `setup()` is a standard Arduino function.
* It runs **once at the start** when the board powers on or resets.

---

```cpp
    pinMode(LED3, OUTPUT);
```

* This sets `LED3` (pin 17) as an **output** pin, so we can turn the LED on/off.

---

```cpp
    xTaskCreate(
        blink1,      // Function name of the task
        "Blink 1",   // Name of the task (e.g. for debugging)
        2048,        // Stack size (bytes)
        NULL,        // Parameter to pass
        1,           // Task priority
        NULL         // Task handle
    );
```

* This **creates a new FreeRTOS task**.
* The task runs the function `blink1()` in parallel with other tasks.
* `"Blink 1"` is just a name for the task.
* `2048` is the stack size in bytes.
* `NULL` means no parameter is passed to the task.
* `1` is the priority (higher number = higher priority).
* Last `NULL` means we don't keep the task handle.

---

```cpp
    xTaskCreate(
        blink2,     // Function name of the task
        "Blink 2",  // Name of the task (e.g. for debugging)
        2048,       // Stack size (bytes)
        NULL,       // Parameter to pass
        1,          // Task priority
        NULL        // Task handle
    );
```

* Same as above, but this creates a second task that runs the `blink2()` function.

---

```cpp
}
```

* End of the `setup()` function.

---

### 🟩 **Task Function: blink1**

```cpp
void blink1(void *parameter) {
```

* This is a task function.
* It takes one argument (`parameter`) because FreeRTOS requires it, even if unused.

---

```cpp
    pinMode(LED1, OUTPUT);
```

* Sets `LED1` (pin 25) as an output.

---

```cpp
    while(1){
```

* Infinite loop: the task runs forever.
* FreeRTOS tasks must **not return**, so we loop forever.

---

```cpp
        digitalWrite(LED1, HIGH);
        delay(500); // Delay for Tasks 
        digitalWrite(LED1, LOW);
        delay(500);
```

* Turns on the LED at pin 25 (`HIGH`), waits 500 milliseconds.
* Then turns it off (`LOW`), and waits another 500 ms.
* This makes LED1 **blink every second** (on for 0.5s, off for 0.5s).

---

```cpp
    }
}
```

* End of the loop and task function.

---

### 🟩 **Task Function: blink2**

```cpp
void blink2(void *parameter) {
    pinMode(LED2, OUTPUT);
    while(1) {
        digitalWrite(LED2, HIGH);
        delay(333);
        digitalWrite(LED2, LOW);
        delay(333);
    }
}
```

* Similar to `blink1()`, but for `LED2` (pin 26).
* The LED blinks faster: it’s on and off every 333 milliseconds.
* So it blinks approximately **1.5 times per second**.

---

### 🟩 **loop() function**

```cpp
void loop(){
```

* This is the default Arduino loop.
* It runs in its **own FreeRTOS task** on platforms like the ESP32.

---

```cpp
    digitalWrite(LED3, HIGH);
    delay(1111);
    digitalWrite(LED3, LOW);
    delay(1111);
}
```

* Controls `LED3` (pin 17).
* It stays on for **1111 ms**, then off for 1111 ms.
* This makes LED3 **blink approximately every 2.2 seconds**.

---

### 🟩 Summary of What the Code Does

| LED  | GPIO | Task       | Blink Interval         |
| ---- | ---- | ---------- | ---------------------- |
| LED1 | 25   | `blink1()` | 500ms on / 500ms off   |
| LED2 | 26   | `blink2()` | 333ms on / 333ms off   |
| LED3 | 17   | `loop()`   | 1111ms on / 1111ms off |

---
===========================================================================================================================
# Question: Will this taks run on same or different cores, also if freeRTOS is not used then the common loop will run in which core?

Great question! Let's talk about **ESP32 cores**, **FreeRTOS task scheduling**, and what happens when **FreeRTOS is not explicitly used**.

---

## 🧠 ESP32 Architecture: Dual-Core

The **ESP32** has **two cores**:

* **Core 0**: Often referred to as the **PRO_CPU**
* **Core 1**: Often referred to as the **APP_CPU**

These cores are symmetric, meaning they can both run code. But the **Arduino framework** (which uses FreeRTOS internally) makes certain decisions for you.

---

## ✅ When FreeRTOS is Used (Like in Your Code)

You're explicitly creating two tasks with `xTaskCreate()` in your `setup()`:

```cpp
xTaskCreate(blink1, ...);
xTaskCreate(blink2, ...);
```

### So, do these run on the same or different cores?

* By default, **`xTaskCreate()` does not pin a task to a core**.
* Therefore, **FreeRTOS decides** which core to schedule each task on, depending on current load.
* This means:

  * Both tasks **can run on the same core**, or
  * They **can be distributed across Core 0 and Core 1**

🟡 **If you want to control which core a task runs on**, use `xTaskCreatePinnedToCore()` instead, like this:

```cpp
xTaskCreatePinnedToCore(blink1, "Blink1", 2048, NULL, 1, NULL, 0); // Pin to Core 0
xTaskCreatePinnedToCore(blink2, "Blink2", 2048, NULL, 1, NULL, 1); // Pin to Core 1
```

---

## 🌀 What about the `loop()` function?

* In the Arduino core for ESP32:

  * `setup()` and `loop()` run in a **special task** created by the Arduino runtime.
  * This task is usually pinned to **Core 1** (`APP_CPU`).
* So, unless you change it, your `loop()` code will always run on **Core 1**.

---

## ❌ When FreeRTOS Is *Not* Used

> (This is more hypothetical for ESP32, because FreeRTOS is always used under the hood.)

Even if you're not **manually** using FreeRTOS (i.e., not calling `xTaskCreate()`), the ESP32 Arduino core **still runs everything inside FreeRTOS tasks**.

So even if you just wrote:

```cpp
void setup() {
  pinMode(25, OUTPUT);
}

void loop() {
  digitalWrite(25, HIGH);
  delay(1000);
  digitalWrite(25, LOW);
  delay(1000);
}
```

Behind the scenes:

* `setup()` and `loop()` run as a FreeRTOS task.
* This Arduino main task is **pinned to Core 1 by default**.

---

## 🔍 Summary

| Code / Function                 | FreeRTOS Used?        | Core Used (Default)         |
| ------------------------------- | --------------------- | --------------------------- |
| `loop()`                        | ✅ Yes                 | Core 1 (APP_CPU)            |
| `xTaskCreate()`                 | ✅ Yes                 | Any core (FreeRTOS decides) |
| `xTaskCreatePinnedToCore()`     | ✅ Yes                 | You specify (0 or 1)        |
| Arduino without manual FreeRTOS | ✅ Yes (behind scenes) | Core 1                      |

---
===========================================================================================================================
# **ticks**, **vTaskDelay()**, and **delay()** in FreeRTOS and ESP32.

---

## 🕒 What is a Tick?

* A **tick** is a **tiny unit of time** used by FreeRTOS to keep track of when to switch between tasks.
* Think of it like a **clock that ticks every 1 millisecond** (on ESP32).
* So, 1 tick = **1 ms** (on ESP32).
* Every time the tick happens, FreeRTOS checks:
  “Should I let another task run now?”

---

## ⚙️ ESP32 Tick Settings

* Tick frequency = **1000 Hz** → means **1000 ticks per second** → each tick = **1 ms**.
* On **AVR Arduinos** (like Uno or Nano), tick frequency = **62 Hz**, so:

  * Each tick = ~16 milliseconds.

You can print these values like this:

```cpp
Serial.println(portTICK_PERIOD_MS);   // Shows how long one tick is (in ms)
Serial.println(configTICK_RATE_HZ);   // Shows how many ticks happen per second
```

---

## ⏳ delay() vs. vTaskDelay()

### 🟩 `delay(ms)`

* Pauses the current task for a number of **milliseconds**.
* Simple and familiar to most Arduino users.
* Example: `delay(500)` waits for **500 ms**.

### 🟩 `vTaskDelay(ticks)`

* Pauses the current task for a number of **ticks**.
* More precise in FreeRTOS, and better practice when using multiple tasks.
* On **ESP32**, since 1 tick = 1 ms, it’s basically the same as `delay()`.

But on **AVR Arduinos**, 1 tick ≈ 16 ms. So `vTaskDelay(500)` would wait **8000 ms** (way too long!).

---

### ✅ To write code that works correctly on any board, use:

```cpp
vTaskDelay(500 / portTICK_PERIOD_MS);  // Always results in 500 ms delay
```

* On ESP32: `portTICK_PERIOD_MS = 1` → `vTaskDelay(500)`
* On AVR: `portTICK_PERIOD_MS = 16` → `vTaskDelay(31)`

---

## 🎯 Bottom Line

| Function                               | Unit Used                     | Works in FreeRTOS? | Good for Multitasking? |
| -------------------------------------- | ----------------------------- | ------------------ | ---------------------- |
| `delay(500)`                           | Milliseconds                  | Yes (on ESP32)     | ❌ Not ideal            |
| `vTaskDelay(500)`                      | Ticks                         | ✅ Yes              | ✅ Recommended          |
| `vTaskDelay(500 / portTICK_PERIOD_MS)` | Milliseconds (cross-platform) | ✅ Yes              | ✅ Best Practice        |

---

## 📌 Summary in One Sentence:

> A **tick** is FreeRTOS's way of counting time (usually 1 ms on ESP32), and `vTaskDelay()` uses **ticks** to pause tasks, making it better for multitasking than the regular `delay()`.

---
===========================================================================================================================
# Determine the memory requirement of a FreeRTOS task

1. **Why we care about task memory (stack) in FreeRTOS**
2. **What `uxTaskGetStackHighWaterMark()` does**
3. **A simple, practical example**
4. **Step-by-step explanation of the code**
5. **Key takeaways / tips**

---

# 📘 Notes: Understanding Stack Usage in FreeRTOS using `uxTaskGetStackHighWaterMark()`

---

## 🧠 1. Why Monitor Stack Memory in FreeRTOS?

In **FreeRTOS**, each task has its **own stack** – a dedicated region of memory used for:

* Local variables
* Function calls and returns
* Temporary calculations

### ✅ Why is this important?

* **Too little stack** → stack overflow → unpredictable crashes or reboot.
* **Too much stack** → wasted RAM → less memory for other tasks or buffers.

Since FreeRTOS doesn't automatically detect overflows in all cases, it’s **your job to monitor** and optimize stack sizes.

---

## 🔍 2. What Does `uxTaskGetStackHighWaterMark()` Do?

This FreeRTOS API function **returns the minimum amount of stack space** that has remained **unused** since the task started running.

### ➕ In other words:

* It tells you **how close you’ve come to using up the task’s stack**.
* You use this value to **fine-tune** each task’s stack size.

### 📌 Syntax:

```cpp
UBaseType_t uxTaskGetStackHighWaterMark(TaskHandle_t xTask);
```

* Returns result in **"words"** (on ESP32, 1 word = 4 bytes)
* A **larger value** means **more free stack** → safe
* A **small value** (like <100) → stack is almost full → increase size!

---

## ✅ 3. Example Code (ESP32 - Arduino IDE)

Here’s a **simple sketch** to monitor stack usage of a blinking task.

```cpp
#include <Arduino.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

#define LED 2  // Onboard LED pin (built-in on many ESP32 boards)

TaskHandle_t blinkTaskHandle;  // Task handle to monitor the blink task

void setup() {
  Serial.begin(115200);
  pinMode(LED, OUTPUT);

  // Create Blink Task with 1000-word stack
  xTaskCreate(
    blinkTask,           // Function name
    "BlinkTask",         // Task name (for debugging)
    1000,                // Stack size in words (4 bytes each)
    NULL,                // No parameters
    1,                   // Priority
    &blinkTaskHandle     // Handle for monitoring
  );

  // Create Monitor Task
  xTaskCreate(
    monitorTask,
    "MonitorTask",
    1000,
    NULL,
    1,
    NULL                // No need to monitor the monitor task itself
  );
}

void blinkTask(void *pvParameters) {
  while (1) {
    digitalWrite(LED, HIGH);
    delay(500);
    digitalWrite(LED, LOW);
    delay(500);
  }
}

void monitorTask(void *pvParameters) {
  while (1) {
    delay(3000); // Check every 3 seconds
    UBaseType_t watermark = uxTaskGetStackHighWaterMark(blinkTaskHandle);
    Serial.print("BlinkTask High Watermark: ");
    Serial.print(watermark);
    Serial.println(" words free");
  }
}

void loop() {
  // Nothing needed here when using FreeRTOS tasks
}
```

---

## 🧾 4. Step-by-Step Explanation

| Line                                            | Purpose                                                                     |
| ----------------------------------------------- | --------------------------------------------------------------------------- |
| `TaskHandle_t blinkTaskHandle;`                 | Creates a handle so we can track the blink task.                            |
| `xTaskCreate(...)`                              | Creates a FreeRTOS task with defined stack size, name, and priority.        |
| `blinkTask()`                                   | Blinks LED in a loop. Uses small stack.                                     |
| `monitorTask()`                                 | Runs independently and checks how much unused stack remains in `blinkTask`. |
| `uxTaskGetStackHighWaterMark(blinkTaskHandle);` | This is the key function to monitor unused stack.                           |

---

## 📊 5. Sample Serial Output

```
BlinkTask High Watermark: 748 words free
BlinkTask High Watermark: 748 words free
```

➡️ This means:

* 1000 - 748 = **252 words used**
* That's about 1008 bytes (on ESP32: 252 × 4)
* You have a **safe margin**. You might reduce the stack a bit to save RAM.

---

## ✅ 6. Key Takeaways

| Concept                         | Explanation                                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Stack size in `xTaskCreate()`   | Always in **words**, not bytes. (ESP32: 1 word = 4 bytes)                                                         |
| `uxTaskGetStackHighWaterMark()` | Tells you **minimum unused stack**                                                                                |
| Use a separate task to monitor  | If you measure inside the same task, the result is less accurate (because calling the function uses stack itself) |
| Optimize stack                  | After testing, reduce stack sizes where you see large watermarks (like >700 words unused)                         |

---

## 🔐 7. Safety Tips

* Never go below ~100 words free, even if the task seems stable.
* Avoid recursive functions in tasks — they can quickly consume stack.
* Don’t forget ISR functions don’t use task stacks — they use the **interrupt stack**.

---

## 💡 Optional Extension: Print Task Name Dynamically

You can also print the task name using:

```cpp
Serial.print(pcTaskGetName(blinkTaskHandle));
```

Which gives:

```
BlinkTask
```

---

## 🗂️ Summary

**`uxTaskGetStackHighWaterMark()`** is your best friend in tuning FreeRTOS task memory on resource-constrained devices like the ESP32. It helps you avoid both:

* Crashes due to low stack
* Waste due to over-allocation

---
===========================================================================================================================





