# banking-management-system
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Account {
    int accountNo;
    char name[50];
    float balance;
};

void createAccount();
void viewAccounts();
void depositMoney();
void withdrawMoney();
void searchAccount();

int main() {
    int choice;

    while (1) {
        printf("\n====== BANKING SYSTEM ======\n");
        printf("1. Create Account\n");
        printf("2. View All Accounts\n");
        printf("3. Deposit Money\n");
        printf("4. Withdraw Money\n");
        printf("5. Search Account\n");
        printf("6. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: createAccount(); break;
            case 2: viewAccounts(); break;
            case 3: depositMoney(); break;
            case 4: withdrawMoney(); break;
            case 5: searchAccount(); break;
            case 6: exit(0);
            default: printf("Invalid choice! Try again.\n");
        }
    }
    return 0;
}

void createAccount() {
    struct Account acc;
    FILE *fp = fopen("accounts.dat", "ab");
    if (!fp) {
        printf("Error opening file!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &acc.accountNo);
    printf("Enter Name: ");
    scanf(" %[^\n]", acc.name);
    printf("Enter Initial Deposit: ");
    scanf("%f", &acc.balance);

    fwrite(&acc, sizeof(acc), 1, fp);
    fclose(fp);
    printf("\n✅ Account created successfully!\n");
}

void viewAccounts() {
    struct Account acc;
    FILE *fp = fopen("accounts.dat", "rb");
    if (!fp) {
        printf("No accounts found!\n");
        return;
    }

    printf("\n--- Account List ---\n");
    while (fread(&acc, sizeof(acc), 1, fp)) {
        printf("Account No: %d | Name: %s | Balance: ₹%.2f\n",
               acc.accountNo, acc.name, acc.balance);
    }
    fclose(fp);
}

void depositMoney() {
    struct Account acc;
    int accNo;
    float amount;
    int found = 0;

    FILE *fp = fopen("accounts.dat", "rb+");
    if (!fp) {
        printf("No accounts found!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &accNo);

    while (fread(&acc, sizeof(acc), 1, fp)) {
        if (acc.accountNo == accNo) {
            printf("Enter deposit amount: ");
            scanf("%f", &amount);
            acc.balance += amount;
            fseek(fp, -sizeof(acc), SEEK_CUR);
            fwrite(&acc, sizeof(acc), 1, fp);
            found = 1;
            printf("\n✅ Money deposited successfully!\n");
            break;
        }
    }
    if (!found) printf("\n❌ Account not found!\n");

    fclose(fp);
}

void withdrawMoney() {
    struct Account acc;
    int accNo;
    float amount;
    int found = 0;

    FILE *fp = fopen("accounts.dat", "rb+");
    if (!fp) {
        printf("No accounts found!\n");
        return;
    }

    printf("Enter Account Number: ");
    scanf("%d", &accNo);

    while (fread(&acc, sizeof(acc), 1, fp)) {
        if (acc.accountNo == accNo) {
            printf("Enter withdrawal amount: ");
            scanf("%f", &amount);

            if (amount > acc.balance) {
                printf("\n❌ Insufficient balance!\n");
            } else {
                acc.balance -= amount;
                fseek(fp, -sizeof(acc), SEEK_CUR);
                fwrite(&acc, sizeof(acc), 1, fp);
                printf("\n✅ Money withdrawn successfully!\n");
            }
            found = 1;
            break;
        }
    }
    if (!found) printf("\n❌ Account not found!\n");

    fclose(fp);
}

void searchAccount() {
    struct Account acc;
    int accNo;
    int found = 0;

    FILE *fp = fopen("accounts.dat", "rb");
    if (!fp) {
        printf("No accounts found!\n");
        return;
    }

    printf("Enter Account Number to search: ");
    scanf("%d", &accNo);

    while (fread(&acc, sizeof(acc), 1, fp)) {
        if (acc.accountNo == accNo) {
            printf("\n✅ Account Found!\n");
            printf("Account No: %d | Name: %s | Balance: ₹%.2f\n",
                   acc.accountNo, acc.name, acc.balance);
            found = 1;
            break;
        }
    }
    if (!found) printf("\n❌ Account not found!\n");

    fclose(fp);
}

