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
// ----- SavingsAccount.java (COMPLETE) ------
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
// ----- Customer.java (Final & Complete) -----
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
    
    public String getCustomerID() { return customerID; }
    
    @Override
    public String toString() {
        // Used by handleDisplayData() to format the data
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

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import java.util.ArrayList;
import java.time.LocalDate;
import java.time.format.DateTimeParseException;
import java.text.DecimalFormat; 

public class BankGUI extends JFrame implements ActionListener { 

    private ArrayList<Customer> customerList = new ArrayList<>();
    private ArrayList<Account> accountList = new ArrayList<>();
    private DecimalFormat currencyFormatter = new DecimalFormat("0.00");

    private JTextField customerIdField = new JTextField(5);
    private JTextField ssnField = new JTextField(9);
    private JTextField lastNameField = new JTextField(20);
    private JTextField firstNameField = new JTextField(15);
    private JTextField streetField = new JTextField(20);
    private JTextField cityField = new JTextField(20);
    private JComboBox<String> stateComboBox;
    private JTextField zipField = new JTextField(5);
    private JTextField phoneField = new JTextField(10);

    private JRadioButton checkingRadio = new JRadioButton("Checking (CHK)");
    private JRadioButton savingsRadio = new JRadioButton("Savings (SAV)");
    private ButtonGroup accountTypeGroup = new ButtonGroup();
    private JTextField accountNumberField = new JTextField(5);
    private JTextField startingBalanceField = new JTextField(10);
    
    private JTextField transactAccountField = new JTextField(5);
    private JTextField transactAmountField = new JTextField(10);
    private JTextField transactDateField = new JTextField("YYYY-MM-DD", 10);
    private JRadioButton depositRadio = new JRadioButton("Deposit");
    private JRadioButton withdrawalRadio = new JRadioButton("Withdrawal");
    private ButtonGroup transactionTypeGroup = new ButtonGroup();
    
    private JButton addCustomerButton;
    private JButton displayButton;
    private JButton clearFieldsButton;
    private JButton performTransactionButton;
    private JButton applyInterestButton;
    
    private JTextArea actionOutputArea; 
    private JLabel lastTransactionLabel; 
    private JLabel balanceLabel; 
    private JTextField lastTransactionCustIdField = new JTextField(5);
    private JTextField lastTransactionAcctField = new JTextField(5);
    private JTextField lastTransactionAmtField = new JTextField(10);
    private JTextField lastTransactionTypeField = new JTextField(5);
    private JTextField lastTransactionFeesField = new JTextField(5);
    private JTextField lastTransactionDateField = new JTextField(10);

    public BankGUI() {
        super("Banking Application - Customer and Transaction Manager");
        
        this.setSize(950, 700);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); 
        this.setLayout(new BorderLayout(10, 10)); 
        
        addCustomerButton = new JButton("Add New Customer and Account");
        displayButton = new JButton("Display Customer/Account Data");
        clearFieldsButton = new JButton("Clear All Fields");
        performTransactionButton = new JButton("Perform Transaction");
        applyInterestButton = new JButton("Apply Interest Earned");
        
        actionOutputArea = new JTextArea(1, 40); 
        actionOutputArea.setText("Application Ready, Enter new customer data.");
        actionOutputArea.setEditable(false);
        actionOutputArea.setBackground(new Color(200, 220, 255)); 
        
        String[] states = {"AL", "AK", "AZ", "CA", "NY", "TX", "VA", "WA"};
        stateComboBox = new JComboBox<>(states);

        accountTypeGroup.add(checkingRadio);
        accountTypeGroup.add(savingsRadio);
        checkingRadio.setSelected(true);

        transactionTypeGroup.add(depositRadio);
        transactionTypeGroup.add(withdrawalRadio);
        depositRadio.setSelected(true);
        
        lastTransactionLabel = new JLabel("Last Transaction / Account Info");
        balanceLabel = new JLabel("Balance: $" + currencyFormatter.format(0.00));
        
        lastTransactionCustIdField.setEditable(false);
        lastTransactionAcctField.setEditable(false);
        lastTransactionAmtField.setEditable(false);
        lastTransactionTypeField.setEditable(false);
        lastTransactionFeesField.setEditable(false);
        lastTransactionDateField.setEditable(false);
        
        JPanel topPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
        topPanel.add(actionOutputArea);
        this.add(topPanel, BorderLayout.NORTH);

        JPanel mainContent = new JPanel(new GridLayout(1, 2, 15, 15));
        
        JPanel leftPanel = new JPanel(new BorderLayout(5, 5));
        JPanel customerGrid = createCustomerAccountGrid(); 
        customerGrid.setBorder(BorderFactory.createTitledBorder("New Customer & Account Data"));
        leftPanel.add(customerGrid, BorderLayout.NORTH);
        
        JPanel lastTransactionPanel = createLastTransactionPanel();
        lastTransactionPanel.setBorder(BorderFactory.createTitledBorder("Last Transaction / Account Info"));
        leftPanel.add(lastTransactionPanel, BorderLayout.CENTER); 
        mainContent.add(leftPanel);
        
        JPanel rightPanel = new JPanel(new BorderLayout(5, 5));
        JPanel actionOutputPanel = createActionOutputPanel();
        actionOutputPanel.setBorder(BorderFactory.createTitledBorder("Actions & Transactions"));
        rightPanel.add(actionOutputPanel, BorderLayout.NORTH);
        
        JPanel transactPanel = createTransactionPanel();
        transactPanel.setBorder(BorderFactory.createTitledBorder("Perform Transaction"));
        rightPanel.add(transactPanel, BorderLayout.CENTER);
        mainContent.add(rightPanel);
        
        this.add(mainContent, BorderLayout.CENTER);
        
        addCustomerButton.addActionListener(this);
        displayButton.addActionListener(this);
        clearFieldsButton.addActionListener(this);
        performTransactionButton.addActionListener(this);
        applyInterestButton.addActionListener(this);
        
        this.setLocationRelativeTo(null); 
        this.setVisible(true); 
    }
    
    // --- FIXED: Changed GridLayout to 12 rows to account for the single radio button row ---
    private JPanel createCustomerAccountGrid() {
        JPanel panel = new JPanel(new GridLayout(12, 2, 5, 5)); // Changed from 13 to 12 rows
        
        panel.add(new JLabel("Customer ID (max 5):")); panel.add(customerIdField);
        panel.add(new JLabel("SSN (9 digits):")); panel.add(ssnField);
        panel.add(new JLabel("Last Name (max 20):")); panel.add(lastNameField);
        panel.add(new JLabel("First Name (max 15):")); panel.add(firstNameField);
        panel.add(new JLabel("Street (max 20):")); panel.add(streetField);
        panel.add(new JLabel("City (max 20):")); panel.add(cityField);
        panel.add(new JLabel("State (2 letters):")); panel.add(stateComboBox);
        panel.add(new JLabel("Zip Code (5 digits):")); panel.add(zipField);
        panel.add(new JLabel("Phone (10 digits):")); panel.add(phoneField);
        
        // This JPanel now holds both radio buttons in one logical row
        JPanel radioPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        radioPanel.add(checkingRadio); 
        radioPanel.add(savingsRadio); // <-- The previously hidden button
        
        panel.add(new JLabel("Account Type:")); 
        panel.add(radioPanel); // <-- Adds the panel containing both radio buttons
        
        panel.add(new JLabel("Account Number (max 5):")); panel.add(accountNumberField);
        panel.add(new JLabel("Starting Balance:")); panel.add(startingBalanceField);
        return panel;
    }
    
    private JPanel createActionOutputPanel() {
        JPanel panel = new JPanel(new BorderLayout(5, 5));
        
        JPanel buttonPanel = new JPanel(new GridLayout(3, 1, 5, 5));
        buttonPanel.add(addCustomerButton);
        buttonPanel.add(displayButton);
        buttonPanel.add(clearFieldsButton);
        
        panel.add(buttonPanel, BorderLayout.NORTH);
        return panel;
    }
    
    private JPanel createTransactionPanel() {
        JPanel panel = new JPanel(new GridLayout(5, 2, 5, 5));
        
        panel.add(new JLabel("Account # to Transact:")); panel.add(transactAccountField);
        panel.add(new JLabel("Transaction Amount:")); panel.add(transactAmountField);
        panel.add(new JLabel("Transaction Date:")); panel.add(transactDateField);
        
        JPanel typeRadioPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        typeRadioPanel.add(depositRadio); typeRadioPanel.add(withdrawalRadio);
        panel.add(new JLabel("Transaction Type:")); panel.add(typeRadioPanel);
        
        JPanel transactButtonPanel = new JPanel(new GridLayout(1, 2, 10, 10));
        transactButtonPanel.add(performTransactionButton);
        transactButtonPanel.add(applyInterestButton);
        panel.add(new JLabel("")); 
        panel.add(transactButtonPanel);

        return panel;
    }
    
    private JPanel createLastTransactionPanel() {
        JPanel panel = new JPanel(new BorderLayout(5, 5));
        panel.add(lastTransactionLabel, BorderLayout.NORTH);
        
        JPanel infoGrid = new JPanel(new GridLayout(2, 6, 5, 5));
        
        infoGrid.add(new JLabel("Cust ID:")); infoGrid.add(new JLabel("Acct #:"));
        infoGrid.add(new JLabel("Amt:")); infoGrid.add(new JLabel("Type:"));
        infoGrid.add(new JLabel("Fees:")); infoGrid.add(new JLabel("Date:"));
        
        infoGrid.add(lastTransactionCustIdField); infoGrid.add(lastTransactionAcctField);
        infoGrid.add(lastTransactionAmtField); infoGrid.add(lastTransactionTypeField);
        infoGrid.add(lastTransactionFeesField); infoGrid.add(lastTransactionDateField);
        
        panel.add(infoGrid, BorderLayout.CENTER);
        panel.add(balanceLabel, BorderLayout.SOUTH);
        
        return panel;
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        actionOutputArea.setText("Processing request...");
        try {
            if (e.getSource() == addCustomer
