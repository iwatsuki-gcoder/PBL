#include <stdio.h>
#include <stdlib.h>

#define MAX 10   // Maximum number of jobs
#define QUANTUM 2 // Time quantum for Round Robin

// Structure to store each job's information
struct Job {
    int id;
    int arrival;
    int burst;
    int priority;
    int waiting;
    int turnaround;
};

// Simple array for jobs
struct Job jobs[MAX];

// Function prototypes
void inputJobs(int n);
void fcfs(int n);
void sjf(int n);
void prioritySched(int n);
void roundRobin(int n);

int main() {
    int n, choice;
    printf("Enter number of jobs (max %d): ", MAX);
    scanf("%d", &n);

    inputJobs(n);

    while (1) {
        printf("\n==== Job Scheduling Menu ====\n");
        printf("1. FCFS\n2. SJF\n3. Priority\n4. Round Robin\n5. Exit\n");
        printf("Choose: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: fcfs(n); break;
            case 2: sjf(n); break;
            case 3: prioritySched(n); break;
            case 4: roundRobin(n); break;
            case 5: printf("Exiting...\n"); return 0;
            default: printf("Invalid choice.\n");
        }
    }
}

// Get job details from user
void inputJobs(int n) {
    for (int i = 0; i < n; i++) {
        printf("\nEnter details for Job %d\n", i+1);
        jobs[i].id = i+1;
        printf("Arrival Time: ");
        scanf("%d", &jobs[i].arrival);
        printf("Burst Time: ");
        scanf("%d", &jobs[i].burst);
        printf("Priority (lower is higher): ");
        scanf("%d", &jobs[i].priority);
    }
}

// FCFS: First Come First Serve
void fcfs(int n) {
    int time = 0;
    printf("\nFCFS Scheduling\n");
    for (int i = 0; i < n; i++) {
        if (time < jobs[i].arrival) time = jobs[i].arrival;
        jobs[i].waiting = time - jobs[i].arrival;
        time += jobs[i].burst;
        jobs[i].turnaround = jobs[i].waiting + jobs[i].burst;
        printf("Job%d Waiting: %d, Turnaround: %d\n", jobs[i].id, jobs[i].waiting, jobs[i].turnaround);
    }
}

// SJF: Shortest Job First
void sjf(int n) {
    struct Job temp[MAX];
    for (int i = 0; i < n; i++) temp[i] = jobs[i];

    // Sort jobs by burst time
    for (int i = 0; i < n-1; i++)
        for (int j = i+1; j < n; j++)
            if (temp[i].burst > temp[j].burst) {
                struct Job t = temp[i];
                temp[i] = temp[j];
                temp[j] = t;
            }
    int time = 0;
    printf("\nSJF Scheduling\n");
    for (int i = 0; i < n; i++) {
        if (time < temp[i].arrival) time = temp[i].arrival;
        temp[i].waiting = time - temp[i].arrival;
        time += temp[i].burst;
        temp[i].turnaround = temp[i].waiting + temp[i].burst;
        printf("Job%d Waiting: %d, Turnaround: %d\n", temp[i].id, temp[i].waiting, temp[i].turnaround);
    }
}

// Priority Scheduling
void prioritySched(int n) {
    struct Job temp[MAX];
    for (int i = 0; i < n; i++) temp[i] = jobs[i];

    // Sort jobs by priority
    for (int i = 0; i < n-1; i++)
        for (int j = i+1; j < n; j++)
            if (temp[i].priority > temp[j].priority) {
                struct Job t = temp[i];
                temp[i] = temp[j];
                temp[j] = t;
            }
    int time = 0;
    printf("\nPriority Scheduling\n");
    for (int i = 0; i < n; i++) {
        if (time < temp[i].arrival) time = temp[i].arrival;
        temp[i].waiting = time - temp[i].arrival;
        time += temp[i].burst;
        temp[i].turnaround = temp[i].waiting + temp[i].burst;
        printf("Job%d Waiting: %d, Turnaround: %d\n", temp[i].id, temp[i].waiting, temp[i].turnaround);
    }
}

// Round Robin
void roundRobin(int n) {
    int remaining[MAX];
    for (int i = 0; i < n; i++) remaining[i] = jobs[i].burst;
    int time = 0, completed = 0;
    printf("\nRound Robin (Quantum=%d)\n", QUANTUM);

    while (completed < n) {
        for (int i = 0; i < n; i++) {
            if (remaining[i] > 0) {
                if (remaining[i] > QUANTUM) {
                    time += QUANTUM;
                    remaining[i] -= QUANTUM;
                } else {
                    time += remaining[i];
                    jobs[i].waiting = time - jobs[i].arrival - jobs[i].burst;
                    jobs[i].turnaround = jobs[i].waiting + jobs[i].burst;
                    remaining[i] = 0;
                    completed++;
                    printf("Job%d Waiting: %d, Turnaround: %d\n", jobs[i].id, jobs[i].waiting, jobs[i].turnaround);
                }
            }
        }
    }
}
