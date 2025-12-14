# ATM-SIMULATION-SYSTEM-
The Java-based ATM Simulation System.This is a high-fidelity frontend simulation of a next-generation ATM system that eliminates the need for physical debit cards. Instead of inserting a card, users authenticate via credentials and a simulated biometric fingerprint scan. The application acts as a "Universal Banking Hub," allowing a single user to manage accounts across multiple different banks (e.g., SBI, HDFC, ICICI) in one unified dashboard..

import java.util.*;

// --- Models ---

class User {
    private String username;
    private String passwordHash;
    private String pinHash;
    private List<Account> accounts;

    public User(String username, String password, String pin) {
        this.username = username;
        this.passwordHash = password; // In real app, use BCrypt
        this.pinHash = pin;
        this.accounts = new ArrayList<>();
    }
    
    // ... getters and setters
}

// --- Admin Controller ---

public class AdminController {
    
    private BankService bankService;

    public void createNewUser(String username, String password, String pin) {
        if (bankService.userExists(username)) {
            throw new RuntimeException("User already exists");
        }

        User newUser = new User(username, password, pin);
        
        // Automatically assign starter accounts
        newUser.addAccount(new Account(UUID.randomUUID().toString(), "State Bank of India", "Savings", 5000.00, "INR"));
        newUser.addAccount(new Account(UUID.randomUUID().toString(), "HDFC Bank", "Checking", 10000.00, "INR"));
        
        bankService.saveUser(newUser);
        System.out.println("User " + username + " created successfully.");
    }
}

// --- Service Layer (In-Memory Database) ---

public class BankService {
    private static Map<String, User> userDatabase = new HashMap<>();

    static {
        // Initialize Admin
        User admin = new User("admin", "admin123", "0000");
        userDatabase.put("admin", admin);
        
        // Initialize Demo User
        User demo = new User("demo_user", "pass1234", "1234");
        demo.addAccount(new Account("ACC1", "SBI", "Savings", 15420.50, "INR"));
        userDatabase.put("demo_user", demo);
    }

    public static User login(String username, String password) {
        User user = userDatabase.get(username);
        if (user != null && user.validatePassword(password)) {
            return user;
        }
        return null;
    }
    
    public void saveUser(User user) {
        userDatabase.put(user.getUsername(), user);
    }
}


