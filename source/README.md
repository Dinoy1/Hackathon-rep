Add your source code files to this directory. Please don't rename this directory.
import os
import sys
import sqlite3

# Initialize the application
def init():
    # Connect to the database
    conn = sqlite3.connect("warehouse.db")
    cursor = conn.cursor()

    # Create the inventory table if it doesn't already exist
    cursor.execute(
        "CREATE TABLE IF NOT EXISTS inventory (item TEXT PRIMARY KEY, quantity INTEGER)"
    )
    conn.commit()

    # Load initial data
    cursor.execute("INSERT INTO inventory VALUES (?, ?)", ("hammer", 10))
    cursor.execute("INSERT INTO inventory VALUES (?, ?)", ("screwdriver", 5))
    cursor.execute("INSERT INTO inventory VALUES (?, ?)", ("nails", 50))
    conn.commit()

    # Display a welcome message
    print("Welcome to the warehouse management CLI!")

# Display a menu of options
def display_menu():
    print("\nMenu:")
    print("1. View inventory")
    print("2. Update inventory")
    print("3. Transfer inventory")
    print("4. Quit")

# View the current inventory
def view_inventory(cursor):
    cursor.execute("SELECT * FROM inventory")
    rows = cursor.fetchall()

    print("\nInventory:")
    for row in rows:
        print(f"{row[0]}: {row[1]}")

# Update the inventory
def update_inventory(cursor):
    item = input("Enter the name of the item: ")
    quantity = int(input("Enter the new quantity: "))

    cursor.execute("UPDATE inventory SET quantity=? WHERE item=?", (quantity, item))

# Transfer inventory from one location to another
def transfer_inventory(cursor):
    source = input("Enter the source location: ")
    destination = input("Enter the destination location: ")
    item = input("Enter the name of the item: ")
    quantity = int(input("Enter the quantity: "))

    # Update the inventory of the source location
    cursor.execute(
        "UPDATE inventory SET quantity=quantity-? WHERE item=? AND location=?",
        (quantity, item, source),
    )
    # Update the inventory of the destination location
    cursor.execute(
        "UPDATE inventory SET quantity=quantity+? WHERE item=? AND location=?",
        (quantity, item, destination),
    )

# Main function
def main():
    init()

    # Connect to the database
    conn = sqlite3.connect("warehouse.db")
    cursor = conn.cursor()

    while True:
        display_menu()

        # Accept user input
        choice = input("Enter your choice: ")

        if choice == "1":
            view_inventory(cursor)
        elif choice == "2":
            update_inventory(cursor)
            conn.commit()
        elif choice == "3":
            transfer_inventory(cursor)
            conn.commit()
        elif choice == "4":
            conn.close()
            sys.exit()
        else:
            print("Invalid choice. Try again.")

if __name__=='__main__':
    main()
