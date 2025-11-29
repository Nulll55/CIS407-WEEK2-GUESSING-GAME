----------
public class Customer {

    private String customerID, ssn, lastName, firstName, street, city, state, zip, phone;

    public void setCustomerID(String s) { customerID = s; }
    public void setSsn(String s) { ssn = s; }
    public void setLastName(String s) { lastName = s; }
    public void setFirstName(String s) { firstName = s; }
    public void setStreet(String s) { street = s; }
    public void setCity(String s) { city = s; }
    public void setState(String s) { state = s; }
    public void setZip(String s) { zip = s; }
    public void setPhone(String s) { phone = s; }

    @Override
    public String toString() {
        return String.format("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s",
                customerID, ssn, lastName, firstName, street, city, state, zip, phone);
    }
}

-----------------------

import java.util.Scanner;

public class DataEntry {

    private static Scanner sc = new Scanner(System.in);

    public static String getLimitedString(String prompt, int limit) {
        System.out.print(prompt);
        String input = sc.nextLine();
        if (input.length() > limit)
            throw new IllegalArgumentException("Input too long.");
        return input;
    }

    public static String getNumericString(String prompt, int length) {
        System.out.print(prompt);
        String input = sc.nextLine();
        if (!input.matches("\\d{" + length + "}"))
            throw new IllegalArgumentException("Must be " + length + " digits.");
        return input;
    }

    public static double getDouble(String prompt) {
        System.out.print(prompt);
        return Double.parseDouble(sc.nextLine());
    }

    public static String getString(String prompt) {
        System.out.print(prompt);
        return sc.nextLine();
    }
}

-------------
import java.util.ArrayList;

public class BankAcctApp {

    public static void main(String[] args) {

        ArrayList<Customer> customers = new ArrayList<>();
        ArrayList<Account> accounts = new ArrayList<>();

        boolean addMore = true;

        System.out.println("Welcome to the Banking Application - Phase 3");
        System.out.println("--------------------------------------------");

        while (addMore) {
            Customer c = new Customer();
            Account a = null;

            System.out.println("\nEnter new customer information:");

            while (true) {
                try {
                    c.setCustomerID(DataEntry.getLimitedString("Customer ID (max 5): ", 5));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setSsn(DataEntry.getNumericString("Customer SSN (9 digits): ", 9));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setLastName(DataEntry.getLimitedString("Last Name (max 20): ", 20));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setFirstName(DataEntry.getLimitedString("First Name (max 15): ", 15));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setStreet(DataEntry.getLimitedString("Street (max 20): ", 20));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setCity(DataEntry.getLimitedString("City (max 20): ", 20));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setState(DataEntry.getLimitedString("State (2 letters): ", 2));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setZip(DataEntry.getNumericString("Zip Code (5 digits): ", 5));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    c.setPhone(DataEntry.getNumericString("Phone (10 digits): ", 10));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            // ---------------- ACCOUNT TYPE ----------------
            String acctType;
            while (true) {
                try {
                    acctType = DataEntry.getLimitedString("Account Type (CHK or SAV): ", 3);
                    if (!acctType.equalsIgnoreCase("CHK") && 
                        !acctType.equalsIgnoreCase("SAV")) {
                        throw new IllegalArgumentException("Account type must be CHK or SAV.");
                    }
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            // ------------ STARTING BALANCE ----------------
            double startBalance;
            while (true) {
                try {
                    startBalance = DataEntry.getDouble("Starting Balance: ");
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            // -------- CREATE CORRECT ACCOUNT CLASS --------
            if (acctType.equalsIgnoreCase("CHK")) {
                a = new CheckingAccount(startBalance);
            } else {
                a = new SavingsAccount(startBalance);
            }

            while (true) {
                try {
                    a.setAccountNumber(DataEntry.getLimitedString("Account Number (max 5): ", 5));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            customers.add(c);
            accounts.add(a);

            System.out.println("\nCustomer and Account added successfully!");
            System.out.println("--------------------------------------------");
            System.out.println(c);
            System.out.println(a);

            String another = DataEntry.getString("\nAdd another customer? (Y/N): ");
            addMore = another.equalsIgnoreCase("Y");
        }

        // ---------------- DISPLAY ----------------
        System.out.println("\nAll Customer Information:");
        System.out.println("--------------------------------------------------------------------------------------------");
        System.out.printf("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s%n",
                "CustID", "SSN", "LastName", "FirstName", "Street", "City", "State", "Zip", "Phone");
        System.out.println("--------------------------------------------------------------------------------------------");

        for (Customer c : customers) {
            System.out.println(c);
        }

        System.out.println("\nAccount Details:");
        System.out.printf("%-10s %-10s %-12s %-12s%n",
                "Acct#", "Type", "Balance", "Class");
        System.out.println("--------------------------------------------------------------");

        for (Account a : accounts) {
            System.out.printf("%-10s %-10s %-12.2f %-12s%n",
                    a.getAccountNumber(),
                    a instanceof CheckingAccount ? "CHK" : "SAV",
                    a.getBalance(),
                    a.getClass().getSimpleName());
        }

        System.out.println("\nThank you for using the Banking Application!");
    }
}

------------------------------- account.java
package courseProject;

public abstract class Account {

    protected double balance;
    protected String accountNumber;

    public Account(double balance) {
        this.balance = balance;
    }

    public String getAccountNumber() {
        return accountNumber;
    }

    public void setAccountNumber(String num) {
        this.accountNumber = num;
    }

    public void setBalance(double bal) {
        this.balance = bal;
    }

    public double getBalance() {
        return balance;
    }

    public abstract void deposit(double amount, String date);

    public abstract void withdrawal(double amount, String date);

    @Override
    public String toString() {
        // Print account number and balance in a neat line
        return String.format("Account Number: %s | Balance: %.2f", 
                              accountNumber == null ? "N/A" : accountNumber,
                              balance);
    }
}

---------AccountInterface.java---------
public interface AccountInterface {
    void withdrawal(double amount) throws Exception;
    void deposit(double amount);
    void balance();
}

---------checkingaccount.java----
package courseProject;

public class CheckingAccount extends Account {

    private final double SERVICE_FEE = 0.50;
    private final double OVERDRAFT_FEE = 30.00;
    private final double INTEREST_RATE = 0.02;

    public CheckingAccount(double balance) {
        super(balance);
    }

    @Override
    public void deposit(double amount, String date) {
        if (amount <= 0) throw new IllegalArgumentException("Deposit must be positive.");

        balance += amount;
        balance -= SERVICE_FEE;

        System.out.println("CHK Deposit: +" + amount +
                " | Fee: -" + SERVICE_FEE +
                " | Date: " + date +
                " | New Balance: " + balance);
    }

    @Override
    public void withdrawal(double amount, String date) {
        if (amount <= 0) throw new IllegalArgumentException("Withdrawal must be positive.");

        balance -= amount;
        balance -= SERVICE_FEE;

        if (balance < 0) {
            balance -= OVERDRAFT_FEE;
            System.out.println("CHK Overdraft Fee Applied: -" + OVERDRAFT_FEE);
        }

        System.out.println("CHK Withdrawal: -" + amount +
                " | Fee: -" + SERVICE_FEE +
                " | Date: " + date +
                " | New Balance: " + balance);
    }

    public void applyInterest() {
        double interest = balance * INTEREST_RATE;
        balance += interest;
        System.out.println("CHK Interest Applied: +" + interest +
                " | New Balance: " + balance);
    }
}

----------SavingsAccount.Java---
package courseProject;

public class SavingsAccount extends Account {

    private final double SERVICE_FEE = 0.25;
    private final double INTEREST_RATE = 0.05;

    public SavingsAccount(double balance) {
        super(balance);
    }

    @Override
    public void deposit(double amount, String date) {
        if (amount <= 0) throw new IllegalArgumentException("Deposit must be positive.");

        balance += amount;
        balance -= SERVICE_FEE;

        System.out.println("SAV Deposit: +" + amount +
                " | Fee: -" + SERVICE_FEE +
                " | Date: " + date +
                " | New Balance: " + balance);
    }

    @Override
    public void withdrawal(double amount, String date) {
        if (amount <= 0) throw new IllegalArgumentException("Withdrawal must be positive.");

        if (amount + SERVICE_FEE > balance) {
            System.out.println("ERROR: Savings account cannot go negative. Withdrawal denied.");
            return;
        }

        balance -= amount;
        balance -= SERVICE_FEE;

        System.out.println("SAV Withdrawal: -" + amount +
                " | Fee: -" + SERVICE_FEE +
                " | Date: " + date +
                " | New Balance: " + balance);
    }

    public void applyInterest() {
        double interest = balance * INTEREST_RATE;
        balance += interest;

        System.out.println("SAV Interest Applied: +" + interest +
                " | New Balance: " + balance);
    }
}

---------------------------- countrylist.java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class CountriesList {
    // Data
    private ArrayList<String> countries = new ArrayList<>();
    private int menuOption;

    // Methods
    public void displayWelcomeMessage() {
        System.out.println("Country List Manager\n");
    }

    public void displayMenu() {
        System.out.println("COMMAND MENU");
        System.out.println("1 - List countries");
        System.out.println("2 - Add a country");
        System.out.println("3 - Exit");
    }

    public int getMenuOption(Scanner sc) {
        System.out.print("\nEnter menu number: ");
        menuOption = sc.nextInt();
        sc.nextLine(); // clear input buffer
        return menuOption;
    }

    public boolean checkForCountry(String country) {
        for (String c : countries) {
            if (c.equalsIgnoreCase(country)) {
                return true;
            }
        }
        return false;
    }

    public void addCountry(Scanner sc) {
        System.out.print("Enter country: ");
        String country = sc.nextLine().trim();

        if (checkForCountry(country)) {
            System.out.println("Country " + country + " already exists in list");
        } else {
            countries.add(country);
            System.out.println("Country " + country + " has been added.");
        }
    }

    public void listCountries() {
        if (countries.isEmpty()) {
            System.out.println("No countries in the list.");
        } else {
            Collections.sort(countries);
            for (String c : countries) {
                System.out.println(c);
            }
        }
    }
}
------------------------countries list app
import java.util.Scanner;

public class CountriesListApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        CountriesList cl = new CountriesList();

        cl.displayWelcomeMessage();

        int choice;
        do {
            cl.displayMenu();
            choice = cl.getMenuOption(sc);

            switch (choice) {
                case 1:
                    cl.listCountries();
                    break;
                case 2:
                    cl.addCountry(sc);
                    break;
                case 3:
                    System.out.println("Goodbye.");
                    break;
                default:
                    System.out.println("Invalid option. Please choose 1, 2, or 3.");
                    break;
            }
        } while (choice != 3);

        sc.close();
    }
}





