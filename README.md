#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TRAINS 100
#define MAX_TICKETS 500
#define MAX_USERS 10

typedef struct {
    int train_number;
    char train_name[50];
    char departure_station[50];
    char arrival_station[50];
    char departure_time[6];
    char arrival_time[6];
    int total_seats;
    int available_seats;
} Train;

typedef struct {
    char passenger_name[50];
    int train_number;
    char travel_date[11];
} Ticket;

typedef struct {
    char username[50];
    char password[50];
    double balance;
} User;

Train trains[MAX_TRAINS];
Ticket tickets[MAX_TICKETS];
User users[MAX_USERS];
int train_count = 0;
int ticket_count = 0;
int user_count = 0;

void addTrain(int number, const char* name, const char* departure, const char* arrival, const char* dep_time, const char* arr_time, int total_seats) {
    trains[train_count].train_number = number;
    strcpy(trains[train_count].train_name, name);
    strcpy(trains[train_count].departure_station, departure);
    strcpy(trains[train_count].arrival_station, arrival);
    strcpy(trains[train_count].departure_time, dep_time);
    strcpy(trains[train_count].arrival_time, arr_time);
    trains[train_count].total_seats = total_seats;
    trains[train_count].available_seats = total_seats;
    train_count++;
}

void bookTicket(const char* passenger, int number, const char* date) {
    for (int i = 0; i < train_count; i++) {
        if (trains[i].train_number == number) {
            if (trains[i].available_seats > 0) {
                strcpy(tickets[ticket_count].passenger_name, passenger);
                tickets[ticket_count].train_number = number;
                strcpy(tickets[ticket_count].travel_date, date);
                ticket_count++;
                trains[i].available_seats--;
                printf("Ticket booked successfully for %s on Train %d for %s!\n", passenger, number, date);
            } else {
                printf("Sorry, no available seats for Train %d on %s.\n", number, date);
            }
            return;
        }
    }
    printf("Train with number %d not found.\n", number);
}

void areyouseriouslycancelingyourticket(const char* passenger, int number, const char* date) {
    for (int i = 0; i < ticket_count; i++) {
        if (strcmp(tickets[i].passenger_name, passenger) == 0 && tickets[i].train_number == number && strcmp(tickets[i].travel_date, date) == 0) {
            for (int j = 0; j < train_count; j++) {
                if (trains[j].train_number == number) {
                    trains[j].available_seats++;
                    printf("Ticket for %s on Train %d for %s has been canceled.\n", passenger, number, date);
                    // Shift remaining tickets to fill the gap caused by cancellation
                    for (int k = i; k < ticket_count - 1; k++) {
                        strcpy(tickets[k].passenger_name, tickets[k + 1].passenger_name);
                        tickets[k].train_number = tickets[k + 1].train_number;
                        strcpy(tickets[k].travel_date, tickets[k + 1].travel_date);
                    }
                    ticket_count--;
                    return;
                }
            }
        }
    }
    printf("No ticket found for %s on Train %d for %s.\n", passenger, number, date);
}

int initiateUser(const char* username, const char* password) {
    for (int i = 0; i < user_count; i++) {
        if (strcmp(users[i].username, username) == 0 && strcmp(users[i].password, password) == 0) {
            return i;
        }
    }
    return -1;
}

int signupUser(const char* username, const char* password) {
    if (user_count < MAX_USERS) {
        strcpy(users[user_count].username, username);
        strcpy(users[user_count].password, password);
        users[user_count].balance = 0.0;
        user_count++;
        return 1;
    } else {
        return 0;
    }
}

int main() {
    addTrain(101, "Thejas Express", "Mumbai", "Delhi", "06:00", "23:30", 250);
    addTrain(102, "VC Superfast", "Delhi", "Chennai", "05:30", "23:45", 300);
    addTrain(103, "Sundar Sir's Streamliner", "Kolkata", "Bangalore", "06:15", "00:00", 200);
    addTrain(104, "cantthinkofaname", "Chennai", "Kochi", "05:45", "00:15", 180);

    char username[50], password[50];
    printf("Enter username: ");
    scanf("%s", username);
    printf("Enter password: ");
    scanf("%s", password);

    int userIndex = initiateUser(username, password);
    if (userIndex != -1) {
        printf("Welcome, %s!\n", users[userIndex].username);

        int choice;
        do {
            printf("\nMain Menu:\n");
            printf("1. Book Ticket\n");
            printf("2. View Train Schedule\n");
            printf("3. Account Balance\n");
            printf("4. Cancel Ticket\n");
            printf("5. Exit\n");
            printf("Enter your choice: ");
            scanf("%d", &choice);

            switch (choice) {
                case 1: {
                    char passenger[50];
                    int trainNumber;
                    char date[11];
                    printf("Enter passenger name: ");
                    scanf("%s", passenger);
                    printf("Enter train number: ");
                    scanf("%d", &trainNumber);
                    printf("Enter travel date (YYYY-MM-DD): ");
                    scanf("%s", date);
                    bookTicket(passenger, trainNumber, date);
                    break;
                }
                case 2:
                    printf("Train Schedule:\n");
                    for (int i = 0; i < train_count; i++) {
                        printf("Train Number: %d\n", trains[i].train_number);
                        printf("Train Name: %s\n", trains[i].train_name);
                        printf("Departure: %s\n", trains[i].departure_station);
                        printf("Arrival: %s\n", trains[i].arrival_station);
                        printf("Departure Time: %s\n", trains[i].departure_time);
                        printf("Arrival Time: %s\n", trains[i].arrival_time);
                        printf("Available Seats: %d\n", trains[i].available_seats);
                        printf("\n");
                    }
                    break;
                case 3:
                    printf("Account Balance: %.2lf\n", users[userIndex].balance);
                    break;
                case 4: {
                    char passenger[50];
                    int trainNumber;
                    char date[11];
                    printf("Enter passenger name: ");
                    scanf("%s", passenger);
                    printf("Enter train number: ");
                    scanf("%d", &trainNumber);
                    printf("Enter travel date (YYYY-MM-DD): ");
                    scanf("%s", date);
                    areyouseriouslycancelingyourticket(passenger, trainNumber, date);
                    break;
                }
                case 5:
                    printf("Exiting...\n");
                    break;
                default:
                    printf("Invalid choice. Please try again.\n");
            }
        } while (choice != 5);
    } else {
        printf("User not found. Do you want to sign up? (y/n): ");
        char ans;
        scanf(" %c", &ans);
        if (ans == 'y' || ans == 'Y') {
            printf("Enter new username: ");
            scanf("%s", username);
            printf("Enter new password: ");
            scanf("%s", password);
            if (signupUser(username, password)) {
                printf("Signup successful! You can now log in.\n");
            } else {
                printf("Maximum user limit reached. Signup failed.\n");
            }
        } else {
            printf("Exiting...\n");
        }
    }

    return 0;
}
