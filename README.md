
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NAME_LEN 10
#define CLIENTS_FILE "clients.dat"
#define ACCOUNTS_FILE "accounts.dat"
#define ACCOUNTS_TEMP_FILE "temp.dat"

typedef struct {
    int id;
    char lastName[MAX_NAME_LEN];
    char firstName[MAX_NAME_LEN];
} Client;

typedef struct {
    int id;
    int clientId;
    int balance;
    int day;
    int month;
    int year;
} Account;

int menu();
void manageClients();
void manageAccounts();
void manageTransactions();
void addClient();
void modifyClient();
void deleteClient();
void showClients();
void addAccount();
void closeAccount();
void searchAccount();
void showAllAccounts();
void deleteAccount();
void withdraw();
void deposit();
void transferFunds() ;
int compareClients(const void* a, const void* b);

int main() {
    while (menu() != 4) {}
    return 0;
}

int menu() {
    int choice;
    printf("\n  The Main Menu:\n");
    printf("1 - Client Management\n");
    printf("2 - Account Management\n");
    printf("3 - Transaction Management\n");
    printf("4 - Transfer Funds Between Accounts\n");
    printf("5 - Exit\n");
    printf("Enter your choice: ");
    scanf("%d", &choice);

    switch (choice) {
        case 1:
            manageClients();
            break;
        case 2:
            manageAccounts();
            break;
        case 3:
            manageTransactions();
            break;
        case 4:
             transferFunds() ;
             break;
        case 5:
            printf("Exiting program.\n");
            break;
        default:
            printf("Invalid choice, please select from the menu options.\n");
    }
    return choice;
}

void manageClients() {
    int subChoice;
    printf("\nClient Management:\n");
    printf("1 - Add a client\n");
    printf("2 - Modify a client\n");
    printf("3 - Delete a client\n");
    printf("4 - List all clients\n");
    printf("5 - Return to main menu\n");
    printf("Enter your choice: ");
    scanf("%d", &subChoice);

    switch (subChoice) {
        case 1:
            addClient();
            break;
        case 2:
            modifyClient();
            break;
        case 3:
            deleteClient();
            break;
        case 4:
            showClients();
            break;
        case 5:
            break; // Return to the main menu
        default:
            printf("Invalid choice, please select a correct sub-option.\n");
    }
}

void manageAccounts() {
    int subChoice;
    printf("\nAccount Management:\n");
    printf("1 - Add an account\n");
    printf("2 - Search for an account\n");
    printf("3 - List all accounts\n");
    printf("4 - Delete an account\n");
    printf("5 - Close an account\n");
    printf("6 - Return to main menu\n");
    printf("Enter your choice: ");
    scanf("%d", &subChoice);

    switch (subChoice) {
        case 1:
            addAccount();
            break;
        case 2:
            searchAccount();
            break;
        case 3:
            showAllAccounts();
            break;
        case 4:
            deleteAccount();
            break;
        case 5:
            closeAccount();
            break;
        case 6:
            break;
        default:
            printf("Invalid choice, please select a correct sub-option.\n");
    }
}

void manageTransactions() {
    int subChoice;
    printf("\nTransaction Management:\n");
    printf("1 - Withdraw money\n");
    printf("2 - Deposit money\n");
    printf("Enter your choice: ");
    scanf("%d", &subChoice);

    switch (subChoice) {
        case 1:
            withdraw();
            break;
        case 2:
            deposit();
            break;
        default:
            printf("Invalid choice, please select a correct transaction option.\n");
    }
}

void addClient() {
    Client newClient;
    printf("Enter client ID: ");
    scanf("%d", &newClient.id);
    printf("Enter last name: ");
    scanf("%s", newClient.lastName);
    printf("Enter first name: ");
    scanf("%s", newClient.firstName);

    FILE *file = fopen(CLIENTS_FILE, "a");
    if (file != NULL) {
        fwrite(&newClient, sizeof(Client), 1, file);
        fclose(file);
        printf("Client added successfully.\n");
    } else {
        printf("Error opening file.\n");
    }
}

void modifyClient() {
    int clientId;
    printf("Enter the client ID to modify: ");
    scanf("%d", &clientId);

    int found = 0;
    Client client;
    FILE *file = fopen(CLIENTS_FILE, "r+");
    while(fread(&client, sizeof(Client), 1, file)) {
        if (client.id == clientId) {
            printf("Enter new last name: ");
            scanf("%s", client.lastName);
            printf("Enter new first name: ");
            scanf("%s", client.firstName);
            fseek(file, -sizeof(Client), SEEK_CUR);
            fwrite(&client, sizeof(Client), 1, file);
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("Client not found.\n");
        return;
    }
    printf("Client modified successfully.\n");
}

void deleteClient() {
    int clientId;
    printf("Enter the client ID to delete: ");
    scanf("%d", &clientId);

    int found = 0;
    Client client;
    FILE *file = fopen(CLIENTS_FILE, "r");
    FILE *tempFile = fopen(ACCOUNTS_TEMP_FILE, "w"); // Corrected file name
    while (fread(&client, sizeof(Client), 1, file)) {
        if (client.id != clientId) {
            fwrite(&client, sizeof(Client), 1, tempFile);
        } else {
            found = 1;
        }
    }
    fclose(file);
    fclose(tempFile); // Added this line

    if (!found) {
        printf("Client not found.\n");
        remove(ACCOUNTS_TEMP_FILE); // Corrected file name
        return;
    }

    remove(CLIENTS_FILE);
    rename(ACCOUNTS_TEMP_FILE, CLIENTS_FILE);
    printf("Client deleted successfully.\n");
}

void showClients() {
    Client client;
    FILE *file = fopen(CLIENTS_FILE, "r");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    while(fread(&client, sizeof(Client), 1, file)) {
        printf("Client ID: %d\n", client.id);
        printf("Last Name: %s\n", client.lastName);
        printf("First Name: %s\n\n", client.firstName);
    }
    fclose(file);
}

void addAccount() {
    Account newAccount;
    printf("Enter account ID: ");
    scanf("%d", &newAccount.id);

    FILE *file = fopen(ACCOUNTS_FILE, "a");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    printf("Enter client ID: ");
    scanf("%d", &newAccount.clientId);
    printf("Enter the day the account was created: ");
    scanf("%d", &newAccount.day);
    printf("Enter the month the account was created: ");
    scanf("%d", &newAccount.month);
    printf("Enter the year the account was created: ");
    scanf("%d", &newAccount.year);
    printf("Enter initial balance: ");
    scanf("%d", &newAccount.balance);

    fwrite(&newAccount, sizeof(Account), 1, file);
    fclose(file);
    printf("Account added successfully.\n");
}

void searchAccount() {
    int accountId;
    printf("Enter the account ID to search for: ");
    scanf("%d", &accountId);

    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.id == accountId) {
            printf("Account ID: %d\n", account.id);
            printf("Client ID: %d\n", account.clientId);
            printf("Balance: %d\n", account.balance);
            printf("Created on: %02d-%02d-%04d\n\n", account.day, account.month, account.year);
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("Account not found.\n");
    }
}

void showAllAccounts() {
    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    while (fread(&account, sizeof(Account), 1, file)) {
        printf("Account ID: %d\n", account.id);
        printf("Client ID: %d\n", account.clientId);
        printf("Balance: %d\n", account.balance);
        printf("Created on: %02d-%02d-%04d\n\n", account.day, account.month, account.year);
    }
    fclose(file);
}

void deleteAccount() {
    int accountId;
    printf("Enter the account ID to delete: ");
    scanf("%d", &accountId);

    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r");
    FILE *tempFile = fopen(ACCOUNTS_TEMP_FILE, "w");
    if (file == NULL || tempFile == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.id != accountId) {
            fwrite(&account, sizeof(Account), 1, tempFile);
        } else {
            found = 1;
        }
    }
    fclose(file);
    fclose(tempFile);

    if (!found) {
        printf("Account not found.\n");
        remove(ACCOUNTS_TEMP_FILE);
        return;
    }

    remove(ACCOUNTS_FILE);
    rename(ACCOUNTS_TEMP_FILE, ACCOUNTS_FILE);
    printf("Account deleted successfully.\n");
}
void withdraw() {
    int accountId, withdrawAmount;
    printf("Enter account ID for withdrawal: ");
    scanf("%d", &accountId);

    printf("Enter withdrawal amount: ");
    scanf("%d", &withdrawAmount);

    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r+");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.id == accountId) {
            if (account.balance >= withdrawAmount) {
                account.balance -= withdrawAmount;
                fseek(file, -sizeof(Account), SEEK_CUR);
                fwrite(&account, sizeof(Account), 1, file);
                printf("Withdrawal successful. New balance: %d\n", account.balance);
            } else {
                printf("Insufficient funds for withdrawal.\n");
            }
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("Account not found.\n");
    }
}

void deposit() {
    int accountId, depositAmount;
    printf("Enter account ID for deposit: ");
    scanf("%d", &accountId);

    printf("Enter deposit amount: ");
    scanf("%d", &depositAmount);

    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r+");
    if (file == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.id == accountId) {
            account.balance += depositAmount;
            fseek(file, -sizeof(Account), SEEK_CUR);
            fwrite(&account, sizeof(Account), 1, file);
            printf("Deposit successful. New balance: %d\n", account.balance);
            found = 1;
            break;
        }
    }
    fclose(file);

    if (!found) {
        printf("Account not found.\n");
    }
}
void closeAccount() {
    int accountId;
    printf("Enter account ID to close: ");
    scanf("%d", &accountId);

    Account account;
    FILE *file = fopen(ACCOUNTS_FILE, "r");
    FILE *tempFile = fopen(ACCOUNTS_TEMP_FILE, "w");
    if (file == NULL || tempFile == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int found = 0;
    while (fread(&account, sizeof(Account), 1, file)) {
        if (account.id != accountId) {
            fwrite(&account, sizeof(Account), 1, tempFile);
        } else {
            found = 1;
        }
    }
    fclose(file);
    fclose(tempFile);

    if (!found) {
        printf("Account not found.\n");
        remove(ACCOUNTS_TEMP_FILE);
        return;
    }

    remove(ACCOUNTS_FILE);
    rename(ACCOUNTS_TEMP_FILE, ACCOUNTS_FILE);
    printf("Account closed successfully.\n");
}
void transferFunds() {
    int sourceAccountId, destinationAccountId, transferAmount;
    printf("Enter source account ID: ");
    scanf("%d", &sourceAccountId);

    printf("Enter destination account ID: ");
    scanf("%d", &destinationAccountId);

    printf("Enter transfer amount: ");
    scanf("%d", &transferAmount);

    Account sourceAccount, destinationAccount;
    FILE *file = fopen(ACCOUNTS_FILE, "r");
    FILE *tempFile = fopen(ACCOUNTS_TEMP_FILE, "w");

    if (file == NULL || tempFile == NULL) {
        printf("Error opening file.\n");
        return;
    }

    int sourceFound = 0, destinationFound = 0;

    while (fread(&sourceAccount, sizeof(Account), 1, file)) {
        if (sourceAccount.id == sourceAccountId) {
            if (sourceAccount.balance >= transferAmount) {
                sourceAccount.balance -= transferAmount;
                sourceFound = 1;
            } else {
                printf("Insufficient funds in the source account for the transfer.\n");
                fclose(file);
                fclose(tempFile);
                remove(ACCOUNTS_TEMP_FILE);
                return;
            }
        }
        fwrite(&sourceAccount, sizeof(Account), 1, tempFile);
    }

    fclose(file);
    fseek(tempFile, 0, SEEK_SET);
    while (fread(&destinationAccount, sizeof(Account), 1, tempFile)) {
        if (destinationAccount.id == destinationAccountId) {
            destinationAccount.balance += transferAmount;
            destinationFound = 1;
        }
    }

    fclose(tempFile);

    if (!sourceFound) {
        printf("Source account not found.\n");
        remove(ACCOUNTS_TEMP_FILE);
        return;
    }

    if (!destinationFound) {
        printf("Destination account not found.\n");
        return;
    }

    remove(ACCOUNTS_FILE);
    rename(ACCOUNTS_TEMP_FILE, ACCOUNTS_FILE);
    printf("Transfer successful.\n");
}


int compareClients(const void* a, const void* b) {
    const Client *client1 = (const Client*)a;
    const Client *client2 = (const Client*)b;
    return strcmp(client1->lastName, client2->lastName);
}
