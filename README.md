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

        System.out.println("Welcome to the Banking Application - Phase 1");
        System.out.println("--------------------------------------------");

        while (addMore) {
            Customer c = new Customer();

            System.out.println("\nEnter new customer information:");

            c.setCustomerID(DataEntry.getLimitedString("Customer ID (max 5): ", 5));
            c.setSsn(DataEntry.getNumericString("Customer SSN (9 digits): ", 9));
            c.setLastName(DataEntry.getLimitedString("Last Name (max 20): ", 20));
            c.setFirstName(DataEntry.getLimitedString("First Name (max 15): ", 15));
            c.setStreet(DataEntry.getLimitedString("Street (max 20): ", 20));
            c.setCity(DataEntry.getLimitedString("City (max 20): ", 20));
            c.setState(DataEntry.getLimitedString("State (2 letters): ", 2));
            c.setZip(DataEntry.getNumericString("Zip Code (5 digits): ", 5));
            c.setPhone(DataEntry.getNumericString("Phone (10 digits): ", 10));

            customers.add(c);

            String another = DataEntry.getString("Add another customer? (Y/N): ");
            addMore = another.equalsIgnoreCase("Y");
        }

        // Display all customers
        System.out.println("\nAll Customer Information:");
        System.out.println("--------------------------------------------------------------");
        System.out.printf("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s%n",
                "CustID", "SSN", "LastName", "FirstName", "Street", "City", "State", "Zip", "Phone");
        System.out.println("--------------------------------------------------------------");

        for (Customer c : customers) {
            System.out.println(c);
        }

        System.out.println("\nThank you for using the Banking Application!");
    }
}

    }
}
