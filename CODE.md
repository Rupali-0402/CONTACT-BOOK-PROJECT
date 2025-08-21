import json

class ContactNode:
    def __init__(self, name, phone, email):
        self.name = name
        self.phone = phone
        self.email = email
        self.next = None  # points to the next ContactNode

class ContactBook:
    def __init__(self):
        self.head = None

    def insert_contact(self, name, phone, email):
        new_node = ContactNode(name, phone, email)

        # Insert at beginning if list empty or name comes before head alphabetically
        if self.head is None or name.lower() < self.head.name.lower():
            new_node.next = self.head
            self.head = new_node
            return

        # Otherwise, find proper sorted position
        current = self.head
        while current.next and current.next.name.lower() < name.lower():
            current = current.next

        new_node.next = current.next
        current.next = new_node

    def search_contact(self, name):
        current = self.head
        while current:
            if current.name.lower() == name.lower():
                return current
            current = current.next
        return None

    def update_contact(self, name, phone=None, email=None):
        contact = self.search_contact(name)
        if contact:
            if phone is not None:
                contact.phone = phone
            if email is not None:
                contact.email = email
            return True
        return False

    def delete_contact(self, name):
        current = self.head
        prev = None
        while current:
            if current.name.lower() == name.lower():
                if prev:
                    prev.next = current.next
                else:
                    self.head = current.next
                return True
            prev = current
            current = current.next
        return False

    def display_contacts(self):
        current = self.head
        if not current:
            print("No contacts to display.")
            return
        print("Contacts List:")
        while current:
            print(f"Name: {current.name}, Phone: {current.phone}, Email: {current.email}")
            current = current.next

    def to_list(self):
        contacts = []
        current = self.head
        while current:
            contacts.append({
                "name": current.name,
                "phone": current.phone,
                "email": current.email
            })
            current = current.next
        return contacts

    def load_from_file(self, filename):
        try:
            with open(filename, 'r') as f:
                contacts = json.load(f)
                self.head = None
                for c in contacts:
                    self.insert_contact(c['name'], c['phone'], c['email'])
        except FileNotFoundError:
            print("No existing contact file found. Starting with an empty contact book.")

    def save_to_file(self, filename):
        with open(filename, 'w') as f:
            json.dump(self.to_list(), f, indent=2)

def main():
    book = ContactBook()
    filename = "contacts.json"
    book.load_from_file(filename)

    while True:
        print("\nContact Book Menu:")
        print("1. Add Contact")
        print("2. Search Contact")
        print("3. Update Contact")
        print("4. Delete Contact")
        print("5. Display All Contacts")
        print("6. Save and Exit")
        choice = input("Choose an option (1-6): ")

        if choice == '1':
            name = input("Enter name: ")
            phone = input("Enter phone: ")
            email = input("Enter email: ")
            book.insert_contact(name, phone, email)
            print(f"Contact '{name}' added.")

        elif choice == '2':
            name = input("Enter name to search: ")
            contact = book.search_contact(name)
            if contact:
                print(f"Found Contact - Name: {contact.name}, Phone: {contact.phone}, Email: {contact.email}")
            else:
                print(f"No contact found with the name '{name}'.")

        elif choice == '3':
            name = input("Enter name to update: ")
            if book.search_contact(name):
                phone = input("Enter new phone (leave blank to keep current): ")
                email = input("Enter new email (leave blank to keep current): ")
                phone = phone if phone else None
                email = email if email else None
                book.update_contact(name, phone, email)
                print(f"Contact '{name}' updated.")
            else:
                print(f"No contact found with the name '{name}'.")

        elif choice == '4':
            name = input("Enter name to delete: ")
            if book.delete_contact(name):
                print(f"Contact '{name}' deleted.")
            else:
                print(f"No contact found with the name '{name}'.")

        elif choice == '5':
            book.display_contacts()

        elif choice == '6':
            book.save_to_file(filename)
            print(f"Contacts saved to '{filename}'. Goodbye!")
            break

        else:
            print("Invalid choice. Please select a valid option.")

if __name__ == "__main__":
    main()
