# New-project
Project 1
import csv
import json


#---------------- Person Class ----------------
class Person:
    def __init__(self, emp_id, name):
        self.id = emp_id
        self.name = name


# ---------------- Employee Class ----------------
class Employee(Person):
    def __init__(self, emp_id, name, salary):
        super().__init__(emp_id, name)
        self.salary = salary

    def calculate_salary(self):
        return self.salary


# ---------------- Manager Class ----------------
class Manager(Employee):
    def __init__(self, emp_id, name, salary, bonus):
        super().__init__(emp_id, name, salary)
        self.bonus = bonus

    # Method Overriding
    def calculate_salary(self):
        return self.salary + self.bonus


# ---------------- File Handler ----------------
class FileHandler:

    def read_csv(self, filename):
        employees = []

        try:
            with open(filename, "r") as file:
                reader = csv.DictReader(file)

                for row in reader:
                    employees.append({
                        "id": row["id"],
                        "name": row["name"],
                        "salary": float(row["salary"])
                    })

        except FileNotFoundError:
            print("CSV file not found.")

        except KeyError:
            print("Missing fields in CSV.")

        except ValueError:
            print("Wrong data type in CSV.")

        return employees

    def read_json(self, filename):
        try:
            with open(filename, "r") as file:
                return json.load(file)

        except FileNotFoundError:
            print("JSON file not found.")
            return {}

        except json.JSONDecodeError:
            print("Invalid JSON format.")
            return {}


# ---------------- Report Generator ----------------
class ReportGenerator:

    def generate_summary(self, employees):
        report = []

        for emp in employees:
            salary = emp.calculate_salary()

            status = "High Income" if salary > 50000 else "Normal"

            report.append({
                "ID": emp.id,
                "Name": emp.name,
                "Salary": salary,
                "Status": status
            })

        return report

    def save_output(self, report, filename):
        with open(filename, "w") as file:
            for r in report:
                file.write(
                    f"ID: {r['ID']}, "
                    f"Name: {r['Name']}, "
                    f"Salary: {r['Salary']}, "
                    f"Status: {r['Status']}\n"
                )


# ---------------- Main Program ----------------
def main():

    fh = FileHandler()

    employee_data = fh.read_csv("employee.csv")
    bonus_data = fh.read_json("bonus.json")

    employees = []

    for emp in employee_data:

        emp_id = emp["id"]
        name = emp["name"]
        salary = emp["salary"]

        if emp_id in bonus_data:
            employees.append(
                Manager(emp_id, name, salary, bonus_data[emp_id])
            )
        else:
            employees.append(
                Employee(emp_id, name, salary)
            )

    rg = ReportGenerator()

    report = rg.generate_summary(employees)

    rg.save_output(report, "report.txt")

    print("Report Generated Successfully!\n")

    for r in report:
        print(r)


if __name__ == "__main__":
    main()
