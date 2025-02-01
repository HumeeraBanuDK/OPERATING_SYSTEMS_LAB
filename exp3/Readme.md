> # Program Ststement
> ## To stimulate CPU Scheduling Algorithms like FCFS, SJF, RR and Priority using C
>
> # Program Description
> # CPU Scheduling Algorithms: FCFS, SJF, RR, and Priority
>
> This document explains the core concepts of First-Come, First-Served (FCFS), Shortest Job First (SJF), Round Robin (RR), and Priority > > CPU scheduling algorithms.
> 
> ## 1. First-Come, First-Served (FCFS)
>
> * **Core Idea:** Processes are executed in the exact order they arrive in the ready queue (like a line at a ticket
>  counter). The first process that requests the CPU gets it, and it runs to completion (or until it blocks for I/O). Only > > then does the next process in the queue get a chance.
>
> * **Analogy:** Imagine a single line at a bank. Customers are served in the order they joined the line.
> 
> * **Example:** If processes P1, P2, and P3 arrive in that order, P1 will run first, then P2, then P3.
>
> * **Key Characteristics:**
>     * Simple to understand and implement.
>     * Non-preemptive: Once a process starts, it runs until it finishes or blocks.
>     * Can lead to long waiting times for short processes if a long process arrives first (the "convoy effect").
>
> ## 2. Shortest Job First (SJF)
> 
> * **Core Idea:** The process with the *shortest* burst time (the estimated time it needs to run on the CPU) is executed
>   next. This aims to minimize average waiting time.
> 
> * **Analogy:** Imagine a doctor's office where patients are seen based on the severity of their condition (shortest  treatment time first).
> 
> * **Example:** If P1 needs 10ms, P2 needs 2ms, and P3 needs 5ms, SJF would run P2 first, then P3, then P1.
> 
> * **Key Characteristics:**
>     * Optimal (non-preemptive): Minimizes average waiting time among non-preemptive algorithms.
>     * Non-preemptive (usually): Once a process starts, it runs to completion.
>     * Requires knowing burst times in advance: This is a major drawback because burst times are often not known precisely. We usually have to estimate them.
>     * Can lead to starvation: Long processes might never get a chance to run if short processes keep arriving.
> 
> ## 3. Round Robin (RR)
> 
> * **Core Idea:** Each process gets a small, fixed amount of CPU time called a "time quantum" or "time slice." If a process doesn't finish within its quantum, it's preempted (temporarily paused) and put back at the end of the ready queue. The CPU then goes to the next process in the queue.
> 
> * **Analogy:** Imagine a group of people taking turns speaking, with each person getting a fixed amount of time to talk before it's someone else's turn.
> 
> * **Example:** If the quantum is 4ms, and P1 needs 10ms, it will run for 4ms, then be preempted. Other processes will get their turn. P1 will then get another 4ms slice when its turn comes around again, and so on.
> 
> * **Key Characteristics:**
>     * Time-sharing: Gives the illusion of concurrency by rapidly switching between processes.
>    * Preemptive: Processes are preempted after their time quantum expires.
>   * Fair: Each process gets a fair share of CPU time.
>    * Good for interactive systems: Provides reasonable response times.
>    * Performance depends on quantum size: A small quantum leads to more context switching overhead, while a large quantum makes RR behave more like FCFS.
>
> ## 4. Priority Scheduling
>
> * **Core Idea:** Each process is assigned a priority. The process with the *highest* priority (often represented by the *smallest* number) is executed first.
>
> * **Analogy:** Imagine a hospital emergency room where patients are treated based on the severity of their injuries (higher priority for more severe cases).
> 
> * **Example:** If P1 has priority 1 (highest), P2 has priority 2, and P3 has priority 3, P1 will run first, then P2, then P3.
>
> * **Key Characteristics:**
>    * Can be preemptive or non-preemptive: A higher-priority process can preempt a lower-priority process (preemptive), or processes can run to completion based on their initial priority (non-preemptive).
 >   * Can lead to starvation: Low-priority processes might never get a chance to run if high-priority processes keep arriving.
>    * Priority inversion: A high-priority process might be blocked by a low-priority process that holds a resource it needs. This can be addressed with techniques like priority inheritance.
> 
> In summary, each algorithm has its strengths and weaknesses. The best choice depends on the specific needs of the system, such as whether it's an interactive system, a batch processing system, or a real-time system.

# Source Code
```c
#include <stdio.h>

struct Process {
    int pid;
    int burst_time;
    int arrival_time;
    int priority;
};

void calculate_avg_times(int wait_time[], int turnaround_time[], int n) {
    float total_wt = 0, total_tat = 0;
    for (int i = 0; i < n; i++) {
        total_wt += wait_time[i];
        total_tat += turnaround_time[i];
    }
    printf("\nTotal Waiting Time: %.2f", total_wt);
    printf("\nTotal Turnaround Time: %.2f", total_tat);
    printf("\nAverage Waiting Time: %.2f", total_wt / n);
    printf("\nAverage Turnaround Time: %.2f\n", total_tat / n);
}

void fcfs(struct Process p[], int n) {
    int wait_time[n], turnaround_time[n], completion_time[n];
    
    completion_time[0] = p[0].arrival_time + p[0].burst_time;
    wait_time[0] = 0;
    turnaround_time[0] = completion_time[0] - p[0].arrival_time;
    
    for (int i = 1; i < n; i++) {
        completion_time[i] = completion_time[i - 1] + p[i].burst_time;
        wait_time[i] = completion_time[i - 1] - p[i].arrival_time;
        turnaround_time[i] = wait_time[i] + p[i].burst_time;
    }
    
    
    printf("PID\tBT\tAT\tWT\tTAT\n");
    for (int i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\n", p[i].pid, p[i].burst_time, p[i].arrival_time, wait_time[i], turnaround_time[i]);
    }
    calculate_avg_times(wait_time, turnaround_time, n);
}

void sjf(struct Process p[], int n) {
    struct Process temp;
    int wait_time[n], turnaround_time[n], completion_time[n];
    
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].burst_time > p[j].burst_time) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }
    
    completion_time[0] = p[0].arrival_time + p[0].burst_time;
    wait_time[0] = 0;
    turnaround_time[0] = completion_time[0] - p[0].arrival_time;
    
    for (int i = 1; i < n; i++) {
        completion_time[i] = completion_time[i - 1] + p[i].burst_time;
        wait_time[i] = completion_time[i - 1] - p[i].arrival_time;
        turnaround_time[i] = wait_time[i] + p[i].burst_time;
    }
    
    printf("\nSJF Scheduling\n");
    printf("PID\tBT\tAT\tWT\tTAT\n");
    for (int i = 0; i < n; i++) {
        printf("%d\t%d\t%d\t%d\t%d\n", p[i].pid, p[i].burst_time, p[i].arrival_time, wait_time[i], turnaround_time[i]);
    }
    calculate_avg_times(wait_time, turnaround_time, n);
}

void priority_scheduling(struct Process p[], int n) {
    struct Process temp;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].priority > p[j].priority) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
        }
    }
    fcfs(p, n);
}

void round_robin(struct Process p[], int n, int quantum) {
    int remaining_bt[n], wait_time[n], turnaround_time[n], completion_time[n];
    for (int i = 0; i < n; i++) remaining_bt[i] = p[i].burst_time;
    int time = 0, done;
    
    printf("\nRound Robin Scheduling\n");
    printf("PID\tWT\tTAT\n");
    
    do {
        done = 1;
        for (int i = 0; i < n; i++) {
            if (remaining_bt[i] > 0) {
                done = 0;
                if (remaining_bt[i] > quantum) {
                    time += quantum;
                    remaining_bt[i] -= quantum;
                } else {
                    time += remaining_bt[i];
                    wait_time[i] = time - p[i].arrival_time - p[i].burst_time;
                    turnaround_time[i] = time - p[i].arrival_time;
                    printf("%d\t%d\t%d\n", p[i].pid, wait_time[i], turnaround_time[i]);
                    remaining_bt[i] = 0;
                }
            }
        }
    } while (!done);
    calculate_avg_times(wait_time, turnaround_time, n);
}

int main() {
    int n;
    printf("Enter number of processes: ");
    scanf("%d", &n);
    struct Process p[n];
    
    for (int i = 0; i < n; i++) {
        printf("Enter PID, Burst Time, Arrival Time, and Priority for Process %d: ", i + 1);
        scanf("%d %d %d %d", &p[i].pid, &p[i].burst_time, &p[i].arrival_time, &p[i].priority);
    }
    
    int choice, quantum;
    do {
        printf("Choose Scheduling Algorithm:\n1. FCFS\n2. SJF\n3. Priority\n4. Round Robin\n5. Exit\n");
        scanf("%d", &choice);
        
        switch (choice) {
            case 1:
		printf("\nFCFS Scheduling\n");
                fcfs(p, n);
                break;
            case 2:
                sjf(p, n);
                break;
            case 3:
		printf("\nPriority Sheduling \n");
                priority_scheduling(p, n);
                break;
            case 4:
                printf("Enter time quantum: ");
                scanf("%d", &quantum);
                round_robin(p, n, quantum);
                break;
            case 5:
                printf("Exiting program...\n");
                break;
            default:
                printf("Invalid choice\n");
        }
    } while (choice != 5);
    
    return 0;
}
```
