# Student-Database-Management-System
First Repository ever created by me.
# --- GLOBAL CONFIGURATION ---
DATABASE_FILE = "student_data.txt"

def get_all_students():
    """
    Reads the database file and converts it into a List of Lists.
    Example return: [['1', 'Vibhor', '20', 'A'], ['2', 'Anvesha', '22', 'B']]
    """
    students = []
    try:
        # Open file in Read mode ('r')
        file = open(DATABASE_FILE, "r")
        lines = file.readlines()
        file.close()
        
        # Process each line
        for line in lines:
            # Strip whitespace and split by comma
            # Format in file: ID,Name,Age,Major,GPA
            data = line.strip().split(",") 
            if len(data) == 5: # Ensure line isn't empty or corrupted
                students.append(data)
    except FileNotFoundError:
        # If file doesn't exist, we just return an empty list
        pass
        
    return students

def save_all_students(students):
    """
    Takes a List of Lists and overwrites the database file with the current data.
    Used after Updating or Deleting.
    """
    file = open(DATABASE_FILE, "w") # 'w' mode wipes the file clean to rewrite
    for student in students:
        # Join the list items back into a comma-separated string
        line = ",".join(student) + "\n"
        file.write(line)
    file.close()

def generate_new_id(students):
    """
    Calculates the next ID based on the last student in the list.
    """
    if not students:
        return "1"
    else:
        # Get the ID of the last student (index -1) and the first column (index 0)
        last_id = int(students[-1][0])
        return str(last_id + 1)

def add_student():
    """Appends a new student to the file."""
    print("\n--- ADD NEW STUDENT ---")
    name = input("Enter Name: ").strip()
    age = input("Enter Age: ").strip()
    major = input("Enter Major: ").strip()
    gpa = input("Enter GPA: ").strip()

    # Load current data only to generate a new ID
    current_data = get_all_students()
    new_id = generate_new_id(current_data)

    # Construct the record string
    record = f"{new_id},{name},{age},{major},{gpa}\n"

    # Append to file using 'a' mode
    file = open(DATABASE_FILE, "a")
    file.write(record)
    file.close()
    
    print(f"Success! Student added with ID {new_id}")

def view_students():
    """Prints all students in a formatted layout using basic string spacing."""
    students = get_all_students()
    
    if not students:
        print("\n[!] Database is empty.")
        return

    print("\n" + "="*60)
    # Using manual spacing instead of advanced formatting
    print(f"{'ID':<5} {'Name':<20} {'Age':<5} {'Major':<15} {'GPA':<5}")
    print("-" * 60)

    for row in students:
        # row[0]=ID, row[11]=Name, row[2]=Age, row[3]=Major, row[4]=GPA
        print(f"{row[0]:<5} {row[1]:<20} {row[2]:<5} {row[3]:<15} {row[4]:<5}")
    print("=" * 60)
def search_student():
    """Finds a student by Name (partial match) or ID."""
    search_term = input("\nEnter Name or ID to search: ").lower()
    students = get_all_students()
    found = False

    print("\n--- SEARCH RESULTS ---")
    for row in students:
        id_match = search_term == row[0]
        name_match = search_term in row[1].lower()
        
        if id_match or name_match:
            print(f"ID: {row[0]} | Name: {row[1]} | Age: {row[2]} | Major: {row[3]} | GPA: {row[4]}")
            found = True
    
    if not found:
        print("No student found.")

def update_student():
    """Updates a specific field of a student."""
    view_students()
    target_id = input("\nEnter ID of student to update: ")
    
    students = get_all_students()
    updated_list = []
    found = False

    for row in students:
        if row[0] == target_id:
            found = True
            print(f"Editing Student: {row[1]}")
            print("Leave blank to keep current value.")
            
            new_name = input(f"Name ({row[1]}): ").strip()
            new_age = input(f"Age ({row[2]}): ").strip()
            new_major = input(f"Major ({row[3]}): ").strip()
            new_gpa = input(f"GPA ({row[4]}): ").strip()

            # If user entered something, update. If blank, keep old value.
            # Using ternary operators for basic logic
            row[1] = new_name if new_name != "" else row[1]
            row[2] = new_age if new_age != "" else row[2]
            row[3] = new_major if new_major != "" else row[3]
            row[4] = new_gpa if new_gpa != "" else row[4]
        
        updated_list.append(row)

    if found:
        save_all_students(updated_list)
        print("Student updated successfully.")
    else:
        print("ID not found.")

def delete_student():
    """Removes a student record from the list and rewrites the file."""
    view_students()
    target_id = input("\nEnter ID of student to DELETE: ")
    
    students = get_all_students()
    new_list = []
    found = False

    for row in students:
        if row[0] == target_id:
            found = True
            # We simply DO NOT append this row to the new list
            continue 
        new_list.append(row)

    if found:
        save_all_students(new_list)
        print(f"Student ID {target_id} deleted.")
    else:
        print("ID not found.")

def main_menu():
    """The main loop controlling the program."""
    while True:
        print("\n=== RAW PYTHON STUDENT DB ===")
        print("1. Add Student")
        print("2. View All")
        print("3. Search")
        print("4. Update")
        print("5. Delete")
        print("6. Exit")
        
        choice = input("Select (1-6): ")

        if choice == '1':
            add_student()
        elif choice == '2':
            view_students()
        elif choice == '3':
            search_student()
        elif choice == '4':
            update_student()
        elif choice == '5':
            delete_student()
        elif choice == '6':
            print("Goodbye.")
            break
        else:
            print("Invalid selection.")

# Start the program
if __name__ == "__main__":
    main_menu()
