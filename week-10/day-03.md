# 📅 Day 3: Forms — Controlled Components & Validation 📝

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- `useState` and `useEffect` from Days 1-2
- HTML forms and input types

---

## 🎯 Today's Goals

- 📝 Build forms with controlled components
- ✅ Implement real-time validation
- 🎯 Handle multiple inputs with a single handler
- 📤 Submit forms and handle loading states

---

## 📘 Lesson Content

### Controlled vs Uncontrolled Components

```jsx
// ✅ Controlled — React state drives the input value
function ControlledInput() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}                              // Value FROM state
      onChange={(e) => setName(e.target.value)}  // Updates state
    />
  );
  // React is the "single source of truth"
}

// Uncontrolled — DOM manages the value internally
// We'll focus on controlled components (React best practice)
```

### Basic Form

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Login:", { email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          required
        />
      </div>
      <button type="submit">Login</button>
    </form>
  );
}
```

### Single Handler for Multiple Inputs

```jsx
function RegistrationForm() {
  const [formData, setFormData] = useState({
    firstName: "",
    lastName: "",
    email: "",
    password: "",
    role: "student",
    agreeToTerms: false
  });

  // One handler for ALL inputs
  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Form submitted:", formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="firstName"
        value={formData.firstName}
        onChange={handleChange}
        placeholder="First Name"
      />
      <input
        name="lastName"
        value={formData.lastName}
        onChange={handleChange}
        placeholder="Last Name"
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />
      <select name="role" value={formData.role} onChange={handleChange}>
        <option value="student">Student</option>
        <option value="instructor">Instructor</option>
        <option value="admin">Admin</option>
      </select>
      <label>
        <input
          name="agreeToTerms"
          type="checkbox"
          checked={formData.agreeToTerms}
          onChange={handleChange}
        />
        I agree to the terms
      </label>
      <button type="submit" disabled={!formData.agreeToTerms}>
        Register
      </button>
    </form>
  );
}
```

### Form Validation

```jsx
function ValidatedForm() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    password: "",
    confirmPassword: ""
  });

  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = (data) => {
    const newErrors = {};

    if (!data.name.trim()) {
      newErrors.name = "Name is required";
    } else if (data.name.trim().length < 2) {
      newErrors.name = "Name must be at least 2 characters";
    }

    if (!data.email.trim()) {
      newErrors.email = "Email is required";
    } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email)) {
      newErrors.email = "Invalid email format";
    }

    if (!data.password) {
      newErrors.password = "Password is required";
    } else if (data.password.length < 8) {
      newErrors.password = "Password must be at least 8 characters";
    }

    if (data.password !== data.confirmPassword) {
      newErrors.confirmPassword = "Passwords do not match";
    }

    return newErrors;
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    const updated = { ...formData, [name]: value };
    setFormData(updated);

    // Validate on change if field was already touched
    if (touched[name]) {
      setErrors(validate(updated));
    }
  };

  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    setErrors(validate(formData));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate(formData);
    setErrors(validationErrors);
    setTouched({ name: true, email: true, password: true, confirmPassword: true });

    if (Object.keys(validationErrors).length === 0) {
      console.log("✅ Form is valid:", formData);
    }
  };

  const isValid = Object.keys(errors).length === 0 &&
    Object.keys(touched).length > 0;

  return (
    <form onSubmit={handleSubmit} noValidate>
      <div>
        <input
          name="name"
          value={formData.name}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Full Name"
          className={touched.name && errors.name ? "input-error" : ""}
        />
        {touched.name && errors.name && (
          <span className="error-text">{errors.name}</span>
        )}
      </div>

      <div>
        <input
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Email"
        />
        {touched.email && errors.email && (
          <span className="error-text">{errors.email}</span>
        )}
      </div>

      <div>
        <input
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Password"
        />
        {touched.password && errors.password && (
          <span className="error-text">{errors.password}</span>
        )}
      </div>

      <div>
        <input
          name="confirmPassword"
          type="password"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Confirm Password"
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error-text">{errors.confirmPassword}</span>
        )}
      </div>

      <button type="submit">Register</button>
    </form>
  );
}
```

### Form with API Submission

```jsx
function ContactForm() {
  const [formData, setFormData] = useState({ name: "", email: "", message: "" });
  const [status, setStatus] = useState("idle"); // idle | submitting | success | error

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setStatus("submitting");

    try {
      const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(formData)
      });

      if (!response.ok) throw new Error("Submission failed");
      setStatus("success");
      setFormData({ name: "", email: "", message: "" });
    } catch {
      setStatus("error");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={formData.name} onChange={handleChange} placeholder="Name" required />
      <input name="email" type="email" value={formData.email} onChange={handleChange} placeholder="Email" required />
      <textarea name="message" value={formData.message} onChange={handleChange} placeholder="Message" required />

      <button type="submit" disabled={status === "submitting"}>
        {status === "submitting" ? "Sending..." : "Send Message"}
      </button>

      {status === "success" && <p className="success">✅ Message sent!</p>}
      {status === "error" && <p className="error">❌ Failed. Please try again.</p>}
    </form>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Course Enrollment Form

Build a multi-field enrollment form with validation:

```jsx
function EnrollmentForm() {
  const [form, setForm] = useState({
    fullName: "",
    email: "",
    phone: "",
    course: "",
    experience: "beginner",
    motivation: ""
  });

  const courses = [
    "Full Stack Web Development",
    "Frontend with React",
    "Backend with Node.js",
    "AI & Machine Learning"
  ];

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Welcome, ${form.fullName}! You enrolled in ${form.course}.`);
  };

  return (
    <form onSubmit={handleSubmit} className="max-w-md mx-auto p-6">
      <h2 className="text-2xl font-bold mb-4">🎓 Enroll Now</h2>

      <input name="fullName" value={form.fullName} onChange={handleChange}
        placeholder="Full Name" required className="w-full p-2 border rounded mb-3" />

      <input name="email" type="email" value={form.email} onChange={handleChange}
        placeholder="Email" required className="w-full p-2 border rounded mb-3" />

      <input name="phone" value={form.phone} onChange={handleChange}
        placeholder="Phone" className="w-full p-2 border rounded mb-3" />

      <select name="course" value={form.course} onChange={handleChange}
        required className="w-full p-2 border rounded mb-3">
        <option value="">Select a course</option>
        {courses.map(c => <option key={c} value={c}>{c}</option>)}
      </select>

      <fieldset className="mb-3">
        <legend className="font-medium mb-1">Experience Level</legend>
        {["beginner", "intermediate", "advanced"].map(level => (
          <label key={level} className="mr-4">
            <input type="radio" name="experience" value={level}
              checked={form.experience === level} onChange={handleChange} />
            {" "}{level.charAt(0).toUpperCase() + level.slice(1)}
          </label>
        ))}
      </fieldset>

      <textarea name="motivation" value={form.motivation} onChange={handleChange}
        placeholder="Why do you want to learn?" className="w-full p-2 border rounded mb-3" rows={3} />

      <button type="submit" className="w-full bg-red-600 text-white py-2 rounded hover:bg-red-700">
        Submit Application
      </button>
    </form>
  );
}
```

---

## 📝 Homework

1. Build a **login form** with email/password, "remember me" checkbox, and show/hide password toggle
2. Create a **survey form** with radio buttons, checkboxes, and a textarea — validate all fields
3. Build a **password strength meter** that shows strength as the user types (weak/medium/strong)
4. Create a **dynamic form builder** — add/remove form fields dynamically
5. Build a **search form** with filters (category, price range, sort) that updates results

---

## 💡 Pro Tips

> 📝 **Always use controlled components:** Let React's state be the single source of truth. It makes validation and submission predictable.

> 🎯 **Validate on blur, not change:** For better UX, validate when the user leaves a field (blur), not on every keystroke.

> 🔑 **Use `name` attribute:** Match input `name` to state property names, and you can use a single `handleChange` for all inputs.

---

[← Day 2](day-02.md) | [Back to Week 10](README.md) | [Day 4 →](day-04.md)
