----- AccountInterface.java-------
package courseProject;

public interface AccountInterface {
    void deposit(double amount, String date) throws Exception;
    void withdrawal(double amount, String date) throws Exception;
    double balance(); // returns current balance
}

----- Account.Java------
package courseProject;

public abstract class Account implements AccountInterface {
    protected String accountNumber;
    protected double balance;

    public Account(double startingBalance) {
        this.balance = startingBalance;
    }

    public void setAccountNumber(String num) {
        if (num == null || num.trim().isEmpty()) throw new IllegalArgumentException("Account number cannot be blank.");
        if (num.length() > 5) throw new IllegalArgumentException("Account number max length is 5.");
        this.accountNumber = num;
    }

    public String getAccountNumber() { return accountNumber; }
    public double getBalance() { return balance; }
    public void setBalance(double b) { this.balance = b; }

    @Override
    public double balance() { return balance; }

    @Override
    public String toString() {
        return String.format("Account Number: %s | Balance: %.2f", 
                accountNumber == null ? "N/A" : accountNumber, balance);
    }
}

------- CheckingAccount.java ------
package courseProject;

import java.time.LocalDate;
import java.time.format.DateTimeParseException;

public class CheckingAccount extends Account {

    private final double SERVICE_FEE = 0.50;
    private final double OVERDRAFT_FEE = 30.00;
    private final double INTEREST_RATE = 0.02; // 2%

    public CheckingAccount(double startingBalance) {
        super(startingBalance);
    }

    // validate date format (YYYY-MM-DD)
    private void validateDate(String date) throws Exception {
        try {
            LocalDate.parse(date);
        } catch (DateTimeParseException ex) {
            throw new Exception("Invalid date. Use YYYY-MM-DD.");
        }
    }

    // type must be "DEP" or "WTH" - caller should ensure but we check in scenario
    @Override
    public void deposit(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Deposit must be positive.");

        balance += amount;
        balance -= SERVICE_FEE;
        // print handled by caller; keep logic here
    }

    @Override
    public void withdrawal(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Withdrawal must be positive.");

        balance -= amount;
        balance -= SERVICE_FEE;

        if (balance < 0) {
            balance -= OVERDRAFT_FEE;
        }
    }

    public void applyInterest(String date) throws Exception {
        // date validated for reporting consistency
        validateDate(date);
        double interest = balance * INTEREST_RATE;
        balance += interest;
    }
}
-----SavingAccount------
package courseProject;

import java.time.LocalDate;
import java.time.format.DateTimeParseException;

public class SavingsAccount extends Account {

    private final double SERVICE_FEE = 0.25;
    private final double INTEREST_RATE = 0.05; // 5%

    public SavingsAccount(double startingBalance) {
        super(startingBalance);
    }

    private void validateDate(String date) throws Exception {
        try {
            LocalDate.parse(date);
        } catch (DateTimeParseException ex) {
            throw new Exception("Invalid date. Use YYYY-MM-DD.");
        }
    }

    @Override
    public void deposit(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Deposit must be positive.");
        balance += amount;
        balance -= SERVICE_FEE;
    }

    @Override
    public void withdrawal(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Withdrawal must be positive.");
        if (amount + SERVICE_FEE > balance) {
            throw new Exception("Savings accounts cannot be overdrawn. Withdrawal denied.");
        }
        balance -= amount;
        balance -= SERVICE_FEE;
    }

    public void applyInterest(String date) throws Exception {
        validateDate(date);
        double interest = balance * INTEREST_RATE;
        balance += interest;
    }
}
------Customer.java-----
package courseProject;

public class Customer {
    private String customerID;
    private String ssn;
    private String lastName;
    private String firstName;
    private String street;
    private String city;
    private String state;
    private String zip;
    private String phone;

    public void setCustomerID(String customerID) { this.customerID = customerID; }
    public void setSsn(String ssn) { this.ssn = ssn; }
    public void setLastName(String lastName) { this.lastName = lastName; }
    public void setFirstName(String firstName) { this.firstName = firstName; }
    public void setStreet(String street) { this.street = street; }
    public void setCity(String city) { this.city = city; }
    public void setState(String state) { this.state = state; }
    public void setZip(String zip) { this.zip = zip; }
    public void setPhone(String phone) { this.phone = phone; }

    @Override
    public String toString() {
        return String.format("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s",
            customerID, ssn, lastName, firstName, street, city, state, zip, phone);
    }
}

-----DataEntry.Java-------
package courseProject;

import java.util.Scanner;

public class DataEntry {
    private static Scanner sc = new Scanner(System.in);

    public static String getString(String prompt) {
        System.out.print(prompt);
        return sc.nextLine().trim();
    }

    public static String getLimitedString(String prompt, int maxLen) {
        System.out.print(prompt);
        String s = sc.nextLine().trim();
        if (s.isEmpty()) throw new IllegalArgumentException("Input cannot be blank.");
        if (s.length() > maxLen) throw new IllegalArgumentException("Input cannot exceed " + maxLen + " characters.");
        return s;
    }

    public static String getNumericString(String prompt, int length) {
        System.out.print(prompt);
        String s = sc.nextLine().trim();
        if (s.isEmpty()) throw new IllegalArgumentException("Input cannot be blank.");
        if (!s.matches("\\d+")) throw new IllegalArgumentException("Input must be numeric.");
        if (s.length() != length) throw new IllegalArgumentException("Input must be exactly " + length + " digits.");
        return s;
    }

    public static double getDouble(String prompt) {
        System.out.print(prompt);
        String line = sc.nextLine().trim();
        try {
            return Double.parseDouble(line);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Enter a valid decimal number.");
        }
    }
}

------ bankAcctApp --------
package courseProject;

import java.util.ArrayList;

public class BankAcctApp {

    public static void main(String[] args) {

        ArrayList<Customer> customers = new ArrayList<>();
        ArrayList<Account> accounts = new ArrayList<>();

        System.out.println("Welcome to the Banking Application - Phase 3");
        System.out.println("--------------------------------------------");

        boolean addMore = true;
        while (addMore) {
            Customer c = new Customer();
            Account a = null;

            System.out.println("\nEnter new customer information:");

            // customer fields
            while (true) {
                try { c.setCustomerID(DataEntry.getLimitedString("Customer ID (max 5): ", 5)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setSsn(DataEntry.getNumericString("Customer SSN (9 digits): ", 9)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setLastName(DataEntry.getLimitedString("Last Name (max 20): ", 20)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setFirstName(DataEntry.getLimitedString("First Name (max 15): ", 15)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setStreet(DataEntry.getLimitedString("Street (max 20): ", 20)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setCity(DataEntry.getLimitedString("City (max 20): ", 20)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setState(DataEntry.getLimitedString("State (2 letters): ", 2)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setZip(DataEntry.getNumericString("Zip Code (5 digits): ", 5)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }
            while (true) {
                try { c.setPhone(DataEntry.getNumericString("Phone (10 digits): ", 10)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }

            // account selection
            String acctType = null;
            while (true) {
                try {
                    acctType = DataEntry.getLimitedString("Account Type (CHK or SAV): ", 3);
                    if (!acctType.equalsIgnoreCase("CHK") && !acctType.equalsIgnoreCase("SAV"))
                        throw new IllegalArgumentException("Account type must be CHK or SAV.");
                    break;
                } catch (Exception e) {
                    System.out.println("Error: " + e.getMessage());
                }
            }

            double startBal = 0;
            while (true) {
                try { startBal = DataEntry.getDouble("Starting Balance: "); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }

            if (acctType.equalsIgnoreCase("CHK")) a = new CheckingAccount(startBal);
            else a = new SavingsAccount(startBal);

            while (true) {
                try { a.setAccountNumber(DataEntry.getLimitedString("Account Number (max 5): ", 5)); break; }
                catch (Exception e) { System.out.println("Error: " + e.getMessage()); }
            }

            customers.add(c);
            accounts.add(a);

            System.out.println("\nCustomer and Account added successfully!");
            System.out.println("--------------------------------------------");
            System.out.println(c);
            System.out.println(a);

            String ans = DataEntry.getString("\nAdd another customer? (Y/N): ");
            addMore = ans.equalsIgnoreCase("Y");
        }

        // display lists
        System.out.println("\nAll Customer Information:");
        System.out.println("--------------------------------------------------------------------------------------------");
        System.out.printf("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s%n",
                "CustID", "SSN", "LastName", "FirstName", "Street", "City", "State", "Zip", "Phone");
        System.out.println("--------------------------------------------------------------------------------------------");
        for (Customer c : customers) System.out.println(c);

        System.out.println("\nAccount Details:");
        System.out.printf("%-12s %-6s %-12s %-12s%n", "Acct#", "Type", "Balance", "Class");
        System.out.println("--------------------------------------------------------------");
        for (Account acc : accounts) {
            System.out.printf("%-12s %-6s %-12.2f %-12s%n",
                    acc.getAccountNumber(),
                    acc instanceof CheckingAccount ? "CHK" : "SAV",
                    acc.getBalance(),
                    acc.getClass().getSimpleName());
        }

        // ----------------- Perform required transaction scenario -----------------
        // Find one checking and one savings (per project step 4/5)
        CheckingAccount chk = null;
        SavingsAccount sav = null;
        Customer chkCust = null;
        Customer savCust = null;

        for (int i = 0; i < accounts.size(); i++) {
            Account acc = accounts.get(i);
            if (chk == null && acc instanceof CheckingAccount) { chk = (CheckingAccount) acc; chkCust = customers.get(i); }
            if (sav == null && acc instanceof SavingsAccount) { sav = (SavingsAccount) acc; savCust = customers.get(i); }
        }

        if (chk == null || sav == null) {
            System.out.println("\nSkipping transaction scenario: need at least one checking and one savings account.");
            System.out.println("Make sure you created both account types.");
            return;
        }

        // header for transaction log
        System.out.println("\nTransaction Log:");
        System.out.printf("%-8s %-8s %-4s %-12s %-4s %-10s %-12s %-12s%n",
                "CustID", "Acct#", "Typ", "TransDate", "TT", "Amount", "Fees/Notes", "Balance");
        System.out.println("-----------------------------------------------------------------------------------------");

        // Helper lambda-like local calls (we'll just write explicit steps)
        try {
            // 1. Deposit $1000 into checking
            chk.deposit(1000.0, "2025-11-28");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    chkCust == null ? "N/A" : chkCust.toString().split(" ")[0],
                    chk.getAccountNumber(),"CHK","2025-11-28","DEP",1000.0,"SvcFee $0.50", chk.getBalance());

            // 2. Withdraw $500 from checking
            chk.withdrawal(500.0, "2025-11-29");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    chkCust == null ? "N/A" : chkCust.toString().split(" ")[0],
                    chk.getAccountNumber(),"CHK","2025-11-29","WTH",500.0,"SvcFee $0.50", chk.getBalance());

            // 3. Withdraw $501 from checking (causes overdraft & $30)
            chk.withdrawal(501.0, "2025-11-30");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    chkCust == null ? "N/A" : chkCust.toString().split(" ")[0],
                    chk.getAccountNumber(),"CHK","2025-11-30","WTH",501.0,"SvcFee + Overdraft", chk.getBalance());

            // 4. Deposit $500 into checking
            chk.deposit(500.0, "2025-12-01");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    chkCust == null ? "N/A" : chkCust.toString().split(" ")[0],
                    chk.getAccountNumber(),"CHK","2025-12-01","DEP",500.0,"SvcFee $0.50", chk.getBalance());

            // 5. Add interest to checking
            chk.applyInterest("2025-12-02");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s %-10s %-12s $%-11.2f%n",
                    chkCust == null ? "N/A" : chkCust.toString().split(" ")[0],
                    chk.getAccountNumber(),"CHK","2025-12-02","INT",0.0,"Interest applied", chk.getBalance());

            // 6. Deposit $1000 into savings
            sav.deposit(1000.0, "2025-11-28");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                    sav.getAccountNumber(),"SAV","2025-11-28","DEP",1000.0,"SvcFee $0.25", sav.getBalance());

            // 7. Withdraw $500 from savings
            try {
                sav.withdrawal(500.0, "2025-11-29");
                System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                        savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                        sav.getAccountNumber(),"SAV","2025-11-29","WTH",500.0,"SvcFee $0.25", sav.getBalance());
            } catch (Exception ex) {
                System.out.printf("%-8s %-8s %-4s %-12s %-4s %-10s %-12s %-12s%n",
                        savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                        sav.getAccountNumber(),"SAV","2025-11-29","WTH",500.0,"Denied", sav.getBalance());
            }

            // 8. Withdraw $501 from savings (should be denied if insufficient)
            try {
                sav.withdrawal(501.0, "2025-11-30");
                System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                        savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                        sav.getAccountNumber(),"SAV","2025-11-30","WTH",501.0,"SvcFee $0.25", sav.getBalance());
            } catch (Exception ex) {
                System.out.printf("%-8s %-8s %-4s %-12s %-4s %-10s %-12s %-12s%n",
                        savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                        sav.getAccountNumber(),"SAV","2025-11-30","WTH",501.0,"Denied", sav.getBalance());
            }

            // 9. Deposit $500 into savings
            sav.deposit(500.0, "2025-12-01");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s $%-9.2f %-12s $%-11.2f%n",
                    savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                    sav.getAccountNumber(),"SAV","2025-12-01","DEP",500.0,"SvcFee $0.25", sav.getBalance());

            // 10. Add interest to savings
            sav.applyInterest("2025-12-02");
            System.out.printf("%-8s %-8s %-4s %-12s %-4s %-10s %-12s $%-11.2f%n",
                    savCust == null ? "N/A" : savCust.toString().split(" ")[0],
                    sav.getAccountNumber(),"SAV","2025-12-02","INT",0.0,"Interest applied", sav.getBalance());

        } catch (Exception e) {
            System.out.println("Transaction error: " + e.getMessage());
        }

        System.out.println("\nAll done — program complete.");
    }
}

