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

    // This validation logic is still used by the GUI to catch errors
    public void setAccountNumber(String num) {
        if (num == null || num.trim().isEmpty()) 
            throw new IllegalArgumentException("Account number cannot be blank.");
        if (num.length() > 5) 
            throw new IllegalArgumentException("Account number max length is 5.");
        this.accountNumber = num;
    }

    // The GUI calls these methods to update the "Balance" labels
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

        balance -= amount;
        balance -= SERVICE_FEE;

        if (balance < 0) {
            balance -= OVERDRAFT_FRAFT_FEE;
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

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.util.ArrayList;

public class BankGUI extends JFrame {

    // --- Data Storage (Backend) ---
    private ArrayList<Customer> customers = new ArrayList<>();
    private ArrayList<Account> accounts = new ArrayList<>();
    private final String[] STATES = {"AL", "CA", "FL", "NY", "TX", "WA"}; // At least 5 states

    // --- GUI Components (Inputs) ---
    private JTextField txtCustomerID, txtSSN, txtLastName, txtFirstName, txtStreet, 
                       txtCity, txtZip, txtPhone, txtAcctNum, txtStartBal;
    private JComboBox<String> cmbState;
    private JRadioButton rdoChecking, rdoSavings;
    private ButtonGroup acctTypeGroup;

    // --- Transaction Components ---
    private JTextField txtTransAmount, txtTransDate;
    private JRadioButton rdoDeposit, rdoWithdrawal;
    private ButtonGroup transTypeGroup;
    private JTextField txtSearchAcctNum; // For search/transaction input

    // --- Output Components ---
    private JLabel lblStatus;
    private JLabel lblOutCustID, lblOutAcctNum, lblOutAcctType, lblOutDate, 
                   lblOutTransType, lblOutAmount, lblOutFees, lblOutBalance;

    public BankGUI() {
        setTitle("Banking Application - Customer and Transaction Manager");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10)); // Main layout

        // 1. Initialize Components
        initializeInputs();
        initializeOutputs();
        
        // 2. Set up Layouts (CENTER and SOUTH)
        add(createInputPanel(), BorderLayout.CENTER);
        add(createOutputPanel(), BorderLayout.SOUTH);
        
        // 3. Set up Status (NORTH)
        lblStatus = new JLabel("Application Ready. Enter new customer data.");
        lblStatus.setHorizontalAlignment(SwingConstants.CENTER);
        lblStatus.setBorder(BorderFactory.createEmptyBorder(5, 5, 5, 5));
        add(lblStatus, BorderLayout.NORTH);

        pack();
        setLocationRelativeTo(null);
        setVisible(true);
    }
    
    // --- Initialization Methods ---
    
    private void initializeInputs() {
        txtCustomerID = new JTextField(5);
        txtSSN = new JTextField(9);
        txtLastName = new JTextField(15);
        txtFirstName = new JTextField(15);
        txtStreet = new JTextField(20);
        txtCity = new JTextField(15);
        txtZip = new JTextField(5);
        txtPhone = new JTextField(10);
        
        cmbState = new JComboBox<>(STATES);
        
        rdoChecking = new JRadioButton("Checking (CHK)");
        rdoSavings = new JRadioButton("Savings (SAV)");
        acctTypeGroup = new ButtonGroup();
        acctTypeGroup.add(rdoChecking);
        acctTypeGroup.add(rdoSavings);
        rdoChecking.setSelected(true); // Default selection

        txtAcctNum = new JTextField(5);
        txtStartBal = new JTextField(10);
        
        txtSearchAcctNum = new JTextField(8);
        txtTransAmount = new JTextField(8);
        txtTransDate = new JTextField("YYYY-MM-DD", 10);
        
        rdoDeposit = new JRadioButton("Deposit", true);
        rdoWithdrawal = new JRadioButton("Withdrawal");
        transTypeGroup = new ButtonGroup();
        transTypeGroup.add(rdoDeposit);
        transTypeGroup.add(rdoWithdrawal);
    }
    
    private void initializeOutputs() {
        lblOutCustID = new JLabel("Cust ID:");
        lblOutAcctNum = new JLabel("Acct #:");
        lblOutAcctType = new JLabel("Type:");
        lblOutDate = new JLabel("Date:");
        lblOutTransType = new JLabel("TT:");
        lblOutAmount = new JLabel("Amt:");
        lblOutFees = new JLabel("Fees:");
        lblOutBalance = new JLabel("Balance: $0.00");
        lblOutBalance.setFont(new Font("SansSerif", Font.BOLD, 14));
    }

    // --- Layout Methods ---

    private JPanel createInputPanel() {
        JPanel mainPanel = new JPanel(new GridLayout(1, 2, 20, 10)); // Two columns: Customer & Transaction
        
        // Left Side: Customer & Account Creation (10 rows, 2 columns)
        JPanel customerPanel = new JPanel(new GridLayout(12, 2, 5, 5));
        customerPanel.setBorder(BorderFactory.createTitledBorder("New Customer & Account Data"));
        
        customerPanel.add(new JLabel("Customer ID (max 5):")); customerPanel.add(txtCustomerID);
        customerPanel.add(new JLabel("SSN (9 digits):")); customerPanel.add(txtSSN);
        customerPanel.add(new JLabel("Last Name (max 20):")); customerPanel.add(txtLastName);
        customerPanel.add(new JLabel("First Name (max 15):")); customerPanel.add(txtFirstName);
        customerPanel.add(new JLabel("Street (max 20):")); customerPanel.add(txtStreet);
        customerPanel.add(new JLabel("City (max 20):")); customerPanel.add(txtCity);
        
        customerPanel.add(new JLabel("State (2 letters):")); customerPanel.add(cmbState);
        customerPanel.add(new JLabel("Zip Code (5 digits):")); customerPanel.add(txtZip);
        customerPanel.add(new JLabel("Phone (10 digits):")); customerPanel.add(txtPhone);
        
        customerPanel.add(new JLabel("Account Type:"));
        JPanel radioPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        radioPanel.add(rdoChecking); radioPanel.add(rdoSavings);
        customerPanel.add(radioPanel);
        
        customerPanel.add(new JLabel("Account Number (max 5):")); customerPanel.add(txtAcctNum);
        customerPanel.add(new JLabel("Starting Balance:")); customerPanel.add(txtStartBal);
        
        // Right Side: Transactions and Action Buttons
        JPanel actionPanel = new JPanel(new BorderLayout(10, 10));
        actionPanel.setBorder(BorderFactory.createTitledBorder("Actions & Transactions"));
        
        // Top of Action Panel: Action Buttons
        JPanel buttonPanel = new JPanel(new GridLayout(3, 1, 10, 10));
        JButton btnAddCustomer = new JButton("Add New Customer and Account");
        JButton btnDisplay = new JButton("Display Customer/Account Data");
        JButton btnClear = new JButton("Clear All Fields");
        
        buttonPanel.add(btnAddCustomer);
        buttonPanel.add(btnDisplay);
        buttonPanel.add(btnClear);
        actionPanel.add(buttonPanel, BorderLayout.NORTH);
        
        // Center of Action Panel: Transaction Input
        JPanel transPanel = new JPanel(new GridLayout(5, 2, 5, 5));
        transPanel.setBorder(BorderFactory.createTitledBorder("Perform Transaction"));
        
        transPanel.add(new JLabel("Account # to Transact:")); transPanel.add(txtSearchAcctNum);
        transPanel.add(new JLabel("Transaction Amount:")); transPanel.add(txtTransAmount);
        transPanel.add(new JLabel("Transaction Date:")); transPanel.add(txtTransDate);

        transPanel.add(new JLabel("Transaction Type:"));
        JPanel transRadioPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        transRadioPanel.add(rdoDeposit); transRadioPanel.add(rdoWithdrawal);
        transPanel.add(transRadioPanel);

        JButton btnPerformTrans = new JButton("Perform Transaction");
        transPanel.add(btnPerformTrans);
        transPanel.add(new JButton("Apply Interest Earned")); // New button for interest
        actionPanel.add(transPanel, BorderLayout.CENTER);
        
        mainPanel.add(customerPanel);
        mainPanel.add(actionPanel);
        
        // --- Register Listeners ---
        btnAddCustomer.addActionListener(this::addNewCustomer);
        btnDisplay.addActionListener(this::displayCustomer);
        btnClear.addActionListener(this::clearFields);
        btnPerformTrans.addActionListener(this::performTransaction);
        
        // Find the "Apply Interest Earned" button and register its listener
        ((JButton) transPanel.getComponent(9)).addActionListener(this::applyInterest);

        return mainPanel;
    }
    
    private JPanel createOutputPanel() {
        JPanel outputPanel = new JPanel(new GridLayout(2, 4, 10, 5));
        outputPanel.setBorder(BorderFactory.createTitledBorder("Last Transaction / Account Info"));
        
        outputPanel.add(lblOutCustID);
        outputPanel.add(lblOutAcctNum);
        outputPanel.add(lblOutAcctType);
        outputPanel.add(lblOutDate);
        
        outputPanel.add(lblOutTransType);
        outputPanel.add(lblOutAmount);
        outputPanel.add(lblOutFees);
        outputPanel.add(lblOutBalance);
        
        return outputPanel;
    }

    // --- Core Logic Methods ---

    private void setStatus(String message, boolean isError) {
        lblStatus.setText(message);
        lblStatus.setForeground(isError ? Color.RED : Color.BLUE);
    }
    
    private void clearFields(ActionEvent e) {
        txtCustomerID.setText("");
        txtSSN.setText("");
        txtLastName.setText("");
        txtFirstName.setText("");
        txtStreet.setText("");
        txtCity.setText("");
        cmbState.setSelectedIndex(0);
        txtZip.setText("");
        txtPhone.setText("");
        txtAcctNum.setText("");
        txtStartBal.setText("");
        txtTransAmount.setText("");
        txtTransDate.setText("YYYY-MM-DD");
        txtSearchAcctNum.setText("");
        
        // Clear outputs
        lblOutCustID.setText("Cust ID:");
        lblOutAcctNum.setText("Acct #:");
        lblOutAcctType.setText("Type:");
        lblOutDate.setText("Date:");
        lblOutTransType.setText("TT:");
        lblOutAmount.setText("Amt:");
        lblOutFees.setText("Fees:");
        lblOutBalance.setText("Balance: $0.00");
        setStatus("All fields cleared.", false);
    }

    private void addNewCustomer(ActionEvent e) {
        Customer c = new Customer();
        Account a = null;
        double startBal = 0.0;
        
        try {
            // Validate and set Customer fields
            c.setCustomerID(validateLimitedString(txtCustomerID.getText(), "Customer ID", 5));
            c.setSsn(validateNumericString(txtSSN.getText(), "SSN", 9));
            c.setLastName(validateLimitedString(txtLastName.getText(), "Last Name", 20));
            c.setFirstName(validateLimitedString(txtFirstName.getText(), "First Name", 15));
            c.setStreet(validateLimitedString(txtStreet.getText(), "Street", 20));
            c.setCity(validateLimitedString(txtCity.getText(), "City", 20));
            c.setState((String) cmbState.getSelectedItem()); // ComboBox handles validation
            c.setZip(validateNumericString(txtZip.getText(), "Zip Code", 5));
            c.setPhone(validateNumericString(txtPhone.getText(), "Phone", 10));

            // Validate and instantiate Account
            startBal = validateDouble(txtStartBal.getText(), "Starting Balance");
            if (rdoChecking.isSelected()) {
                a = new CheckingAccount(startBal);
            } else if (rdoSavings.isSelected()) {
                a = new SavingsAccount(startBal);
            } else {
                throw new Exception("Please select an Account Type.");
            }

            // Validate and set Account Number
            a.setAccountNumber(validateLimitedString(txtAcctNum.getText(), "Account Number", 5));
            
            // Check for duplicate account number (simple check)
            for (Account existingAcct : accounts) {
                if (existingAcct.getAccountNumber().equals(a.getAccountNumber())) {
                    throw new Exception("Account number already exists.");
                }
            }

            // Add to lists
            customers.add(c);
            accounts.add(a);
            
            setStatus("SUCCESS! Customer " + c.toString().split(" ")[0] + " added with Account " + a.getAccountNumber(), false);
        } catch (Exception ex) {
            setStatus("Error adding customer: " + ex.getMessage(), true);
        }
    }
    
    private void displayCustomer(ActionEvent e) {
        String searchAcctNum = txtSearchAcctNum.getText().trim();
        try {
            if (searchAcctNum.isEmpty()) throw new Exception("Enter an Account # to display.");
            
            int index = -1;
            for (int i = 0; i < accounts.size(); i++) {
                if (accounts.get(i).getAccountNumber().equals(searchAcctNum)) {
                    index = i;
                    break;
                }
            }

            if (index == -1) throw new Exception("Account not found.");

            Customer c = customers.get(index);
            Account a = accounts.get(index);
            
            String acctType = a instanceof CheckingAccount ? "CHK" : "SAV";
            
            // Update Output Labels with current data
            lblOutCustID.setText("Cust ID: " + c.toString().split(" ")[0]);
            lblOutAcctNum.setText("Acct #: " + a.getAccountNumber());
            lblOutAcctType.setText("Type: " + acctType);
            lblOutBalance.setText(String.format("Balance: $%.2f", a.getBalance()));
            
            // Clear transaction specific fields
            lblOutDate.setText("Date:");
            lblOutTransType.setText("TT:");
            lblOutAmount.setText("Amt:");
            lblOutFees.setText("Fees:");
            
            setStatus("Displaying data for Account " + searchAcctNum, false);

        } catch (Exception ex) {
            setStatus("Error displaying data: " + ex.getMessage(), true);
        }
    }

    private void performTransaction(ActionEvent e) {
        String searchAcctNum = txtSearchAcctNum.getText().trim();
        double amount = 0.0;
        String date = txtTransDate.getText().trim();
        String transType = rdoDeposit.isSelected() ? "DEP" : "WTH";
        
        try {
            if (searchAcctNum.isEmpty()) throw new Exception("Enter an Account # to transact.");
            amount = validateDouble(txtTransAmount.getText(), "Transaction Amount");
            
            // 1. Find the Account
            int index = -1;
            for (int i = 0; i < accounts.size(); i++) {
                if (accounts.get(i).getAccountNumber().equals(searchAcctNum)) {
                    index = i;
                    break;
                }
            }
            if (index == -1) throw new Exception("Account not found.");
            
            Account a = accounts.get(index);
            double balanceBefore = a.getBalance();
            double feeBefore = 0.0;
            
            // Determine fees for display (pre-transaction)
            if (a instanceof CheckingAccount) {
                feeBefore = 0.50; // Service Fee
                if (transType.equals("WTH") && (balanceBefore - amount - 0.50) < 0) {
                    feeBefore += 30.00; // Overdraft Fee
                }
            } else if (a instanceof SavingsAccount) {
                feeBefore = 0.25;
            }

            // 2. Perform the Transaction
            if (transType.equals("DEP")) {
                a.deposit(amount, date);
            } else { // Withdrawal
                a.withdrawal(amount, date);
            }
            
            // 3. Update Output Labels
            Customer c = customers.get(index);
            String acctType = a instanceof CheckingAccount ? "CHK" : "SAV";
            double balanceAfter = a.getBalance();
            double feesCharged = balanceBefore + (transType.equals("WTH") ? -amount : amount) - balanceAfter;
            
            lblOutCustID.setText("Cust ID: " + c.toString().split(" ")[0]);
            lblOutAcctNum.setText("Acct #: " + a.getAccountNumber());
            lblOutAcctType.setText("Type: " + acctType);
            lblOutDate.setText("Date: " + date);
            lblOutTransType.setText("TT: " + transType);
            lblOutAmount.setText(String.format("Amt: $%.2f", amount));
            lblOutFees.setText(String.format("Fees: $%.2f", Math.abs(feesCharged)));
            lblOutBalance.setText(String.format("Balance: $%.2f", a.getBalance()));

            setStatus("SUCCESS! Transaction completed for Account " + searchAcctNum, false);

        } catch (Exception ex) {
            // Display error and current balance
            Account a = accounts.get(index);
            lblOutBalance.setText(String.format("Balance: $%.2f", a.getBalance()));
            setStatus("Transaction FAILED: " + ex.getMessage(), true);
        }
    }
    
    private void applyInterest(ActionEvent e) {
        String searchAcctNum = txtSearchAcctNum.getText().trim();
        String date = txtTransDate.getText().trim();
        
        try {
            if (searchAcctNum.isEmpty()) throw new Exception("Enter an Account # to apply interest.");
            
            // 1. Find the Account
            int index = -1;
            for (int i = 0; i < accounts.size(); i++) {
                if (accounts.get(i).getAccountNumber().equals(searchAcctNum)) {
                    index = i;
                    break;
                }
            }
            if (index == -1) throw new Exception("Account not found.");
            
            Account a = accounts.get(index);
            double balanceBefore = a.getBalance();

            if (a instanceof CheckingAccount) {
                ((CheckingAccount) a).applyInterest(date);
            } else if (a instanceof SavingsAccount) {
                ((SavingsAccount) a).applyInterest(date);
            } else {
                throw new Exception("Account type not recognized.");
            }
            
            // 3. Update Output Labels
            Customer c = customers.get(index);
            String acctType = a instanceof CheckingAccount ? "CHK" : "SAV";
            double interestAmount = a.getBalance() - balanceBefore;
            
            lblOutCustID.setText("Cust ID: " + c.toString().split(" ")[0]);
            lblOutAcctNum.setText("Acct #: " + a.getAccountNumber());
            lblOutAcctType.setText("Type: " + acctType);
            lblOutDate.setText("Date: " + date);
            lblOutTransType.setText("TT: INT");
            lblOutAmount.setText("Amt: $0.00");
            lblOutFees.setText(String.format("Interest: $%.2f", interestAmount));
            lblOutBalance.setText(String.format("Balance: $%.2f", a.getBalance()));

            setStatus("SUCCESS! Interest applied to Account " + searchAcctNum, false);

        } catch (Exception ex) {
            setStatus("Interest application FAILED: " + ex.getMessage(), true);
        }
    }
    
    // --- Reusable Validation Helpers (Adapted from DataEntry.java) ---

    private String validateLimitedString(String s, String name, int maxLen) throws Exception {
        s = s.trim();
        if (s.isEmpty()) throw new Exception(name + " cannot be blank.");
        if (s.length() > maxLen) throw new Exception(name + " max length is " + maxLen + ".");
        return s;
    }

    private String validateNumericString(String s, String name, int length) throws Exception {
        s = s.trim();
        if (s.isEmpty()) throw new Exception(name + " cannot be blank.");
        if (!s.matches("\\d+")) throw new Exception(name + " must be numeric.");
        if (s.length() != length) throw new Exception(name + " must be exactly " + length + " digits.");
        return s;
    }
    
    private double validateDouble(String s, String name) throws Exception {
        s = s.trim();
        try {
            return Double.parseDouble(s);
        } catch (NumberFormatException e) {
            throw new Exception("Enter a valid decimal number for " + name + ".");
        }
    }
}
