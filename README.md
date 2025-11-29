-----StackCalculator.java----
import java.util.Stack;

public class StackCalculator {

    // Data
    private Stack<Double> calculator = new Stack<>();
    private String command;

    // Display welcome message
    public void displayWelcomeMessage() {
        System.out.println("Welcome to the Stack Calculator.\n");
    }

    // Display list of commands
    public void displayCommands() {
        System.out.println("Commands: push n, add, sub, mult, div, list, clear, or quit.\n");
    }

    // Push a value onto the stack
    public void push(double value) {
        calculator.push(value);
    }

    // Pop a value, with error check
    public Double pop() {
        if (calculator.empty()) {
            System.out.println("Error: Stack is empty.");
            return null;
        }
        return calculator.pop();
    }

    // Return stack size
    public int size() {
        return calculator.size();
    }

    // Get values for printing
    public void listStack() {
        if (calculator.empty()) {
            System.out.println("empty");
            return;
        }

        for (Double value : calculator) {
            System.out.println(value);
        }
    }

    // Clear the stack
    public void clearStack() {
        calculator.clear();
    }

    // ADD – pop two numbers, add them, push result
    public void add() {
        if (calculator.size() < 2) {
            System.out.println("Error: Need at least two values to add.");
            return;
        }
        double a = calculator.pop();
        double b = calculator.pop();
        double result = a + b;
        calculator.push(result);
        System.out.println(result);
    }

    // SUB – pop two numbers: second - first
    public void sub() {
        if (calculator.size() < 2) {
            System.out.println("Error: Need at least two values to subtract.");
            return;
        }
        double a = calculator.pop();
        double b = calculator.pop();
        double result = b - a;
        calculator.push(result);
        System.out.println(result);
    }

    // MULT – multiply two values
    public void mult() {
        if (calculator.size() < 2) {
            System.out.println("Error: Need at least two values to multiply.");
            return;
        }
        double a = calculator.pop();
        double b = calculator.pop();
        double result = a * b;
        calculator.push(result);
        System.out.println(result);
    }

    // DIV – divide second value by first
    public void div() {
        if (calculator.size() < 2) {
            System.out.println("Error: Need at least two values to divide.");
            return;
        }

        double a = calculator.pop();
        double b = calculator.pop();

        if (a == 0) {
            System.out.println("Error: Division by zero.");
            // Push values back so state isn't lost
            calculator.push(b);
            calculator.push(a);
            return;
        }

        double result = b / a;
        calculator.push(result);
        System.out.println(result);
    }
}
--------StackCalculatorApp.java--------
import java.util.Scanner;

public class StackCalculatorApp {

    public static void main(String[] args) {

        StackCalculator calc = new StackCalculator();
        Scanner sc = new Scanner(System.in);

        calc.displayWelcomeMessage();
        calc.displayCommands();

        String input;

        while (true) {
            System.out.print("stack> ");
            input = sc.nextLine().trim().toLowerCase();

            if (input.startsWith("push")) {
                try {
                    String[] parts = input.split(" ");
                    double value = Double.parseDouble(parts[1]);
                    calc.push(value);
                    System.out.println(value);
                } catch (Exception e) {
                    System.out.println("Error: Use format 'push n'");
                }
            }

            else if (input.equals("add")) {
                calc.add();
            }

            else if (input.equals("sub")) {
                calc.sub();
            }

            else if (input.equals("mult")) {
                calc.mult();
            }

            else if (input.equals("div")) {
                calc.div();
            }

            else if (input.equals("list")) {
                calc.listStack();
            }

            else if (input.equals("clear")) {
                calc.clearStack();
                System.out.println("empty");
            }

            else if (input.equals("quit")) {
                System.out.println("Thanks for using The Stack Calculator.");
                break;
            }

            else {
                System.out.println("Unrecognized command.");
            }
        }

        sc.close();
    }
}
----------
public class Customer {

    // Instance variables
    private String customerID;
    private String ssn;
    private String lastName;
    private String firstName;
    private String street;
    private String city;
    private String state;
    private String zip;
    private String phone;

    // Constructor
    public Customer() {
        // Empty constructor for manual data entry
    }

    // Getters and Setters
    public String getCustomerID() {
        return customerID;
    }

    public void setCustomerID(String customerID) {
        this.customerID = customerID;
    }

    public String getSsn() {
        return ssn;
    }

    public void setSsn(String ssn) {
        this.ssn = ssn;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }

    public String getZip() {
        return zip;
    }

    public void setZip(String zip) {
        this.zip = zip;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    // toString() method to display all info neatly
    @Override
    public String toString() {
        return String.format(
            "%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s",
            customerID, ssn, lastName, firstName, street, city, state, zip, phone
        );

-----------------------

import java.util.Scanner;

public class DataEntry {

    private static Scanner input = new Scanner(System.in);

    // a) Entry of string data with no limit
    public static String getString(String prompt) {
        System.out.print(prompt);
        return input.nextLine().trim();
    }

    // b) Entry of string data but with a length limit
    public static String getLimitedString(String prompt, int maxLength) {
        String data;
        do {
            System.out.print(prompt);
            data = input.nextLine().trim();
            if (data.isEmpty()) {
                System.out.println("Input cannot be blank.");
            } else if (data.length() > maxLength) {
                System.out.println("Input cannot exceed " + maxLength + " characters.");
            } else {
                return data;
            }
        } while (true);
    }

    // c) Entry of string data that must only have numeric values
    public static String getNumericString(String prompt, int length) {
        String data;
        do {
            System.out.print(prompt);
            data = input.nextLine().trim();
            if (data.isEmpty()) {
                System.out.println("Input cannot be blank.");
            } else if (!data.matches("\\d+")) {
                System.out.println("Input must contain only numeric characters.");
            } else if (data.length() != length) {
                System.out.println("Input must be exactly " + length + " digits.");
            } else {
                return data;
            }
        } while (true);
    }

    // d) Entry of integer data
    public static int getInt(String prompt) {
        System.out.print(prompt);
        return Integer.parseInt(input.nextLine().trim());
    }

    // e) Entry of integer data with a range limit
    public static int getIntInRange(String prompt, int min, int max) {
        int value;
        do {
            System.out.print(prompt);
            value = Integer.parseInt(input.nextLine().trim());
            if (value < min || value > max) {
                System.out.println("Value must be between " + min + " and " + max + ".");
            } else {
                return value;
            }
        } while (true);
    }

    // f) Entry of decimal data
    public static double getDouble(String prompt) {
        System.out.print(prompt);
        return Double.parseDouble(input.nextLine().trim());
    }

    // g) Entry of decimal data with a range limit
    public static double getDoubleInRange(String prompt, double min, double max) {
        double value;
        do {
            System.out.print(prompt);
            value = Double.parseDouble(input.nextLine().trim());
            if (value < min || value > max) {
                System.out.println("Value must be between " + min + " and " + max + ".");
            } else {
                return value;
            }
        } while (true);
    }

    // h) Entry of a date (basic placeholder for Phase 1)
    public static String getDate(String prompt) {
        System.out.print(prompt);
        return input.nextLine().trim();
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
            Account a = null;   // <-- IMPORTANT: start as null

            System.out.println("\nEnter new customer information:");

            // ---------------- CUSTOMER INPUT VALIDATION -----------------

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

            // ---------------- ACCOUNT SELECTION -----------------
            System.out.println("\nEnter account information:");

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

            // -------- CREATE CORRECT SUBCLASS INSTANCE --------
            if (acctType.equalsIgnoreCase("CHK")) {
                a = new CheckingAccount();
            } else {
                a = new SavingsAccount();
            }

            // --------------- SUBCLASS VARIABLE VALIDATION ----------------

            while (true) {
                try {
                    a.setAccountNumber(DataEntry.getLimitedString("Account Number (max 5): ", 5));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    a.setBalance(DataEntry.getDouble("Starting Balance: "));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            customers.add(c);
            accounts.add(a);

            // ---------------- CONFIRM ----------------
            System.out.println("\nCustomer and Account added successfully!");
            System.out.println("--------------------------------------------");
            System.out.println(c);
            System.out.println(a);

            String another = DataEntry.getString("\nAdd another customer? (Y/N): ");
            addMore = another.equalsIgnoreCase("Y");
        }

        // ---------------- DISPLAY ALL CUSTOMERS ----------------
        System.out.println("\nAll Customer Information:");
        System.out.println("--------------------------------------------------------------------------------------------");
        System.out.printf("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s%n",
                "CustID", "SSN", "LastName", "FirstName", "Street", "City", "State", "Zip", "Phone");
        System.out.println("--------------------------------------------------------------------------------------------");

        for (Customer c : customers) {
            System.out.println(c);
        }

        // ---------------- DISPLAY ALL ACCOUNTS ----------------
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

public abstract class Account {

    protected String accountNumber;   // max 5
    protected String accountType;     // CHK or SAV
    protected double serviceFee;      // checking: .50, savings: .25
    protected double interestRate;    // checking: 2%, savings: 5%
    protected double overdrawFee;     // checking: 30.00, savings cannot overdraw
    protected double balance = 0.0;

    public String getAccountNumber() { return accountNumber; }
    public void setAccountNumber(String accountNumber) { this.accountNumber = accountNumber; }

    public String getAccountType() { return accountType; }
    public void setAccountType(String accountType) { this.accountType = accountType; }

    public double getServiceFee() { return serviceFee; }
    public void setServiceFee(double serviceFee) { this.serviceFee = serviceFee; }

    public double getInterestRate() { return interestRate; }
    public void setInterestRate(double interestRate) { this.interestRate = interestRate; }

    public double getOverdrawFee() { return overdrawFee; }
    public void setOverdrawFee(double overdrawFee) { this.overdrawFee = overdrawFee; }

    public double getBalance() { return balance; }

    // ABSTRACT METHODS REQUIRED FOR SUBCLASSES
    public abstract void withdrawal(double amount) throws Exception;
    public abstract void deposit(double amount);
    public abstract void balance();
}


---------AccountInterface.java---------
public interface AccountInterface {
    void withdrawal(double amount) throws Exception;
    void deposit(double amount);
    void balance();
}

---------checkingaccount.java----
import java.time.LocalDate;
import java.time.format.DateTimeParseException;

public class CheckingAccount extends Account implements AccountInterface {

    private String transactionDate;
    private String transactionType; // DEP or WTH
    private double transactionAmount;

    public CheckingAccount() {
        this.serviceFee = 0.50;
        this.interestRate = 2.0;
        this.overdrawFee = 30.00;
        this.accountType = "CHK";
    }

    public void setTransactionDate(String date) throws Exception {
        try {
            LocalDate.parse(date);
            this.transactionDate = date;
        } catch (DateTimeParseException e) {
            throw new Exception("Invalid date format. Use YYYY-MM-DD.");
        }
    }

    public void setTransactionType(String type) throws Exception {
        if (!type.equalsIgnoreCase("DEP") && !type.equalsIgnoreCase("WTH")) {
            throw new Exception("Transaction type must be DEP or WTH.");
        }
        this.transactionType = type.toUpperCase();
    }

    public void setTransactionAmount(double amt) throws Exception {
        if (amt <= 0) {
            throw new Exception("Amount must be greater than zero.");
        }
        this.transactionAmount = amt;
    }

    @Override
    public void deposit(double amt) {
        balance += amt;
        balance -= serviceFee;
    }

    @Override
    public void withdrawal(double amt) {
        balance -= amt;
        balance -= serviceFee;

        if (balance < 0) {
            balance -= overdrawFee;
        }
    }

    @Override
    public void balance() {
        balance += (balance * (interestRate / 100));
    }
}
----------SavingsAccount.Java---
import java.time.LocalDate;
import java.time.format.DateTimeParseException;

public class SavingsAccount extends Account implements AccountInterface {

    private String transactionDate;
    private String transactionType;
    private double transactionAmount;

    public SavingsAccount() {
        this.serviceFee = 0.25;
        this.interestRate = 5.0;
        this.overdrawFee = 0.00;
        this.accountType = "SAV";
    }

    public void setTransactionDate(String date) throws Exception {
        try {
            LocalDate.parse(date);
            this.transactionDate = date;
        } catch (DateTimeParseException e) {
            throw new Exception("Invalid date format. Use YYYY-MM-DD.");
        }
    }

    public void setTransactionType(String type) throws Exception {
        if (!type.equalsIgnoreCase("DEP") && !type.equalsIgnoreCase("WTH")) {
            throw new Exception("Transaction type must be DEP or WTH.");
        }
        this.transactionType = type.toUpperCase();
    }

    public void setTransactionAmount(double amt) throws Exception {
        if (amt <= 0) {
            throw new Exception("Amount must be greater than zero.");
        }
        this.transactionAmount = amt;
    }

    @Override
    public void deposit(double amt) {
        balance += amt;
        balance -= serviceFee;
    }

    @Override
    public void withdrawal(double amt) throws Exception {
        if (amt > balance) {
            throw new Exception("Savings accounts cannot be overdrawn.");
        }
        balance -= amt;
        balance -= serviceFee;
    }

    @Override
    public void balance() {
        balance += (balance * (interestRate / 100));
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





