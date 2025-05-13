# 🧾 Frontend Code Style Guide  
**Framework:** Next.js (App Router)  
**Styling:** Tailwind CSS (default only)  
**Icons:** Google Material Icons

This guide defines the file structure, styling rules, and component architecture conventions for building a consistent, scalable frontend.

---

## 📁 1. Project Structure

```
/app
  /pages           → Route-level pages (e.g., dashboard, settings)
  /auth            → Authentication pages (e.g., login, register)
/components
  /layout          → Shared layout components (e.g., Header, Sidebar)
  /pages
    /[route]       → Components specific to each route
/lib               → Server-side utilities (e.g., DB, API wrappers)
/utils             → Client-side pure functions (one per file)
```

- `components/pages/[route]` mirrors the structure of `app/pages/[route]`.
- Logic shared across pages belongs in `/lib` or `/utils`.

---

## 📄 2. Page Files (`app/pages/[route]/page.tsx`)

- Contains only the **server-side data fetch** needed for initial page load.
- Imports the corresponding component from `components/pages/[route]/index.tsx`.
- Passes fetched data directly to the component.
- ❌ No business/rendering logic in `page.tsx`.

**Example:**
```tsx
// app/pages/dashboard/page.tsx
import DashboardPage from '@/components/pages/dashboard';

export default async function Page() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return <DashboardPage data={data} />;
}
```

---

## 📦 3. Component Architecture

### `components/pages/[route]`

- Each component is in its **own file**.
- `index.tsx` is the **main entry point** and:
  - Imports and assembles all components
  - Contains all shared logic for the page
  - Passes a centralized object down to child components

**Example File Structure:**
```
components/
  pages/
    dashboard/
      index.tsx
      StatsCard.tsx
      ActivityList.tsx
```

---

## ⚙️ 4. Route Logic Object

In each `index.tsx`, define a **PascalCase object** (e.g., `Dashboard`) that holds all the logic, event handlers, and derived data for the page.

- This object is passed down to all components.
- ❌ Do not use React Context unless explicitly needed.

**Example:**
```tsx
const Dashboard = {
  handleRefresh: () => { ... },
  stats: [ ... ]
};

export default function DashboardPage({ data }) {
  return (
    <div>
      <StatsCard Dashboard={Dashboard} />
      <ActivityList Dashboard={Dashboard} />
    </div>
  );
}
```

---

## 🛠️ 5. Utility Functions (`/utils`)

- Utilities should be **pure functions** unrelated to a specific page.
- Only **one function per file**.
- File name must match the function name.

**Example:**
```js
// utils/formatDate.js
export default function formatDate(date) {
  return new Intl.DateTimeFormat('en-US').format(new Date(date));
}
```

✅ This keeps utilities reusable and easy to import without bloat.

---

## 🎨 6. Styling Rules

- ✅ Use only **plain, out-of-the-box Tailwind CSS**.
- ❌ No custom Tailwind configs or `@apply`.
- Keep class usage simple and readable.
- Group classes by:
  1. Layout (e.g. `flex`, `grid`)
  2. Color (`bg-`, `text-`)
  3. Spacing (`p-4`, `mt-2`)
  4. Typography (`text-sm`, `font-bold`)

---

## 🎯 7. Icons

- Use only **Google Material Icons**.
- Load via CDN or install using `@mui/icons-material` if needed.

**Example (HTML style):**
```html
<span class="material-icons">check_circle</span>
```

**Example (React):**
```tsx
import CheckCircleIcon from '@mui/icons-material/CheckCircle';

<CheckCircleIcon className="text-green-600" />
```

---

## ✅ Summary

This style guide ensures:
- Predictable page and component structure
- Clean separation of data, logic, and presentation
- Scalable and maintainable codebase
- Easy onboarding for new developers

All developers must follow this guide unless otherwise specified.

---
