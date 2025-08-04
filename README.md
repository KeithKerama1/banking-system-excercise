# banking-system-excercise

public class BankAccount {
    private double balance;
    private static final double MIN_BALANCE = 100.0;
    private static final double OVERDRAFT_LIMIT = -500.0;

    public BankAccount(double initialBalance) {
        if (initialBalance < MIN_BALANCE) {
            throw new IllegalArgumentException("Initial balance must be at least " + MIN_BALANCE);
        }
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive.");
        }
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive.");
        }
        if ((balance - amount) < OVERDRAFT_LIMIT) {
            throw new IllegalArgumentException("Overdraft limit exceeded.");
        }
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
Step 2: Unit Tests (Using JUnit 5)
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.*;

class BankAccountTest {

    @Test
    void testInitialBalanceValid() {
        BankAccount account = new BankAccount(200);
        assertEquals(200, account.getBalance());
    }

    @Test
    void testDepositValid() {
        BankAccount account = new BankAccount(200);
        account.deposit(100);
        assertEquals(300, account.getBalance());
    }

    @Test
    void testWithdrawValid() {
        BankAccount account = new BankAccount(200);
        account.withdraw(50);
        assertEquals(150, account.getBalance());
    }

    @Test
    void testOverdraftLimitExceeded() {
        BankAccount account = new BankAccount(100);
        assertThrows(IllegalArgumentException.class, () -> account.withdraw(700));
    }

    @Test
    void testDepositNegative() {
        BankAccount account = new BankAccount(200);
        assertThrows(IllegalArgumentException.class, () -> account.deposit(-50));
    }

    @Test
    void testWithdrawZero() {
        BankAccount account = new BankAccount(200);
        assertThrows(IllegalArgumentException.class, () -> account.withdraw(0));
    }
}
Exercise 3: Simple E-commerce System – Integration Testing

I)	Product Class
public class Product {
    private String name;
    private double price;
    private int stock;

    public Product(String name, double price, int stock) {
        this.name = name;
        this.price = price;
        this.stock = stock;
    }

    public boolean isInStock(int quantity) {
        return stock >= quantity;
    }

    public void reduceStock(int quantity) {
        if (quantity > stock) throw new IllegalArgumentException("Not enough stock.");
        stock -= quantity;
    }

    public double getPrice() {
        return price;
    }

    public int getStock() {
        return stock;
    }

    public String getName() {
        return name;
    }
}
2. ShoppingCart Class

import java.util.*;

public class ShoppingCart {
    private Map<Product, Integer> items = new HashMap<>();

    public void addItem(Product product, int quantity) {
        if (!product.isInStock(quantity)) {
            throw new IllegalArgumentException("Not enough stock.");
        }
        items.put(product, items.getOrDefault(product, 0) + quantity);
    }

    public void removeItem(Product product) {
        items.remove(product);
    }

    public double calculateTotal() {
        double total = 0;
        for (Map.Entry<Product, Integer> entry : items.entrySet()) {
            total += entry.getKey().getPrice() * entry.getValue();
        }
        return total;
    }

    public Map<Product, Integer> getItems() {
        return items;
    }
}
3. OrderProcessor Class
import java.util.UUID;

public class OrderProcessor {

    public String placeOrder(ShoppingCart cart) {
        for (Map.Entry<Product, Integer> entry : cart.getItems().entrySet()) {
            Product product = entry.getKey();
            int quantity = entry.getValue();

            if (!product.isInStock(quantity)) {
                throw new IllegalStateException("Insufficient stock for: " + product.getName());
            }

            product.reduceStock(quantity);
        }
        return UUID.randomUUID().toString(); // Order ID
    }
}
Unit Testing (Product & ShoppingCart)
class ProductTest {

    @Test
    void testStockCheck() {
        Product p = new Product("Laptop", 1000, 5);
        assertTrue(p.isInStock(3));
        assertFalse(p.isInStock(6));
    }

    @Test
    void testReduceStock() {
        Product p = new Product("Laptop", 1000, 5);
        p.reduceStock(2);
        assertEquals(3, p.getStock());
    }
}

class ShoppingCartTest {

    @Test
    void testAddAndCalculateTotal() {
        Product p = new Product("Phone", 500, 10);
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(p, 2);
        assertEquals(1000, cart.calculateTotal());
    }

    @Test
    void testRemoveItem() {
        Product p = new Product("Phone", 500, 10);
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(p, 1);
        cart.removeItem(p);
        assertEquals(0, cart.calculateTotal());
    }

    @Test
    void testAddExceedingStock() {
        Product p = new Product("Tablet", 200, 3);
        ShoppingCart cart = new ShoppingCart();
        assertThrows(IllegalArgumentException.class, () -> cart.addItem(p, 5));
    }
}


Integration Testing
 ShoppingCart & Product Integration
@Test
void testAddItemAndUpdateTotal() {
    Product p = new Product("Camera", 250, 5);
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(p, 2); // interacts with Product for stock check
    assertEquals(500, cart.calculateTotal());
}
🔹 OrderProcessor with ShoppingCart and Product
java
CopyEdit
@Test
void testPlaceOrderReducesStock() {
    Product p = new Product("Mouse", 50, 10);
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(p, 3);

    OrderProcessor processor = new OrderProcessor();
    processor.placeOrder(cart);

    assertEquals(7, p.getStock()); // stock reduced
}


