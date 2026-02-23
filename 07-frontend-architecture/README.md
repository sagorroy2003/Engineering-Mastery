# 🎨 07. Frontend Architecture & Component Design

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: UI Engineering](https://img.shields.io/badge/Focus-UI_Engineering-blue?style=for-the-badge)
![Ecosystem: React](https://img.shields.io/badge/Ecosystem-React-61DAFB?style=for-the-badge)

Modern frontend development is not simply about "making things look good"; it is rigorous software engineering applied to the user interface. When building a complex platform like a multi-vendor marketplace, scattered CSS and duplicated logic will quickly lead to an unmaintainable, sluggish application. 

As a frontend architect, your job is to control state boundaries, minimize unnecessary re-renders, and build a robust design system.

---

## 🌉 The Bridge Between Figma and React

When junior developers look at a Figma file, they immediately start writing raw CSS or scattered Tailwind classes for every single page. If there is a blue button on the login page and a blue button on the checkout page, they code it twice. This is an anti-pattern.

**The Senior Approach: Abstract the Design System.**
Before writing a single page route, you must extract the "Atoms" from the Figma file. Identify the typography scales, the color palette, and the core interactive elements (Buttons, Inputs, Cards, Modals). 



### ❌ The "Junior" Approach (Raw & Duplicated)
```jsx
// checkout.jsx
<button className="bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700 flex items-center justify-center disabled:opacity-50">
  {isLoading ? <Spinner /> : 'Complete Purchase'}
</button>

// login.jsx (Duplicated logic and styling!)
<button className="bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700 disabled:opacity-50">
  Login
</button>
```

### ✅ The "Senior" Approach (Reusable Component Library)
We build a centralized `<Button />` component. The pages should *never* contain raw styling for standard elements.

```jsx
// components/ui/Button.jsx
import { cva } from 'class-variance-authority';

// We define the strict design variants based on the Figma file
const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md font-medium transition-colors disabled:opacity-50",
  {
    variants: {
      variant: {
        primary: "bg-blue-600 text-white hover:bg-blue-700",
        outline: "border border-slate-200 bg-transparent hover:bg-slate-100",
        danger: "bg-red-600 text-white hover:bg-red-700"
      },
      size: {
        sm: "h-9 px-3",
        default: "h-10 px-4 py-2",
        lg: "h-11 px-8"
      }
    },
    defaultVariants: {
      variant: "primary",
      size: "default"
    }
  }
);

export const Button = ({ className, variant, size, isLoading, children, ...props }) => {
  return (
    <button className={buttonVariants({ variant, size, className })} disabled={isLoading || props.disabled} {...props}>
      {isLoading && <Spinner className="mr-2 h-4 w-4" />}
      {children}
    </button>
  );
};
```

*Now, your `checkout.jsx` looks like this:*
```jsx
<Button variant="primary" size="lg" isLoading={isSubmitting}>
  Complete Purchase
</Button>
```

---

## 🏗️ Container vs. Presentational Components

To keep your application highly testable, separate your logic from your UI.

* **Container Components (The Brains):** These components fetch data, manage complex state, and handle side effects. They rarely have HTML/CSS of their own. They pass data down as props.
* **Presentational Components (The Muscle):** These components know *nothing* about APIs or global state. They simply take data via props and render the UI. They are highly reusable and easy to unit test.

---

## 🧠 State Management Strategy

Do not throw everything into Redux or a global Context API. State should live as close to where it is needed as possible.

1. **Local State (`useState`, `useReducer`):** For UI toggles, form inputs, and component-specific behavior.
2. **Server State (React Query / SWR):** For asynchronous data fetched from your API (e.g., fetching the marketplace product list). These libraries handle caching, deduplication, and background refetching automatically.
3. **Global Client State (Zustand / Redux Toolkit):** Use this *only* for state that truly needs to be accessed everywhere (e.g., the authenticated user's session data, the global shopping cart, or a dark mode toggle).

---

## ⚡ Performance Optimization & Re-renders



React is fast, but bad architecture makes it slow. Every time a parent component's state changes, all of its children re-render by default.

### 1. Memoization (`React.memo`, `useMemo`, `useCallback`)
If you have an expensive child component (like a complex chart on the Vendor Dashboard), wrap it in `React.memo`. It will only re-render if its specific props change. Use `useCallback` to prevent function references from breaking this memoization.

*Warning:* Do not over-memoize. Calculating whether to skip a render costs performance too. Only memoize heavy components.

### 2. List Virtualization
If a vendor has 5,000 products in their inventory, rendering 5,000 DOM nodes will crash the browser. Use a library like `react-window` or `react-virtuoso` to "virtualize" the list—meaning React only renders the 20 items currently visible on the user's screen.

---

## 🎯 Practical Exercises
1. **Figma Abstraction:** Take a complex UI element from a Figma file (like a multi-select Dropdown) and build it as a pure, presentational React component that accepts `options`, `value`, and `onChange` props.
2. **Custom Hook Refactoring:** Find a component where you wrote a messy `useEffect` to fetch data. Extract that logic into a custom reusable hook (e.g., `useFetchProducts(categoryId)`).
3. **Performance Profiling:** Install the React Developer Tools extension. Turn on "Highlight updates when components render." Navigate through your app and identify a component that is re-rendering unnecessarily, then fix it using proper state placement or memoization.

## 🎤 Interview Questions
* *"Can you explain the difference between Server State and Global Client State? Why wouldn't you just store your API responses in Redux?"*
* *"You have a deeply nested component tree, and a component 5 levels down needs a specific piece of data from the top. How do you pass it down without 'prop drilling'?"*
* *"Walk me through how `useEffect` works. What is the dependency array, and what happens if you leave it completely empty versus not providing one at all?"*
