import tkinter as tk
from tkinter import messagebox
import json
from datetime import datetime

class ExpenseTracker:
    def __init__(self, master):
        self.master = master
        self.master.title("Expense Tracker")

        self.expenses = []

        self.create_widgets()
        self.load_data()

    def create_widgets(self):
        # Сумма
        tk.Label(self.master, text="Сумма").grid(row=0, column=0)
        self.amount_entry = tk.Entry(self.master)
        self.amount_entry.grid(row=0, column=1)

        # Категория
        tk.Label(self.master, text="Категория").grid(row=1, column=0)
        self.category_entry = tk.Entry(self.master)
        self.category_entry.grid(row=1, column=1)

        # Дата
        tk.Label(self.master, text="Дата (YYYY-MM-DD)").grid(row=2, column=0)
        self.date_entry = tk.Entry(self.master)
        self.date_entry.grid(row=2, column=1)

        # Кнопка добавления
        self.add_button = tk.Button(self.master, text="Добавить расход", command=self.add_expense)
        self.add_button.grid(row=3, column=0, columnspan=2)

        # Выведем сумму за выбранный период
        self.calculate_button = tk.Button(self.master, text="Подсчёт суммы", command=self.calculate_total)
        self.calculate_button.grid(row=4, column=0, columnspan=2)

    def add_expense(self):
        amount = self.amount_entry.get()
        category = self.category_entry.get()
        date = self.date_entry.get()

        if self.validate_input(amount, date):
            expense = {
                'amount': float(amount),
                'category': category,
                'date': date
            }
            self.expenses.append(expense)
            self.save_data()
            messagebox.showinfo("Успех", "Расход добавлен!")
            self.amount_entry.delete(0, tk.END)
            self.category_entry.delete(0, tk.END)
            self.date_entry.delete(0, tk.END)
        else:
            messagebox.showerror("Ошибка", "Некорректный ввод. Проверьте сумму и дату.")

    def validate_input(self, amount, date):
        try:
            if float(amount) <= 0:
                return False
            datetime.strptime(date, "%Y-%m-%d")  # Проверка формата даты
            return True
        except ValueError:
            return False

    def load_data(self):
        try:
            with open("expenses.json", "r") as file:
                self.expenses = json.load(file)
        except FileNotFoundError:
            self.expenses = []

    def save_data(self):
        with open("expenses.json", "w") as file:
            json.dump(self.expenses, file)

    def calculate_total(self):
        total = sum(expense['amount'] for expense in self.expenses)
        messagebox.showinfo("Сумма расходов", f"Общая сумма расходов: {total}")

if __name__ == "__main__":
    root = tk.Tk()
    app = ExpenseTracker(root)
    root.mainloop()
