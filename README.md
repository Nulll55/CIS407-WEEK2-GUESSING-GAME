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
        if (num == null || num.trim().isEmpty()) {
            throw new IllegalArgumentException("Account number cannot be blank.");
        }
        if (num.length() > 5) {
            throw new IllegalArgumentException("Account number max length is 5.");
        }
        this.accountNumber = num;
    }

    public String getAccountNumber() { return accountNumber; }
    public double getBalance() { return balance; }
    public void setBalance(double b) { this.balance = b; }

    @Override
    public double balance() { return balance; }

    @Override
    public abstract void deposit(double amount, String date) throws Exception;

    @Override
    public abstract void withdrawal(double amount, String date) throws Exception;
    
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

    // Helper method to validate date format (used by all transaction methods)
    private void validateDate(String date) throws Exception {
        try {
            // Checks if the string can be parsed into a valid date (YYYY-MM-DD)
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
        balance -= SERVICE_FEE; // Apply service fee
    }

    @Override
    public void withdrawal(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Withdrawal must be positive.");

        balance -= amount;
        balance -= SERVICE_FEE; // Apply service fee

        // Check for overdraft AFTER the withdrawal and fee
        if (balance < 0) {
            balance -= OVERDRAFT_FEE; // Apply overdraft fee
        }
    }

    public void applyInterest(String date) throws Exception {
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

    // Helper method to validate date format
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
        balance -= SERVICE_FEE; // Apply service fee
    }

    @Override
    public void withdrawal(double amount, String date) throws Exception {
        validateDate(date);
        if (amount <= 0) throw new Exception("Withdrawal must be positive.");
        
        // CRITICAL: Check if the transaction + fee will cause an overdraft
        if (amount + SERVICE_FEE > balance) {
            throw new Exception("Savings accounts cannot be overdrawn. Withdrawal denied.");
        }
        
        // If the check passes, perform the transaction
        balance -= amount;
        balance -= SERVICE_FEE; // Apply service fee
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

    // Setters (getters are omitted for brevity but should be added if needed, 
    // though the GUI code uses the objects directly)
    public void setCustomerID(String customerID) { this.customerID = customerID; }
    public void setSsn(String ssn) { this.ssn = ssn; }
    public void setLastName(String lastName) { this.lastName = lastName; }
    public void setFirstName(String firstName) { this.firstName = firstName; }
    public void setStreet(String street) { this.street = street; }
    public void setCity(String city) { this.city = city; }
    public void setState(String state) { this.state = state; }
    public void setZip(String zip) { this.zip = zip; }
    public void setPhone(String phone) { this.phone = phone; }
    
    // Simple getters for display in the GUI class logic
    public String getCustomerID() { return customerID; }
    // ... other getters as needed

    @Override
    public String toString() {
        return String.format("%-8s %-10s %-15s %-15s %-20s %-15s %-5s %-5s %-10s",
            customerID, ssn, lastName, firstName, street, city, state, zip, phone);
    }
}

// ----- BankAcctApp.java (Updated) ------
package courseProject;

import javax.swing.SwingUtilities;

public class BankAcctApp {

    public static void main(String[] args) {
        System.out.println("Starting Banking Application GUI...");

        SwingUtilities.invokeLater(() -> {
            new BankGUI(); // Launch the GUI
        });
    }
}

// ----- BankGUI.java (New Class) ------
package courseProject;

import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JLabel;
import javax.swing.JButton;
import javax.swing.JTextField;
import javax.swing.JTextArea;
import javax.swing.JRadioButton;
import javax.swing.ButtonGroup;
import javax.swing.JComboBox;
import javax.swing.JOptionPane; // Useful for pop-up messages
import java.awt.BorderLayout;
import java.awt.GridLayout;
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import java.util.ArrayList;

public class BankGUI extends JFrame implements ActionListener { 

    // --- DATA STORAGE ---
    private ArrayList<Customer> customerList = new ArrayList<>();
    private ArrayList<Account> accountList = new ArrayList<>();

    // --- GUI COMPONENTS (Define the main components here) ---
    // Customer Fields
    private JTextField customerIdField;
    private JTextField ssnField;
    private JTextField lastNameField;
    // ... add all other JTextFields for address, phone, etc. ...

    // Account Fields
    private JRadioButton checkingRadio;
    private JRadioButton savingsRadio;
    private ButtonGroup accountTypeGroup;
    private JTextField accountNumberField;
    private JTextField startingBalanceField;

    // Transaction Fields
    private JTextField transactAccountField;
    private JTextField transactAmountField;
    private JTextField transactDateField;
    private JRadioButton depositRadio;
    private JRadioButton withdrawalRadio;
    private ButtonGroup transactionTypeGroup;

    // Buttons
    private JButton addCustomerButton;
    private JButton displayButton;
    private JButton clearFieldsButton;
    private JButton performTransactionButton;
    private JButton applyInterestButton;
    
    // Output Areas
    private JTextArea actionOutputArea; // For "SUCCESS! Customer added" messages
    private JLabel lastTransactionLabel; // For displaying last transaction info
    private JLabel balanceLabel; // For displaying the current balance

    // --- CONSTRUCTOR: Build the GUI ---
    public BankGUI() {
        // 1. JFrame Setup
        super("Banking Application - Customer and Transaction Manager");
        this.setSize(900, 650);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); 
        this.setLayout(new BorderLayout(10, 10)); // Use a main layout

        // 2. Initialize Components (example)
        customerIdField = new JTextField(5);
        addCustomerButton = new JButton("Add New Customer and Account");
        
        // 3. Assemble Panels (You will define panels for each section later)
        JPanel mainPanel = new JPanel(new GridLayout(1, 2, 10, 10));
        
        // Example: Setup the Actions/Transactions area (Top right)
        actionOutputArea = new JTextArea(3, 20);
        actionOutputArea.setEditable(false);
        // ... (You will need many more panels for the layout shown in your screenshots) ...

        // 4. Register Listeners
        addCustomerButton.addActionListener(this);
        performTransactionButton.addActionListener(this);
        // ... register all other buttons ...
        
        // 5. Add Panels to Frame
        // Example: this.add(mainPanel, BorderLayout.CENTER);
        
        // 6. Finalize
        this.pack(); // Adjusts size to fit components (or use setSize if preferred)
        this.setLocationRelativeTo(null); // Centers the window
        this.setVisible(true); 
    }

    // --- ACTIONLISTENER IMPLEMENTATION (Handles button clicks) ---
    @Override
    public void actionPerformed(ActionEvent e) {
        // Determine which button was clicked
        if (e.getSource() == addCustomerButton) {
            handleAddCustomer();
        } else if (e.getSource() == performTransactionButton) {
            handlePerformTransaction();
        } 
        // ... add logic for other buttons ...
    }
    
    // --- HANDLER METHODS (Core logic hooks) ---
    private void handleAddCustomer() {
        // 1. Get user input from all fields (Customer ID, SSN, Balances, etc.)
        String custId = customerIdField.getText();
        
        try {
            // 2. Instantiate new Customer and Account objects (using CheckingAccount or SavingsAccount)
            // Example: Account newAccount = new CheckingAccount(startingBalance);
            // Example: Customer newCustomer = new Customer();
            
            // 3. Add to lists
            // customerList.add(newCustomer);
            // accountList.add(newAccount);
            
            // 4. Update UI
            actionOutputArea.setText("SUCCESS! Customer and Account added.");
            
        } catch (Exception ex) {
            // 5. Handle errors
            JOptionPane.showMessageDialog(this, "Error adding customer: " + ex.getMessage(), "Input Error", JOptionPane.ERROR_MESSAGE);
        }
    }
    
    private void handlePerformTransaction() {
        // Logic to process deposits or withdrawals
        // 1. Find the correct Account object from accountList using the Account #
        // 2. Call account.deposit() or account.withdrawal()
        // 3. Handle Exceptions thrown by the Account objects (e.g., overdraft denied)
        // 4. Update balanceLabel and lastTransactionLabel
    }
    
    private void handleApplyInterest() {
        // Logic to apply interest to a selected account
        // 1. Find the Account object
        // 2. Call checkingAccount.applyInterest() or savingsAccount.applyInterest()
        // 3. Update balanceLabel
    }
    
    private void clearAllFields() {
        // Logic to clear all text fields
    }


    // The main method is now in BankAcctApp.java, keeping this file clean!
}
