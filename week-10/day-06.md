# 📅 Day 6: Project Day — Expense Tracker App 💰

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- All hooks from Days 1-5 (useState, useEffect, useRef, useMemo, custom hooks)
- Form handling and validation

---

## 🎯 Today's Goals

- 💰 Build a complete **Expense Tracker App** using React hooks
- 📊 Display income vs expense balance
- 📋 Add, filter, and delete transactions
- 💾 Persist data with localStorage via custom hook

---

## 📘 Project Overview

### Features

- **Balance dashboard** — total balance, income, expenses
- **Add transaction** — form with amount, description, category, type (income/expense)
- **Transaction list** — filterable, sortable, with delete
- **Category breakdown** — group expenses by category
- **localStorage persistence** — data survives page refresh

---

## 🏗️ Project Structure

```
expense-tracker/
├── src/
│   ├── hooks/
│   │   ├── useLocalStorage.js
│   │   └── useForm.js
│   ├── components/
│   │   ├── Balance.jsx
│   │   ├── TransactionForm.jsx
│   │   ├── TransactionList.jsx
│   │   ├── TransactionItem.jsx
│   │   └── CategoryChart.jsx
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
└── package.json
```

### Step 1: Custom Hooks

```jsx
// src/hooks/useLocalStorage.js
import { useState, useEffect } from "react";

export function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored !== null ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}
```

### Step 2: Balance Component

```jsx
// src/components/Balance.jsx
import { useMemo } from "react";

export default function Balance({ transactions }) {
  const { balance, income, expenses } = useMemo(() => {
    const income = transactions
      .filter(t => t.type === "income")
      .reduce((sum, t) => sum + t.amount, 0);

    const expenses = transactions
      .filter(t => t.type === "expense")
      .reduce((sum, t) => sum + t.amount, 0);

    return {
      balance: income - expenses,
      income,
      expenses
    };
  }, [transactions]);

  return (
    <div className="mb-6">
      <h2 className="text-center text-gray-500 text-sm uppercase tracking-wide">
        Your Balance
      </h2>
      <p className={`text-center text-4xl font-bold my-2 ${
        balance >= 0 ? "text-green-600" : "text-red-600"
      }`}>
        PKR {balance.toLocaleString()}
      </p>
      <div className="grid grid-cols-2 gap-4 mt-4">
        <div className="bg-green-50 p-4 rounded-xl text-center">
          <p className="text-sm text-gray-500">Income</p>
          <p className="text-xl font-bold text-green-600">
            +PKR {income.toLocaleString()}
          </p>
        </div>
        <div className="bg-red-50 p-4 rounded-xl text-center">
          <p className="text-sm text-gray-500">Expenses</p>
          <p className="text-xl font-bold text-red-600">
            -PKR {expenses.toLocaleString()}
          </p>
        </div>
      </div>
    </div>
  );
}
```

### Step 3: Transaction Form

```jsx
// src/components/TransactionForm.jsx
import { useState, useRef } from "react";

const CATEGORIES = [
  "Food", "Transport", "Entertainment", "Shopping",
  "Bills", "Education", "Health", "Salary", "Freelance", "Other"
];

export default function TransactionForm({ onAdd }) {
  const [form, setForm] = useState({
    description: "",
    amount: "",
    type: "expense",
    category: "Other"
  });
  const inputRef = useRef(null);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const amount = parseFloat(form.amount);
    if (!form.description.trim() || isNaN(amount) || amount <= 0) return;

    onAdd({
      id: Date.now(),
      description: form.description.trim(),
      amount,
      type: form.type,
      category: form.category,
      date: new Date().toISOString()
    });

    setForm({ description: "", amount: "", type: "expense", category: "Other" });
    inputRef.current.focus();
  };

  return (
    <form onSubmit={handleSubmit} className="bg-white p-4 rounded-xl shadow-sm mb-6">
      <h3 className="font-bold text-lg mb-3">Add Transaction</h3>

      <div className="grid grid-cols-2 gap-3 mb-3">
        <input
          ref={inputRef}
          name="description"
          value={form.description}
          onChange={handleChange}
          placeholder="Description"
          required
          className="col-span-2 p-2 border rounded-lg"
        />
        <input
          name="amount"
          type="number"
          min="0"
          step="any"
          value={form.amount}
          onChange={handleChange}
          placeholder="Amount (PKR)"
          required
          className="p-2 border rounded-lg"
        />
        <select
          name="category"
          value={form.category}
          onChange={handleChange}
          className="p-2 border rounded-lg"
        >
          {CATEGORIES.map(c => <option key={c} value={c}>{c}</option>)}
        </select>
      </div>

      <div className="flex gap-4 mb-3">
        <label className="flex items-center gap-1">
          <input
            type="radio"
            name="type"
            value="expense"
            checked={form.type === "expense"}
            onChange={handleChange}
          />
          Expense
        </label>
        <label className="flex items-center gap-1">
          <input
            type="radio"
            name="type"
            value="income"
            checked={form.type === "income"}
            onChange={handleChange}
          />
          Income
        </label>
      </div>

      <button
        type="submit"
        className="w-full py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors font-medium"
      >
        Add {form.type === "income" ? "Income" : "Expense"}
      </button>
    </form>
  );
}
```

### Step 4: Transaction Item

```jsx
// src/components/TransactionItem.jsx
export default function TransactionItem({ transaction, onDelete }) {
  const { id, description, amount, type, category, date } = transaction;
  const isIncome = type === "income";

  return (
    <li className={`flex items-center justify-between p-3 rounded-lg border-l-4 ${
      isIncome ? "border-green-500 bg-green-50" : "border-red-500 bg-red-50"
    }`}>
      <div>
        <p className="font-medium text-gray-800">{description}</p>
        <p className="text-sm text-gray-500">
          {category} • {new Date(date).toLocaleDateString()}
        </p>
      </div>
      <div className="flex items-center gap-3">
        <span className={`font-bold ${isIncome ? "text-green-600" : "text-red-600"}`}>
          {isIncome ? "+" : "-"}PKR {amount.toLocaleString()}
        </span>
        <button
          onClick={() => onDelete(id)}
          className="text-gray-400 hover:text-red-500 transition-colors"
          title="Delete"
        >
          ×
        </button>
      </div>
    </li>
  );
}
```

### Step 5: Transaction List with Filter

```jsx
// src/components/TransactionList.jsx
import { useState, useMemo } from "react";
import TransactionItem from "./TransactionItem";

export default function TransactionList({ transactions, onDelete }) {
  const [filter, setFilter] = useState("all");
  const [sortBy, setSortBy] = useState("date");

  const filteredAndSorted = useMemo(() => {
    let result = [...transactions];

    // Filter
    if (filter === "income") result = result.filter(t => t.type === "income");
    if (filter === "expense") result = result.filter(t => t.type === "expense");

    // Sort
    if (sortBy === "date") result.sort((a, b) => new Date(b.date) - new Date(a.date));
    if (sortBy === "amount") result.sort((a, b) => b.amount - a.amount);
    if (sortBy === "name") result.sort((a, b) => a.description.localeCompare(b.description));

    return result;
  }, [transactions, filter, sortBy]);

  return (
    <div>
      <div className="flex items-center justify-between mb-3">
        <h3 className="font-bold text-lg">Transactions</h3>
        <div className="flex gap-2">
          <select
            value={filter}
            onChange={(e) => setFilter(e.target.value)}
            className="text-sm p-1 border rounded"
          >
            <option value="all">All</option>
            <option value="income">Income</option>
            <option value="expense">Expenses</option>
          </select>
          <select
            value={sortBy}
            onChange={(e) => setSortBy(e.target.value)}
            className="text-sm p-1 border rounded"
          >
            <option value="date">By Date</option>
            <option value="amount">By Amount</option>
            <option value="name">By Name</option>
          </select>
        </div>
      </div>

      {filteredAndSorted.length === 0 ? (
        <p className="text-center text-gray-400 py-8">No transactions yet.</p>
      ) : (
        <ul className="space-y-2">
          {filteredAndSorted.map(t => (
            <TransactionItem key={t.id} transaction={t} onDelete={onDelete} />
          ))}
        </ul>
      )}
    </div>
  );
}
```

### Step 6: Category Breakdown

```jsx
// src/components/CategoryChart.jsx
import { useMemo } from "react";

export default function CategoryChart({ transactions }) {
  const categoryTotals = useMemo(() => {
    const expenses = transactions.filter(t => t.type === "expense");
    const totals = {};

    expenses.forEach(t => {
      totals[t.category] = (totals[t.category] || 0) + t.amount;
    });

    const totalExpenses = Object.values(totals).reduce((a, b) => a + b, 0);

    return Object.entries(totals)
      .map(([category, amount]) => ({
        category,
        amount,
        percentage: totalExpenses > 0 ? (amount / totalExpenses) * 100 : 0
      }))
      .sort((a, b) => b.amount - a.amount);
  }, [transactions]);

  if (categoryTotals.length === 0) return null;

  const colors = [
    "bg-red-500", "bg-blue-500", "bg-green-500", "bg-yellow-500",
    "bg-purple-500", "bg-pink-500", "bg-indigo-500", "bg-orange-500"
  ];

  return (
    <div className="mt-6">
      <h3 className="font-bold text-lg mb-3">Expenses by Category</h3>
      <div className="space-y-2">
        {categoryTotals.map((item, i) => (
          <div key={item.category}>
            <div className="flex justify-between text-sm mb-1">
              <span>{item.category}</span>
              <span>PKR {item.amount.toLocaleString()} ({item.percentage.toFixed(1)}%)</span>
            </div>
            <div className="w-full bg-gray-200 rounded-full h-2">
              <div
                className={`h-2 rounded-full ${colors[i % colors.length]}`}
                style={{ width: `${item.percentage}%` }}
              />
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Step 7: App Component

```jsx
// src/App.jsx
import { useCallback } from "react";
import { useLocalStorage } from "./hooks/useLocalStorage";
import Balance from "./components/Balance";
import TransactionForm from "./components/TransactionForm";
import TransactionList from "./components/TransactionList";
import CategoryChart from "./components/CategoryChart";

function App() {
  const [transactions, setTransactions] = useLocalStorage("transactions", []);

  const addTransaction = useCallback((transaction) => {
    setTransactions(prev => [...prev, transaction]);
  }, [setTransactions]);

  const deleteTransaction = useCallback((id) => {
    setTransactions(prev => prev.filter(t => t.id !== id));
  }, [setTransactions]);

  return (
    <div className="min-h-screen bg-gray-100 py-8">
      <div className="max-w-md mx-auto px-4">
        <header className="text-center mb-6">
          <h1 className="text-3xl font-bold text-gray-800">💰 Expense Tracker</h1>
          <p className="text-gray-500">Track your income & expenses</p>
        </header>

        <Balance transactions={transactions} />
        <TransactionForm onAdd={addTransaction} />
        <TransactionList
          transactions={transactions}
          onDelete={deleteTransaction}
        />
        <CategoryChart transactions={transactions} />

        <footer className="text-center mt-8 text-sm text-gray-400">
          <p>&copy; 2026 NextGen Arts — Week 10 Project</p>
        </footer>
      </div>
    </div>
  );
}

export default App;
```

---

## 🧪 Test Your App

1. ✅ Add an income transaction — balance increases
2. ✅ Add an expense — balance decreases, category bar appears
3. ✅ Delete a transaction — balance recalculates
4. ✅ Filter by income/expense — list updates
5. ✅ Sort by date/amount/name — order changes
6. ✅ Refresh page — data persists (localStorage)
7. ✅ Empty state shows when no transactions exist

---

## 🚀 Stretch Goals

- 📅 Add **date picker** for transactions (not just "today")
- 📊 Add a **monthly summary** chart
- 🏷️ Add **tags** to transactions for extra categorization
- 📤 **Export** transactions as CSV file
- 🔍 Add **search** to find specific transactions

---

## 📝 Homework

1. Add **edit transaction** — click to edit description, amount, or category inline
2. Implement a **monthly budget** feature — set budget per category, show warnings
3. Add a **recurring transactions** feature (e.g., monthly salary)
4. Create a **statistics page** with average daily spending, biggest expense, etc.

---

## 💡 Pro Tips

> 📦 **Custom hooks = clean code:** Notice how `useLocalStorage` makes persistence a single line. Extract patterns you use twice!

> 🧮 **Derived state with useMemo:** Balance, category totals, and filtered lists are all computed from transactions — never stored separately.

> 🎯 **Component responsibility:** Each component does ONE thing. `Balance` shows numbers, `TransactionForm` handles input, `TransactionList` handles display/filter.

---

[← Day 5](day-05.md) | [Back to Week 10](README.md) | [Week 11 →](../week-11/README.md)
