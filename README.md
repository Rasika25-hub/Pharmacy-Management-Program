import hashlib
import time

class Medicine:
    def __init__(self, name, price, quantity):
        self.name = name
        self.price = price
        self.quantity = quantity

    def update_quantity(self, quantity):
        self.quantity += quantity

    def sell(self, quantity):
        if quantity <= self.quantity:
            self.quantity -= quantity
            return self.price * quantity
        else:
            print("Not enough stock.")
            return 0


class Customer:
    def __init__(self, customer_id, name, phone_number):
        self.customer_id = customer_id
        self.name = name
        self.phone_number = phone_number
        self.prescriptions = []

    def add_prescription(self, prescription):
        self.prescriptions.append(prescription)

    def view_prescriptions(self):
        return self.prescriptions


class Prescription:
    def __init__(self, medicine_name, quantity, date):
        self.medicine_name = medicine_name
        self.quantity = quantity
        self.date = date


class Pharmacy:
    def __init__(self):
        self.medicine_inventory = {}
        self.customers = {}
        self.sales_total = 0
        self.user_credentials = {"admin": hashlib.sha256("admin123".encode()).hexdigest()}  # Username: admin, Password: admin123

    def add_medicine(self, name, price, quantity):
        if name in self.medicine_inventory:
            print(f"{name} already exists. Updating quantity.")
            self.medicine_inventory[name].update_quantity(quantity)
        else:
            self.medicine_inventory[name] = Medicine(name, price, quantity)
            print(f"{name} added to inventory.")

    def remove_medicine(self, name):
        if name in self.medicine_inventory:
            del self.medicine_inventory[name]
            print(f"{name} removed from inventory.")
        else:
            print(f"{name} not found in inventory.")

    def sell_medicine(self, name, quantity):
        if name in self.medicine_inventory:
            medicine = self.medicine_inventory[name]
            total_price = medicine.sell(quantity)
            if total_price > 0:
                self.sales_total += total_price
                print(f"Sold {quantity} of {name} for ${total_price}")
            else:
                print("Sale failed.")
        else:
            print(f"{name} not found in inventory.")

    def view_inventory(self):
        if not self.medicine_inventory:
            print("Inventory is empty.")
        else:
            print("\n" + "-"*50)
            print(f"{'Medicine Name':<20}{'Price':<10}{'Quantity':<10}")
            print("-"*50)
            for name, medicine in self.medicine_inventory.items():
                print(f"{name:<20}{medicine.price:<10}{medicine.quantity:<10}")
            print("-"*50)

    def add_customer(self, customer_id, name, phone_number):
        self.customers[customer_id] = Customer(customer_id, name, phone_number)

    def add_prescription(self, customer_id, medicine_name, quantity):
        if customer_id in self.customers:
            prescription = Prescription(medicine_name, quantity, time.strftime("%Y-%m-%d"))
            self.customers[customer_id].add_prescription(prescription)
        else:
            print("Customer not found.")

    def view_customer_prescriptions(self, customer_id):
        if customer_id in self.customers:
            prescriptions = self.customers[customer_id].view_prescriptions()
            if prescriptions:
                print("\nCustomer Prescriptions:")
                print(f"{'Medicine':<20}{'Quantity':<10}{'Date'}")
                for prescription in prescriptions:
                    print(f"{prescription.medicine_name:<20}{prescription.quantity:<10}{prescription.date}")
            else:
                print("No prescriptions found for this customer.")
        else:
            print("Customer not found.")

    def revenue_tracking(self):
        print(f"\nTotal Sales Revenue: ${self.sales_total}")

    def inventory_alert(self, threshold):
        for name, medicine in self.medicine_inventory.items():
            if medicine.quantity <= threshold:
                print(f"Alert: {name} is low in stock! Only {medicine.quantity} left.")


# User Authentication
def authenticate_user(pharmacy):
    print("User Authentication")
    username = input("Enter username: ")
    password = input("Enter password: ")
    hashed_password = hashlib.sha256(password.encode()).hexdigest()
    if pharmacy.user_credentials.get(username) == hashed_password:
        print(f"Welcome {username}!")
        return True
    else:
        print("Invalid username or password.")
        return False


# Main Program Logic
def main():
    pharmacy = Pharmacy()

    # Sample medicines
    pharmacy.add_medicine("Paracetamol", 5.0, 100)
    pharmacy.add_medicine("Aspirin", 3.5, 50)
    pharmacy.add_medicine("Ibuprofen", 7.2, 200)

    # Sample customers
    pharmacy.add_customer(1, "Alice", "123-456-7890")
    pharmacy.add_customer(2, "Bob", "987-654-3210")

    # Authentication (admin login)
    if not authenticate_user(pharmacy):
        return

    while True:
        print("\nPharmacy Management System")
        print("1. Add Medicine")
        print("2. Remove Medicine")
        print("3. Sell Medicine")
        print("4. View Inventory")
        print("5. Add Customer")
        print("6. Add Prescription")
        print("7. View Customer Prescriptions")
        print("8. Revenue Tracking")
        print("9. Inventory Alerts")
        print("10. Exit")
        choice = input("Enter your choice: ")

        if choice == '1':
            name = input("Enter medicine name: ")
            price = float(input("Enter price: $"))
            quantity = int(input("Enter quantity: "))
            pharmacy.add_medicine(name, price, quantity)
        
        elif choice == '2':
            name = input("Enter medicine name to remove: ")
            pharmacy.remove_medicine(name)
        
        elif choice == '3':
            name = input("Enter medicine name to sell: ")
            quantity = int(input("Enter quantity to sell: "))
            pharmacy.sell_medicine(name, quantity)
        
        elif choice == '4':
            pharmacy.view_inventory()
        
        elif choice == '5':
            customer_id = int(input("Enter customer ID: "))
            name = input("Enter customer name: ")
            phone_number = input("Enter customer phone number: ")
            pharmacy.add_customer(customer_id, name, phone_number)
        
        elif choice == '6':
            customer_id = int(input("Enter customer ID for prescription: "))
            medicine_name = input("Enter medicine name: ")
            quantity = int(input("Enter quantity: "))
            pharmacy.add_prescription(customer_id, medicine_name, quantity)
        
        elif choice == '7':
            customer_id = int(input("Enter customer ID to view prescriptions: "))
            pharmacy.view_customer_prescriptions(customer_id)
        
        elif choice == '8':
            pharmacy.revenue_tracking()
        
        elif choice == '9':
            threshold = int(input("Enter inventory threshold for alert: "))
            pharmacy.inventory_alert(threshold)
        
        elif choice == '10':
            print("Exiting Pharmacy Management System...")
            break
        
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()

