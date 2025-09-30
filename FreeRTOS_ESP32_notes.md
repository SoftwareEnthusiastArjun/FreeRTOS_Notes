FreeRTOS ESP32:

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
=========================================================================================================================
# Question: 

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

Let me know if you'd like to run specific tasks on specific cores or get performance insights!

