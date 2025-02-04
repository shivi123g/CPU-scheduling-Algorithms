#include <limits.h>
#include <stdbool.h>

struct process {
    int pid;
    int arrival_time;
    int burst_time;
    int remaining_time;
    int start_time;
    int completion_time;
    int turnaround_time;
    int response_time;
};

// Function to calculate and print average response and turnaround times
void calculate_avg_times(struct process p[], int n) {
    float avg_response_time = 0, avg_turnaround_time = 0;
    for (int i = 0; i < n; i++) {
        avg_response_time += p[i].response_time;
        avg_turnaround_time += p[i].turnaround_time;
    }
    avg_response_time /= n;
    avg_turnaround_time /= n;
    printf("Average Response Time: %.2f\n", avg_response_time);
    printf("Average Turnaround Time: %.2f\n", avg_turnaround_time);
}
void initialize_remaining_time(struct process p[], int n) {
    for (int i = 0; i < n; i++) {
        p[i].remaining_time = p[i].burst_time;
    }
}
// Sort processes by arrival time for FIFO scheduling
void sort_by_arrival(struct process p[], int n) {
    struct process temp;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (p[i].arrival_time > p[j].arrival_time) {
                temp = p[i];
                p[i] = p[j];
                p[j] = temp;
            }
            else if(p[i].arrival_time==p[j].arrival_time){
                if(p[j].burst_time < p[i].burst_time){
                    temp = p[i];
                    p[i] = p[j];
                    p[j] = temp;
                    
                }
            }
        }
    }
}

// First-In-First-Out (FIFO) Scheduling
void fifo(struct process p[], int n) {
    printf("\n--- FIFO Scheduling ---\n");
    int current_time = 0;

    // Sort the processes by arrival time before scheduling
    sort_by_arrival(p, n);

    for (int i = 0; i < n; i++) {
        if (current_time < p[i].arrival_time) {
            current_time = p[i].arrival_time;  // Wait until the process arrives
        }
        p[i].start_time = current_time;
        p[i].completion_time = current_time + p[i].burst_time;
        p[i].turnaround_time = p[i].completion_time - p[i].arrival_time;
        p[i].response_time = p[i].start_time - p[i].arrival_time;
        current_time += p[i].burst_time;
        printf("Process %d completed at time %d\n", p[i].pid, current_time);
    }
    calculate_avg_times(p, n);
}

// Shortest Job First (SJF) Scheduling
void sjf(struct process p[], int n) {
    printf("\n--- Shortest Job First Scheduling ---\n");
    int current_time = 0, completed = 0, min_burst_time, idx;
    
    bool is_completed[n];
    for (int i = 0; i < n; i++) is_completed[i] = false;

    while (completed != n) {
        min_burst_time = INT_MAX;
        idx = -1;
        
        for (int i = 0; i < n; i++) {
            if (p[i].arrival_time <= current_time && !is_completed[i]&&p[i].burst_time<min_burst_time) {
                min_burst_time = p[i].burst_time;
                idx = i;
            }
        }
        if (idx != -1) {
            p[idx].start_time = current_time;
            p[idx].completion_time = current_time + p[idx].burst_time;
            p[idx].turnaround_time = p[idx].completion_time - p[idx].arrival_time;
            p[idx].response_time = p[idx].start_time - p[idx].arrival_time;
            current_time += p[idx].burst_time;
            is_completed[idx] = true;
            completed++;
            printf("Process %d completed at time %d\n", p[idx].pid, current_time);
        } else {
            current_time++;
        }
    }
    calculate_avg_times(p, n);
}
void srtf(struct process p[], int n) {
    printf("\n--- Shortest Remaining Time First Scheduling ---\n");
    int current_time = 0, completed = 0, min_remaining_time, idx;
    bool is_started[n];
    for (int i = 0; i < n; i++) is_started[i] = false;
    while (completed != n) {
        min_remaining_time = INT_MAX;
        idx = -1;
        for (int i = 0; i < n; i++) {
            if (p[i].arrival_time <= current_time && p[i].remaining_time > 0 && p[i].remaining_time < min_remaining_time) {
                min_remaining_time = p[i].remaining_time;
                idx = i;
            }
        }
        if (idx != -1) {
            if (!is_started[idx]) {
                p[idx].start_time = current_time;
                is_started[idx] = true;
            }
            p[idx].remaining_time--;
            current_time++;
            printf("Process %d\n",p[idx].pid);
            if (p[idx].remaining_time == 0) {
                p[idx].completion_time = current_time;
                p[idx].turnaround_time = p[idx].completion_time - p[idx].arrival_time;
                p[idx].response_time = p[idx].start_time - p[idx].arrival_time;
                completed++;
                printf("Process %d completed at time %d\n", p[idx].pid, current_time);
            }
        } else {
            current_time++;
        }
    }
    calculate_avg_times(p, n);
}

// Round Robin (RR) Scheduling
void rr(struct process p[], int n, int time_quantum) {
    printf("\n--- Round Robin Scheduling ---\n");
    int current_time = 0, completed = 0;
    int queue[n], front = 0, rear = 0;
    bool is_started[n], in_queue[n];
    
    // Initialize flags
    for (int i = 0; i < n; i++) {
        is_started[i] = false;
        in_queue[i] = false;
    }

    while (completed != n) {
        // Add processes that have arrived by current_time to the queue
        for (int i = 0; i < n; i++) {
            if (p[i].arrival_time <= current_time && p[i].remaining_time > 0 && !in_queue[i]) {
                queue[rear++] = i;
                in_queue[i] = true;
                printf("Process %d added to the queue at time %d\n", p[i].pid, current_time);
            }
        }

        // If there are processes in the queue, execute the one at the front
        if (front != rear) {
            int idx = queue[front++];
            in_queue[idx] = false;  // Mark it as not in queue after taking it out

            // Start execution if it's the first time
            if (!is_started[idx]) {
                p[idx].start_time = current_time;
                is_started[idx] = true;
                printf("Process %d started execution at time %d\n", p[idx].pid, current_time);
            }

            // Execute for either time_quantum or the remaining time
            int exec_time = (p[idx].remaining_time > time_quantum) ? time_quantum : p[idx].remaining_time;
            current_time += exec_time;
            p[idx].remaining_time -= exec_time;

            // If the process finishes, update completion times
            if (p[idx].remaining_time == 0) {
                p[idx].completion_time = current_time;
                p[idx].turnaround_time = p[idx].completion_time - p[idx].arrival_time;
                p[idx].response_time = p[idx].start_time - p[idx].arrival_time;
                completed++;
                printf("Process %d completed at time %d\n", p[idx].pid, current_time);
            } else {
                // If not finished, requeue the process
                queue[rear++] = idx;
                in_queue[idx] = true;
                printf("Process %d requeued at time %d\n", p[idx].pid, current_time);
            }
        } else {
            // If no processes are ready, increment time
            current_time++;
        }
    }

    calculate_avg_times(p, n);
}


// Main function
int main() {
    int n, time_quantum;
    printf("Enter the number of processes: ");
    scanf("%d", &n);
    struct process p[n];

    // Input process details
    for (int i = 0; i < n; i++) {
        printf("Enter arrival time and burst time for process %d: ", i + 1);
        p[i].pid = i + 1;
        scanf("%d %d", &p[i].arrival_time, &p[i].burst_time);
        p[i].remaining_time = p[i].burst_time;
    }
    printf("Enter the time quantum for Round Robin: ");
    scanf("%d", &time_quantum);
    initialize_remaining_time(p, n);

    // Simulate different scheduling algorithms
    fifo(p, n);
    sjf(p, n);
    srtf(p, n);
    rr(p, n, time_quantum);

    return 0;
}
