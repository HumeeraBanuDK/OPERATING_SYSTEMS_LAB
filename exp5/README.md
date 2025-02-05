## Program Statement: To illustrate concurrent execution of threads using pthread library.
## Source Code:
```c
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h> // For sleep function

int* array;
int sum = 0;
int array_size;
pthread_mutex_t sum_mutex; // Mutex for synchronizing access to sum

// Function to be executed by the first thread
void* thread_function1(void* arg) {
    for (int i = 0; i < array_size / 2; i++) {
        pthread_mutex_lock(&sum_mutex);
        sum += array[i];
        printf("Thread 1: adding %d, partial sum = %d\n", array[i], sum);
        pthread_mutex_unlock(&sum_mutex);
        usleep(100000); // Adding delay to simulate longer processing (100ms)
    }
    pthread_exit(NULL);
}

// Function to be executed by the second thread
void* thread_function2(void* arg) {
    for (int i = array_size / 2; i < array_size; i++) {
        pthread_mutex_lock(&sum_mutex);
        sum += array[i];
        printf("Thread 2: adding %d, partial sum = %d\n", array[i], sum);
        pthread_mutex_unlock(&sum_mutex);
        usleep(100000); // Adding delay to simulate longer processing (100ms)
    }
    pthread_exit(NULL);
}

int main() {
    pthread_t thread1, thread2;
    pthread_mutex_init(&sum_mutex, NULL); // Initialize the mutex

    // Reading the size of the array from the keyboard
    printf("Enter the size of the array: ");
    scanf("%d", &array_size);

    // Allocating memory for the array
    array = (int*)malloc(array_size * sizeof(int));
    if (array == NULL) {
        perror("Failed to allocate memory");
        return 1;
    }

    // Reading array elements from the keyboard
    printf("Enter %d integers: ", array_size);
    for (int i = 0; i < array_size; i++) {
        scanf("%d", &array[i]);
    }

    // Creating the first thread
    if (pthread_create(&thread1, NULL, thread_function1, NULL) != 0) {
        perror("Failed to create thread 1");
        free(array);
        return 1;
    }

    // Creating the second thread
    if (pthread_create(&thread2, NULL, thread_function2, NULL) != 0) {
        perror("Failed to create thread 2");
        free(array);
        return 1;
    }

    // Waiting for both threads to finish
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("The total sum of the array elements is: %d\n", sum);

    // Freeing allocated memory and destroying the mutex
    free(array);
    pthread_mutex_destroy(&sum_mutex);

    return 0;
}
```
## Program Interpretation

# Concurrent Execution of Threads Using Pthreads

## **Program Overview**
This program demonstrates concurrent execution in C using the `pthread` library. It allows the user to input an array of integers and computes the sum of the array using two threads. The program also employs mutex locks to handle synchronization and prevent race conditions.

---

## **Step-by-Step Interpretation**

### **1. Header Files and Global Variables**
- **`#include <pthread.h>`**: Provides functions for creating and managing threads.
- **`#include <stdlib.h>`** and **`#include <stdio.h>`**: Used for memory allocation and standard I/O operations.
- **Global Variables**:
  - `int *array;` → Dynamically allocated array for user inputs.
  - `int sum = 0;` → Shared variable to store the cumulative sum.
  - `int array_size;` → Holds the size of the array.
  - `pthread_mutex_t sum_mutex;` → A mutex to synchronize access to the shared variable `sum`.

---

### **2. Thread Functions**
- **`thread_function1` and `thread_function2`** are responsible for summing the first and second halves of the array, respectively.
- **Key Operations in Each Thread:**
  - **`pthread_mutex_lock(&sum_mutex);`** → Locks the shared resource to prevent race conditions.
  - **`sum += array[i];`** → Adds the current array element to the sum.
  - **`pthread_mutex_unlock(&sum_mutex);`** → Unlocks the mutex, allowing the other thread to access the sum.
  - **`usleep(100000);`** → Introduces a 100 ms delay to simulate longer processing times, making concurrent execution more observable.

---

### **3. Main Function Execution**

#### **Step 1: Input and Memory Allocation**
- Prompts the user to enter the size of the array.
- Dynamically allocates memory for the array using `malloc()`.
- Reads the array elements from the user.

#### **Step 2: Thread Creation**
- **`pthread_create(&thread1, NULL, thread_function1, NULL);`** → Creates the first thread.
- **`pthread_create(&thread2, NULL, thread_function2, NULL);`** → Creates the second thread.
- Each thread works concurrently to process its half of the array.

#### **Step 3: Synchronization and Joining Threads**
- **`pthread_join(thread1, NULL);`** and **`pthread_join(thread2, NULL);`** → The main thread waits until both threads finish execution.

#### **Step 4: Displaying Results and Cleanup**
- Prints the final sum of the array.
- Frees dynamically allocated memory and destroys the mutex using `pthread_mutex_destroy()`.

---

## **Key Concepts Demonstrated**

1. **Concurrency:** Both threads execute simultaneously, splitting the workload for efficiency.
2. **Race Condition Prevention:** The use of a mutex ensures that only one thread modifies the shared `sum` at a time.
3. **Thread Synchronization:** The `pthread_join()` function ensures that the main program waits for threads to complete before proceeding.
4. **Dynamic Memory Management:** Efficiently handles arrays of varying sizes based on user input.

---


## OUTPUT:
![output](op.png)

---
## **Conclusion**
This program illustrates the basics of concurrent execution using the `pthread` library in C. By dividing the summation task between two threads, it showcases parallelism. Mutex locks ensure data integrity during concurrent access to shared resources, and the artificial delays make the concurrent behavior visibly evident. This example lays a foundation for understanding multi-threaded programming and synchronization techniques in operating systems.

