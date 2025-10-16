import java.sql.*;
import java.util.Scanner;

class BookstoreManagement {
    static final String DB_URL = "jdbc:mysql://localhost:3306/bookstore_db";
    static final String USER = "root";
    static final String PASS = "Harshu@123"; 

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try (Connection con = DriverManager.getConnection(DB_URL, USER, PASS)) {
            System.out.println("✅ Connected to Database!");

            while (true) {
                System.out.println("\n=== Bookstore Management System ===");
                System.out.println("1. Add Book");
                System.out.println("2. View Books");
                System.out.println("3. Update Book");
                System.out.println("4. Delete Book");
                System.out.println("5. Exit");
                System.out.print("Enter your choice: ");
                int choice = sc.nextInt();

                switch (choice) {
                    case 1 -> addBook(con, sc);
                    case 2 -> viewBooks(con);
                    case 3 -> updateBook(con, sc);
                    case 4 -> deleteBook(con, sc);
                    case 5 -> {
                        System.out.println("Exiting...");
                        return;
                    }
                    default -> System.out.println("Invalid choice!");
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    // Add a new book
    public static void addBook(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter title: ");
        sc.nextLine(); // consume newline
        String title = sc.nextLine();
        System.out.print("Enter author: ");
        String author = sc.nextLine();
        System.out.print("Enter price: ");
        double price = sc.nextDouble();
        System.out.print("Enter quantity: ");
        int quantity = sc.nextInt();

        String sql = "INSERT INTO books(title, author, price, quantity) VALUES (?, ?, ?, ?)";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setString(1, title);
        ps.setString(2, author);
        ps.setDouble(3, price);
        ps.setInt(4, quantity);
        ps.executeUpdate();

        System.out.println("✅ Book added successfully!");
    }

    // View all books
    public static void viewBooks(Connection con) throws SQLException {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM books");
        System.out.println("\nID\tTitle\t\tAuthor\t\tPrice\tQuantity");
        while (rs.next()) {
            System.out.println(rs.getInt("book_id") + "\t" +
                    rs.getString("title") + "\t" +
                    rs.getString("author") + "\t" +
                    rs.getDouble("price") + "\t" +
                    rs.getInt("quantity"));
        }
    }

    // Update book price or quantity
    public static void updateBook(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter Book ID to update: ");
        int id = sc.nextInt();
        System.out.print("Enter new price: ");
        double price = sc.nextDouble();
        System.out.print("Enter new quantity: ");
        int qty = sc.nextInt();

        String sql = "UPDATE books SET price=?, quantity=? WHERE book_id=?";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setDouble(1, price);
        ps.setInt(2, qty);
        ps.setInt(3, id);

        int rows = ps.executeUpdate();
        if (rows > 0)
            System.out.println("✅ Book updated successfully!");
        else
            System.out.println("❌ Book ID not found!");
    }

    // Delete a book
    public static void deleteBook(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter Book ID to delete: ");
        int id = sc.nextInt();

        String sql = "DELETE FROM books WHERE book_id=?";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setInt(1, id);
        int rows = ps.executeUpdate();

        if (rows > 0)
            System.out.println("✅ Book deleted successfully!");
        else
            System.out.println("❌ Book ID not found!");
    }
}


// SQL Commands to Create Database

CREATE DATABASE bookstore_db;

USE bookstore_db;

CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    author VARCHAR(100),
    price DOUBLE,
    quantity INT
); 
