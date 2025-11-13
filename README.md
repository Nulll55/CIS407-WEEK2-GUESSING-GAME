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
        boolean addMore = true;

        System.out.println("Welcome to the Banking Application - Phase 2");
        System.out.println("--------------------------------------------");

        while (addMore) {
            Customer c = new Customer();
            Account a = new Account();

            System.out.println("\nEnter new customer information:");

            // --- CUSTOMER INPUT WITH TRY/CATCH VALIDATION ---
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

            // --- ACCOUNT INPUT WITH TRY/CATCH VALIDATION ---
            System.out.println("\nEnter account information:");
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
                    a.setAccountType(DataEntry.getLimitedString("Account Type (CHK or SAV): ", 3));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    a.setServiceFee(DataEntry.getDoubleInRange("Service Fee (0–10): ", 0, 10));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    a.setInterestRate(DataEntry.getDoubleInRange("Interest Rate (0–10): ", 0, 10));
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            while (true) {
                try {
                    a.setOverdrawFee(DataEntry.getDouble("Overdraw Fee: "));
                    if (a.getOverdrawFee() < 0) throw new IllegalArgumentException("Fee cannot be negative.");
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            customers.add(c);

            // Display confirmation
            System.out.println("\nCustomer and Account added successfully!");
            System.out.println("--------------------------------------------");
            System.out.println(c);
            System.out.println(a);

            String another = DataEntry.getString("\nAdd another customer? (Y/N): ");
            addMore = another.equalsIgnoreCase("Y");
        }

        // --- DISPLAY ALL ---
        System.out.println("\nAll Customer and Account Information:");
        System.out.println("--------------------------------------------------------------------------------------------");
        System.out.printf("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s%n",
                "CustID", "SSN", "LastName", "FirstName", "Street", "City", "State", "Zip", "Phone");
        System.out.println("--------------------------------------------------------------------------------------------");

        for (Customer c : customers) {
            System.out.println(c);
        }

        System.out.println("\nAccount Details:");
        System.out.printf("%-8s %-5s %-10s %-12s %-12s %-12s%n",
                "Acct#", "Type", "SvcFee", "IntRate(%)", "Overdraw", "Balance");
        System.out.println("--------------------------------------------------------------");
        // Eventually this will print from Checking/Savings subclass objects in Phase 3

        System.out.println("\nThank you for using the Banking Application!");
    }
}

------------------------------- account.java

public class Account {
    // Instance variables
    private String accountNumber;
    private String accountType; // "CHK" or "SAV"
    private double serviceFee;  // 0.00–10.00
    private double interestRate; // 0–10 percent
    private double overdrawFee;
    private double balance; // starts at 0.0

    // Constructor
    public Account() {
        this.balance = 0.0;
    }

    // Getters and Setters
    public String getAccountNumber() {
        return accountNumber;
    }

    public void setAccountNumber(String accountNumber) {
        this.accountNumber = accountNumber;
    }

    public String getAccountType() {
        return accountType;
    }

    public void setAccountType(String accountType) {
        if (!accountType.equalsIgnoreCase("CHK") && !accountType.equalsIgnoreCase("SAV")) {
            throw new IllegalArgumentException("Account type must be CHK or SAV.");
        }
        this.accountType = accountType.toUpperCase();
    }

    public double getServiceFee() {
        return serviceFee;
    }

    public void setServiceFee(double serviceFee) {
        if (serviceFee < 0 || serviceFee > 10.00) {
            throw new IllegalArgumentException("Service fee must be between 0 and 10.00.");
        }
        this.serviceFee = serviceFee;
    }

    public double getInterestRate() {
        return interestRate;
    }

    public void setInterestRate(double interestRate) {
        if (interestRate < 0 || interestRate > 10) {
            throw new IllegalArgumentException("Interest rate must be between 0 and 10 percent.");
        }
        this.interestRate = interestRate;
    }

    public double getOverdrawFee() {
        return overdrawFee;
    }

    public void setOverdrawFee(double overdrawFee) {
        if (overdrawFee < 0) {
            throw new IllegalArgumentException("Overdraw fee cannot be negative.");
        }
        this.overdrawFee = overdrawFee;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return String.format("%-8s %-5s $%-8.2f %-8.2f%% $%-8.2f $%-8.2f",
                accountNumber, accountType, serviceFee, interestRate, overdrawFee, balance);
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





