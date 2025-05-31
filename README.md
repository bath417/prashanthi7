#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <iomanip>

using namespace std;

struct Account {
    int accountNumber;
    string name;
    double balance;

    void display() const {
        cout << "Account Number: " << accountNumber << endl;
        cout << "Name: " << name << endl;
        cout << fixed << setprecision(2) << "Balance: $" << balance << endl;
    }
};

class BankingSystem {
    vector<Account> accounts;

    void loadAccounts() {
        ifstream inFile("accounts.dat");
        accounts.clear();
        if (inFile.is_open()) {
            Account acc;
            while (inFile >> acc.accountNumber >> ws && getline(inFile, acc.name) && inFile >> acc.balance) {
                accounts.push_back(acc);
            }
            inFile.close();
        }
    }

    void saveAccounts() {
        ofstream outFile("accounts.dat");
        for (const auto& acc : accounts) {
            outFile << acc.accountNumber << endl
                    << acc.name << endl
                    << acc.balance << endl;
        }
        outFile.close();
    }

    Account* findAccount(int accountNumber) {
        for (auto& acc : accounts) {
            if (acc.accountNumber == accountNumber)
                return &acc;
        }
        return nullptr;
    }

public:
    BankingSystem() {
        loadAccounts();
    }

    ~BankingSystem() {
        saveAccounts();
    }

    void createAccount() {
        Account acc;
        cout << "Enter Account Number: ";
        cin >> acc.accountNumber;

        if (findAccount(acc.accountNumber)) {
            cout << "Account already exists.\n";
            return;
        }

        cout << "Enter Name: ";
        cin.ignore();
        getline(cin, acc.name);
        cout << "Enter Initial Deposit: ";
        cin >> acc.balance;

        accounts.push_back(acc);
        cout << "Account created successfully!\n";
    }

    void deposit() {
        int accNum;
        double amount;
        cout << "Enter Account Number: ";
        cin >> accNum;

        Account* acc = findAccount(accNum);
        if (!acc) {
            cout << "Account not found.\n";
            return;
        }

        cout << "Enter amount to deposit: ";
        cin >> amount;
        if (amount <= 0) {
            cout << "Invalid amount.\n";
            return;
        }

        acc->balance += amount;
        cout << "Deposit successful!\n";
    }

    void withdraw() {
        int accNum;
        double amount;
        cout << "Enter Account Number: ";
        cin >> accNum;

        Account* acc = findAccount(accNum);
        if (!acc) {
            cout << "Account not found.\n";
            return;
        }

        cout << "Enter amount to withdraw: ";
        cin >> amount;
        if (amount <= 0 || amount > acc->balance) {
            cout << "Invalid amount or insufficient funds.\n";
            return;
        }

        acc->balance -= amount;
        cout << "Withdrawal successful!\n";
    }

    void transfer() {
        int fromAcc, toAcc;
        double amount;
        cout << "Enter sender's Account Number: ";
        cin >> fromAcc;
        cout << "Enter receiver's Account Number: ";
        cin >> toAcc;

        Account* from = findAccount(fromAcc);
        Account* to = findAccount(toAcc);

        if (!from || !to) {
            cout << "One or both accounts not found.\n";
            return;
        }

        cout << "Enter amount to transfer: ";
        cin >> amount;

        if (amount <= 0 || amount > from->balance) {
            cout << "Invalid amount or insufficient balance.\n";
            return;
        }

        from->balance -= amount;
        to->balance += amount;
        cout << "Transfer successful!\n";
    }

    void showAccount() {
        int accNum;
        cout << "Enter Account Number: ";
        cin >> accNum;

        Account* acc = findAccount(accNum);
        if (!acc) {
            cout << "Account not found.\n";
            return;
        }

        acc->display();
    }

    void menu() {
        int choice;
        do {
            cout << "\n====== Online Banking System ======\n";
            cout << "1. Create Account\n";
            cout << "2. Deposit\n";
            cout << "3. Withdraw\n";
            cout << "4. Transfer\n";
            cout << "5. Show Account\n";
            cout << "0. Exit\n";
            cout << "Enter your choice: ";
            cin >> choice;

            switch (choice) {
                case 1: createAccount(); break;
                case 2: deposit(); break;
                case 3: withdraw(); break;
                case 4: transfer(); break;
                case 5: showAccount(); break;
                case 0: cout << "Exiting...\n"; break;
                default: cout << "Invalid choice. Try again.\n";
            }
        } while (choice != 0);
    }
};

int main() {
    BankingSystem bank;
    bank.menu();
    return 0;
}
