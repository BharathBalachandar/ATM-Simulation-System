#ABSTRACT
#This project implements an ATM Simulation System in Java, replicating the core functionalities of an automated teller machine, including user authentication, balance inquiry, deposits, withdrawals, and session termination. The system ensures security through PIN validation and provides accurate account management by dynamically updating the balance after each transaction

# ATM-Simulation-System_PROJECT
import javax.swing.*;
import java.awt.*;

public class ATMSystemGUI {

    // Simulate a basic bank account with balance and PIN
    static class Account {
        String cardNumber;
        String pin;
        double balance;

        public Account(String cardNumber, String pin, double balance) {
            this.cardNumber = cardNumber;
            this.pin = pin;
            this.balance = balance;
        }

        public boolean authenticate(String enteredPin) {
            return pin.equals(enteredPin);
        }

        public boolean hasSufficientFunds(double amount) {
            return balance >= amount;
        }

        public void withdraw(double amount) {
            balance -= amount;
        }

        public void deposit(double amount) {
            balance += amount;
        }

        public double getBalance() {
            return balance;
        }
    }

    // Main GUI class
    public static class ATMSystem extends JFrame {
        private final Account account;
        private final CardLayout cardLayout = new CardLayout();
        private final JPanel mainPanel = new JPanel(cardLayout);

        public ATMSystem(Account account) {
            this.account = account;
            setTitle("ATM System");
            setSize(400, 300);
            setDefaultCloseOperation(EXIT_ON_CLOSE);

            // Setup panels
            mainPanel.add(new LoginPanel(), "Login");
            mainPanel.add(new MenuPanel(), "Menu");
            mainPanel.add(new BalancePanel(), "Balance");
            mainPanel.add(new DepositPanel(), "Deposit");
            mainPanel.add(new WithdrawalPanel(), "Withdrawal");

            add(mainPanel);
            cardLayout.show(mainPanel, "Login");
            setVisible(true);
        }

        private class LoginPanel extends JPanel {
            public LoginPanel() {
                setLayout(new GridLayout(3, 1));
                JLabel pinLabel = new JLabel("Enter PIN:");
                JPasswordField pinField = new JPasswordField();
                JButton loginButton = new JButton("Login");

                add(pinLabel);
                add(pinField);
                add(loginButton);

                loginButton.addActionListener(e -> {
                    String enteredPin = new String(pinField.getPassword());
                    if (account.authenticate(enteredPin)) {
                        cardLayout.show(mainPanel, "Menu");
                    } else {
                        JOptionPane.showMessageDialog(this, "Incorrect PIN. Try again.", "Error", JOptionPane.ERROR_MESSAGE);
                    }
                });
            }
        }

        private class MenuPanel extends JPanel {
            public MenuPanel() {
                setLayout(new GridLayout(4, 1));
                JButton balanceButton = new JButton("Balance Inquiry");
                JButton depositButton = new JButton("Deposit");
                JButton withdrawButton = new JButton("Withdraw");
                JButton exitButton = new JButton("Exit");

                add(balanceButton);
                add(depositButton);
                add(withdrawButton);
                add(exitButton);

                balanceButton.addActionListener(e -> cardLayout.show(mainPanel, "Balance"));
                depositButton.addActionListener(e -> cardLayout.show(mainPanel, "Deposit"));
                withdrawButton.addActionListener(e -> cardLayout.show(mainPanel, "Withdrawal"));
                exitButton.addActionListener(e -> {
                    JOptionPane.showMessageDialog(this, "Thank you for using the ATM!");
                    System.exit(0);
                });
            }
        }

        private class BalancePanel extends JPanel {
            private JLabel balanceLabel;

            public BalancePanel() {
                setLayout(new BorderLayout());
                balanceLabel = new JLabel("", JLabel.CENTER); // Initialize with an empty label
                JButton backButton = new JButton("Back");

                add(balanceLabel, BorderLayout.CENTER);
                add(backButton, BorderLayout.SOUTH);

                backButton.addActionListener(e -> cardLayout.show(mainPanel, "Menu"));
            }

            // Method to update the balance dynamically
            public void updateBalance() {
                balanceLabel.setText("Balance: $" + account.getBalance());
            }
        }


        private class DepositPanel extends JPanel {
            public DepositPanel() {
                setLayout(new GridLayout(3, 1));
                JLabel depositLabel = new JLabel("Enter deposit amount:");
                JTextField depositField = new JTextField();
                JButton depositButton = new JButton("Deposit");

                add(depositLabel);
                add(depositField);
                add(depositButton);

                depositButton.addActionListener(e -> {
                    try {
                        double amount = Double.parseDouble(depositField.getText());
                        account.deposit(amount);
                        JOptionPane.showMessageDialog(this, "Deposit successful! New balance: $" + account.getBalance());
                        cardLayout.show(mainPanel, "Menu");
                    } catch (NumberFormatException ex) {
                        JOptionPane.showMessageDialog(this, "Invalid amount. Try again.", "Error", JOptionPane.ERROR_MESSAGE);
                    }
                });
            }
        }

        private class WithdrawalPanel extends JPanel {
            public WithdrawalPanel() {
                setLayout(new GridLayout(3, 1));
                JLabel withdrawLabel = new JLabel("Enter withdrawal amount:");
                JTextField withdrawField = new JTextField();
                JButton withdrawButton = new JButton("Withdraw");

                add(withdrawLabel);
                add(withdrawField);
                add(withdrawButton);

                withdrawButton.addActionListener(e -> {
                    try {
                        double amount = Double.parseDouble(withdrawField.getText());
                        if (account.hasSufficientFunds(amount)) {
                            account.withdraw(amount);
                            JOptionPane.showMessageDialog(this, "Withdrawal successful! New balance: $" + account.getBalance());
                            cardLayout.show(mainPanel, "Menu");
                        } else {
                            JOptionPane.showMessageDialog(this, "Insufficient funds.", "Error", JOptionPane.ERROR_MESSAGE);
                        }
                    } catch (NumberFormatException ex) {
                        JOptionPane.showMessageDialog(this, "Invalid amount. Try again.", "Error", JOptionPane.ERROR_MESSAGE);
                    }
                });
            }
        }
    }

    // Main method to run the application
    public static void main(String[] args) {
        Account account = new Account("1234567890", "1234", 1000.00);
        new ATMSystem(account);
    }
}
