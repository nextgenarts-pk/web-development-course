# 📅 Week 12 — Day 3: Advanced Component Patterns 🧩

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Learn the compound component pattern
- Understand the render props pattern
- Build higher-order components (HOCs)
- Use the children-as-function pattern
- Know when to use each pattern vs. hooks

---

## 📖 Lesson Content

### 1. Why Advanced Patterns?

As apps grow, you need **flexible, reusable** components. Advanced patterns let you:

- Share logic without duplicating code
- Build components that are flexible about **what** they render
- Create UI libraries that work for many use cases

### 2. Compound Components

A group of components that work together and share implicit state — like `<select>` and `<option>` in HTML.

```jsx
// components/Accordion.jsx
import { createContext, useContext, useState } from 'react';

const AccordionContext = createContext();

function Accordion({ children, defaultIndex = -1 }) {
  const [activeIndex, setActiveIndex] = useState(defaultIndex);

  function toggle(index) {
    setActiveIndex(prev => (prev === index ? -1 : index));
  }

  return (
    <AccordionContext.Provider value={{ activeIndex, toggle }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

function AccordionItem({ children, index }) {
  const { activeIndex, toggle } = useContext(AccordionContext);
  const isOpen = activeIndex === index;

  return (
    <div className={`accordion-item ${isOpen ? 'open' : ''}`}>
      {typeof children === 'function'
        ? children({ isOpen, toggle: () => toggle(index) })
        : children}
    </div>
  );
}

function AccordionHeader({ children, index }) {
  const { activeIndex, toggle } = useContext(AccordionContext);
  const isOpen = activeIndex === index;

  return (
    <button className="accordion-header" onClick={() => toggle(index)}>
      {children}
      <span>{isOpen ? '▲' : '▼'}</span>
    </button>
  );
}

function AccordionPanel({ children, index }) {
  const { activeIndex } = useContext(AccordionContext);
  const isOpen = activeIndex === index;

  if (!isOpen) return null;

  return <div className="accordion-panel">{children}</div>;
}

// Attach sub-components
Accordion.Item = AccordionItem;
Accordion.Header = AccordionHeader;
Accordion.Panel = AccordionPanel;

export default Accordion;
```

**Usage:**

```jsx
<Accordion defaultIndex={0}>
  <Accordion.Item index={0}>
    <Accordion.Header index={0}>What is React?</Accordion.Header>
    <Accordion.Panel index={0}>
      React is a JavaScript library for building user interfaces.
    </Accordion.Panel>
  </Accordion.Item>

  <Accordion.Item index={1}>
    <Accordion.Header index={1}>What is JSX?</Accordion.Header>
    <Accordion.Panel index={1}>
      JSX is a syntax extension that lets you write HTML-like code in JavaScript.
    </Accordion.Panel>
  </Accordion.Item>
</Accordion>
```

The consumer controls **structure and content** while the Accordion manages **state and behavior**.

### 3. Tabs — Another Compound Component

```jsx
import { createContext, useContext, useState } from 'react';

const TabsContext = createContext();

function Tabs({ children, defaultTab = 0 }) {
  const [activeTab, setActiveTab] = useState(defaultTab);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list" role="tablist">{children}</div>;
}

function Tab({ children, index }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);

  return (
    <button
      role="tab"
      className={`tab ${activeTab === index ? 'tab--active' : ''}`}
      onClick={() => setActiveTab(index)}
    >
      {children}
    </button>
  );
}

function TabPanels({ children }) {
  const { activeTab } = useContext(TabsContext);
  return <div className="tab-panels">{children[activeTab]}</div>;
}

function TabPanel({ children }) {
  return <div className="tab-panel" role="tabpanel">{children}</div>;
}

Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panels = TabPanels;
Tabs.Panel = TabPanel;

export default Tabs;
```

**Usage:**

```jsx
<Tabs defaultTab={0}>
  <Tabs.List>
    <Tabs.Tab index={0}>Overview</Tabs.Tab>
    <Tabs.Tab index={1}>Specs</Tabs.Tab>
    <Tabs.Tab index={2}>Reviews</Tabs.Tab>
  </Tabs.List>

  <Tabs.Panels>
    <Tabs.Panel><p>Product overview content...</p></Tabs.Panel>
    <Tabs.Panel><p>Technical specifications...</p></Tabs.Panel>
    <Tabs.Panel><p>Customer reviews...</p></Tabs.Panel>
  </Tabs.Panels>
</Tabs>
```

### 4. Render Props Pattern

A component that takes a **function as a prop** and calls it with data:

```jsx
// components/MouseTracker.jsx
import { useState, useEffect } from 'react';

function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return render(position);
}

export default MouseTracker;
```

**Usage:**

```jsx
<MouseTracker
  render={({ x, y }) => (
    <div>
      <p>Mouse: ({x}, {y})</p>
      <div
        className="dot"
        style={{ left: x - 5, top: y - 5, position: 'fixed' }}
      />
    </div>
  )}
/>
```

### 5. Data Fetcher with Render Props

```jsx
function DataFetcher({ url, render }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(json => { if (!cancelled) setData(json); })
      .catch(err => { if (!cancelled) setError(err.message); })
      .finally(() => { if (!cancelled) setLoading(false); });

    return () => { cancelled = true; };
  }, [url]);

  return render({ data, loading, error });
}

// Usage
<DataFetcher
  url="https://jsonplaceholder.typicode.com/posts?_limit=5"
  render={({ data, loading, error }) => {
    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error}</p>;
    return (
      <ul>
        {data.map(post => <li key={post.id}>{post.title}</li>)}
      </ul>
    );
  }}
/>
```

> **Note:** In modern React, custom hooks (`useFetch`) have largely replaced render props for logic sharing. Render props are still useful when you need **the parent to control the rendering**.

### 6. Higher-Order Components (HOCs)

A function that takes a component and returns a **new enhanced component**:

```jsx
// hocs/withLoading.jsx
function withLoading(WrappedComponent) {
  return function WithLoadingComponent({ loading, ...props }) {
    if (loading) return <p>Loading...</p>;
    return <WrappedComponent {...props} />;
  };
}

// Usage
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}

const UserListWithLoading = withLoading(UserList);

// In parent:
<UserListWithLoading loading={isLoading} users={users} />
```

### 7. HOC — withAuth

```jsx
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { isLoggedIn } = useAuth();

    if (!isLoggedIn) {
      return <Navigate to="/login" replace />;
    }

    return <WrappedComponent {...props} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
```

### 8. Pattern Comparison

| Pattern | Use When | Modern Alternative |
|---------|----------|-------------------|
| **Compound** | Building flexible UI kits (Tabs, Accordion, Menu) | Still best for this |
| **Render Props** | Parent needs to control what's rendered | Custom hooks |
| **HOCs** | Adding behavior to many components | Custom hooks |
| **Custom Hooks** | Sharing stateful logic | The default choice |

> **In 2025:** Custom hooks are the default. Use compound components for UI libraries. Render props and HOCs are less common but important to recognize in existing code.

---

## 💪 Hands-On Exercise

### Build a Modal Compound Component

1. Create `<Modal>`, `<Modal.Trigger>`, `<Modal.Content>`, `<Modal.Close>`
2. Modal manages open/close state via context
3. `Modal.Trigger` opens the modal
4. `Modal.Content` renders in a portal overlay
5. `Modal.Close` closes the modal
6. Close on overlay click and Escape key

---

## 📝 Homework

1. Build a `<Dropdown>` compound component (Dropdown, Dropdown.Toggle, Dropdown.Menu, Dropdown.Item)
2. Create a `withLogger` HOC that logs component renders to the console
3. Create a `<Toggle>` render prop component: `<Toggle render={({ on, toggle }) => ...} />`
4. Refactor the `MouseTracker` render prop into a `useMouse` custom hook — compare the API

---

## 💡 Pro Tips

- Compound components use **Context** internally — the consumer doesn't need to know
- Attach sub-components with `Parent.Child = ChildComponent` for dot-notation imports
- HOCs should start with `with` by convention: `withAuth`, `withLoading`
- If you're choosing between render props and custom hooks for new code — choose hooks

---

[← Day 2](day-02.md) | [Back to Week 12](README.md) | [Day 4 →](day-04.md)
