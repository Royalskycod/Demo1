# Demo1
import mysql.connector as a

# Connect to MySQL
con = a.connect(
    host="localhost",
    user="root",
    password="nagabesye",
    database="shopping"
)     
cur = con.cursor()

def register():
    u = input("Enter username: ")
    p = input("Enter password: ")
    cur.execute("INSERT INTO users (username, password) VALUES (%s, %s)", (u, p))
    con.commit()
    print("Registered successfully!!”)

def login():
    u = input("Username: ")
    p = input("Password: ")
    cur.execute("SELECT id FROM users WHERE username=%s AND password=%s", (u, p))
    res = cur.fetchone()
    if res:
        print("Login successful!")
        shop(res[0])
    else:
        print("Invalid login!")

def view_products():
    cur.execute("SELECT * FROM products")
    for row in cur.fetchall():
        print(f"{row[0]}. {row[1]} - ₹{row[2]} (Stock: {row[3]})")

def shop(user_id):
    while True:
        print("\n1. View Products\n2. Buy Product\n3. Logout")
        ch = input("Enter choice: ")
        if ch == '1':
            view_products()
        elif ch == '2':
            pid = int(input("Product ID: "))
            qty = int(input("Quantity: "))
            cur.execute("SELECT stock FROM products WHERE id=%s", (pid,))
            s = cur.fetchone()
            if s and s[0] >= qty:
                cur.execute("UPDATE products SET stock=stock-%s WHERE id=%s", (qty, pid))
                op=input(‘enter your address and phone number’)
                con.commit()
                print("Order placed!,CASH ON DELIVERY TO",op)
            else:
                print("Not enough stock!")
        elif ch == '3':
            break
        else:
            print("Invalid choice.")

def admainlogin():
    u = input("Enter admin username: ")
    p = input("Enter admin password: ")
    if u == "cspro" and p == "2026":
        print("Login successful! Master access granted.")
        while True:
            print("\n1. View Products")
            print("2. Add Product")
            print("3. Delete Product")
            print("4. Update Product")
            print("5. Exit")
            choice = input("Enter your choice: ")

            if choice == "1":
                cur.execute("SELECT * FROM products")
                results = cur.fetchall()
                print("\n--- Products ---")
                for row in results:
                    print(f"ID: {row[0]}, Name: {row[1]}, Price: ₹{row[2]}, Stock: {row[3]}")
                print("----------------")

            elif choice == "2":
                name = input("Enter product name: ")
                price = float(input("Enter product price: "))
                stock = int(input("Enter product stock: "))
                cur.execute("INSERT INTO products (name, price, stock) VALUES (%s, %s, %s)", (name, price, stock))
                con.commit()
                print("Product added successfully.")

            elif choice == "3":
                prod_id = int(input("Enter product ID to delete: "))
                cur.execute("DELETE FROM products WHERE id = %s", (prod_id,))
                con.commit()
                print("Product deleted successfully.")

            elif choice == "4":
                prod_id = int(input("Enter product ID to update: "))
                cur.execute("SELECT * FROM products WHERE id = %s", (prod_id,))
                product = cur.fetchone()
                if product:
                    print(f"Current -> Name: {product[1]}, Price: ₹{product[2]}, Stock: {product[3]}")
                    name = input("Enter new name (leave blank to keep current): ") or product[1]
                    price_input = input("Enter new price (leave blank to keep current): ")
                    price = float(price_input) if price_input else product[2]
                    stock_input = input("Enter new stock (leave blank to keep current): ")
                    stock = int(stock_input) if stock_input else product[3]
                    cur.execute("UPDATE products SET name=%s, price=%s, stock=%s WHERE id=%s",
                                (name, price, stock, prod_id))
                    con.commit()
                    print("Product updated successfully.")
                else:
                    print("Product not found.")

            elif choice == "5":
                print("Exiting admin panel...")
                break

            else:
                print("Invalid choice. Please try again.")
    else:
        print("Access denied. Invalid credentials.")

# Main menu
while True:
    print("\n===== Online Shopping =====")
    print("1. Register")
    print("2. Login")
    print("3. Admin Login")
    print("4. Exit")
    ch = input("Enter choice: ")
    if ch == '1':
        register()
    elif ch == '2':
        login()
    elif ch == '3':
        admainlogin()
    elif ch == '4':
        print("Bye!")
        break
    else:
        print("Invalid choice.")


