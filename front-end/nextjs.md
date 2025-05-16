# 🧾 Frontend Code Style Guide

**Framework:** Next.js (App Router)  
**Language:** JavaScript only (❌ Do not use TypeScript)  
**Styling:** Tailwind CSS (default only)  
**Icons:** Google Material Icons

This guide defines the file structure, styling rules, and component architecture conventions for building a consistent, scalable frontend.

---

## 📁 1. Project Structure

```
project-root/
  error.jsx           → Global error boundary
  globals.css         → Tailwind global styles
  globalIcons.css     → Google Material Icons loader
/app
  /(pages)           → Route-level pages (e.g., dashboard, settings)
    /[route]/
      page.jsx       → Main page file
      layout.jsx     → If needed, separate layout
    layout.jsx       → Shared layout components (Header, Sidebar, etc.)
  /(auth)            → Authentication pages (e.g., login, register)
    /login/
      page.jsx
    /logout/
      page.jsx
  Layout.jsx

/components
  /layout/           → Shared layout components (e.g., Header, Sidebar)
  /pages/
    /[route]/        → Components specific to each route
    *.jsx           → Additional components used on this page

/lib                 → Server-side utilities (API wrappers).
                       Standard lib folder provided and should always be used.

/utils               → Client-side pure functions (one per file)
```

- `components/pages/[route]` mirrors the structure of `app/(pages)/[route]`.
- Logic shared across pages belongs in `/lib` or `/utils`.

---

## 📄 2. Page Files (`app/(pages)/[route]/page.jsx`)

- Contains only the **server-side data fetch** needed for initial page load.
- Imports the corresponding component from `components/pages/[route]/index.jsx`.
- Passes fetched data directly to the component.
- ❌ No business/rendering logic in `page.jsx`.
- Should always be named `Page()`.

**Example:**

```jsx
// app/(pages)/dashboard/page.jsx
import DashboardPage from "@/components/pages/dashboard";
import { serverApi } from "@/lib/serverFetch";

export default async function Page() {
  const data = await serverApi.get("https://api.example.com/data");
  return <DashboardPage data={data} />;
}
```

If `params` or `searchParams` are needed, it should be like this:

**Example:**

```jsx
// app/(pages)/dashboard/page.jsx
import DashboardPage from "@/components/pages/dashboard";
import { serverApi } from "@/lib/serverFetch";

export default async function Page({ params, searchParams }) {
  const { location } = params;
  const { searchLocation } = searchParams;

  const data = await serverApi.get(
    "https://api.example.com/data" + location + searchLocation
  );
  return <DashboardPage data={data} />;
}
```

- ⚠️ Do **not** include `params` or `searchParams` unless needed

---

## 📦 3. Component Architecture

### `components/pages/[route]`

- Each component must be in its **own file**.
- `index.jsx` is the **main entry point** for the page:
  - Imports and assembles all child components.
  - Contains all **page-specific logic** and computed data.
  - Defines and passes down a centralized object to all components.

**Example Structure:**

```
components/
  pages/
    dashboard/
      index.jsx        → Main container for the dashboard route
      StatsCard.jsx    → UI subcomponent
      ActivityList.jsx → UI subcomponent
```

---

## ⚙️ 4. Route Logic Object

In each `index.jsx`, define an **UPPERCASE object** (e.g., `DASHBOARD`) that holds all logic, state, and derived data for the page.

- This object must be passed as a prop to all child components using the **same UPPERCASE name**.
- The **page component must be named like the route** followed by `Page` (e.g., `DashboardPage` for `/dashboard`).
- ❌ Do not use React Context unless explicitly needed.

**✅ Example:**

```jsx
// components/pages/dashboard/index.jsx
import { useState } from "react";
import StatsCard from "./StatsCard";
import ActivityList from "./ActivityList";

export default function DashboardPage({ data }) {
  const [showDate, setShowDate] = useState(false);

  const DASHBOARD = {
    showDate,
    setShowDate,
    data,
    handleRefresh: () => {
      console.log("Refreshing...");
    },
  };

  return (
    <div className="space-y-4">
      <StatsCard DASHBOARD={DASHBOARD} />
      <ActivityList DASHBOARD={DASHBOARD} />
    </div>
  );
}
```

🧠 **Key Rules Recap**:

- Route logic object is always named in `ALL CAPS`.
- Main page component is named in `PascalCase` matching the route (e.g., `BookingsPage`, `SettingsPage`).
- Props passed to child components must match the object name (`DASHBOARD={DASHBOARD}`).

---


---

## 🧩 4.5 Naming & Client Component Rules

- 📛 **File Names**: All files must use `camelCase` (e.g., `userList.jsx`, `formatDate.js`).
- 🧾 **Props**: Always pass props using the **exact same name** as the object/function (e.g., `DASHBOARD={DASHBOARD}`).
- ⚠️ **Client Components**: Use `"use client"` **only if required** (e.g., when using state, event listeners, or browser-only APIs).
  - Never include `"use client"` unless the component cannot run on the server.

## 🛠️ 5. Utility Functions (`/utils`)

- Utilities should be **pure functions** unrelated to a specific page.
- Only **one function per file**.
- File name must match the function name.

**Example:**

```js
// utils/formatDate.js
export default function formatDate(date) {
  return new Intl.DateTimeFormat("en-US").format(new Date(date));
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
- Load via CDN — do **not install as npm**.
- Create a `globalIcons.css` file in the root of your project and include the following:

```css
@import url("https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200");

.material-symbols-outlined {
  font-variation-settings: "FILL" 0, "wght" 400, "GRAD" -25, "opsz" 40;
}
```

- Import this CSS file in your main `app/layout.jsx`.

**✅ Example Usage:**

```html
<span class="material-symbols-outlined">check_circle</span>
```

---

## ✅ Summary

This style guide ensures:

- Predictable page and component structure
- Clean separation of data, logic, and presentation
- Scalable and maintainable codebase
- Easy onboarding for new developers

All developers must follow this guide unless otherwise specified.
