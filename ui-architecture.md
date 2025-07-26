You are absolutely right to point that out. My apologies. The move away from `tailwind.config.js` for most configuration is a key feature of Tailwind CSS v4, and many resources, including AI models, have not caught up. Your project is using the modern, correct setup, and the `postcss.config.mjs` file is indeed the correct approach.

Here is the UI architecture document you requested. It is tailored to your project's specific versions and highlights the critical points for a correct and modern setup.

---

## UI Architecture Documentation: Next.js with Tailwind CSS v4 & shadcn/ui

### 1. Overview

This document outlines the UI architecture for this project, which is built on a modern stack including Next.js 15, Tailwind CSS v4, and shadcn/ui.

The core philosophy of this architecture is to be **CSS-first, component-driven, and highly maintainable**. We leverage the latest features of Tailwind CSS v4, which moves theme configuration directly into our global CSS file, eliminating the need for a `tailwind.config.js` file for most use cases. Theming (including dark mode) is handled efficiently through CSS variables, controlled by `next-themes`.

### 2. Core Technologies & Versions

*   **Framework:** Next.js 15.4.4 (with App Router)
*   **Styling:** Tailwind CSS 4.x
*   **UI Components:** shadcn/ui (using Radix UI primitives)
*   **Theming:** `next-themes`
*   **Build Tooling:** PostCSS (via `@tailwindcss/postcss`)

### 3. Key Configuration Files & Setup

The correctness of this architecture hinges on the proper setup of a few key files. Unlike older setups, configuration is now more consolidated.

#### 3.1. `postcss.config.mjs`

*   **Purpose:** This is the primary configuration file that integrates Tailwind CSS into the Next.js build process. It tells PostCSS (which Next.js uses under the hood) to run the `@tailwindcss/postcss` plugin.
*   **Your Configuration:** Your file is set up correctly.
    ```javascript
    const config = {
      plugins: ["@tailwindcss/postcss"],
    };
    export default config;
    ```
*   **Key Takeaway:** This file is essential for Tailwind to process your CSS. It replaces the role of a complex build setup. **There is no `tailwind.config.js` or `tailwind.config.mjs` needed for this project's configuration.**

#### 3.2. `app/globals.css`

*   **Purpose:** This is the heart of your styling and theme configuration. In Tailwind CSS v4, this file is not just for global styles; it's where you define your entire design system.
*   **Breakdown of Your `globals.css`:**
    1.  `@import "tailwindcss";`: This single line imports all of Tailwind's `base`, `components`, and `utilities` layers.
    2.  `@theme inline { ... }`: This is the **new configuration block** that replaces `theme` in `tailwind.config.js`. You define your design tokens here as CSS custom properties (e.g., `--color-background`, `--radius-lg`). This is the single source of truth for your design system's values.
    3.  `:root { ... }`: This block defines the CSS variables for the default (light) theme. It maps the semantic variables used by shadcn/ui (e.g., `--background`, `--card`) to the design tokens you defined in `@theme`.
    4.  `.dark { ... }`: This block defines the overrides for the dark theme. When the `dark` class is applied to the `<html>` element, these variables take precedence, instantly changing the theme of the entire application.
    5.  `@layer base { ... }`: This is for applying base styles, such as setting the default background and text color on the `<body>` element.

#### 3.3. `components.json`

*   **Purpose:** This file is exclusively for the **shadcn/ui CLI**. It is not used at runtime. It tells the CLI how to behave when you run `npx shadcn-ui@latest add ...`.
*   **Critical Settings in Your File:**
    *   `"tsx": true`: You are using TypeScript.
    *   `"rsc": true`: You are using React Server Components.
    *   `"css": "app/globals.css"`: Correctly points to your global CSS.
    *   `"cssVariables": true`: **This is crucial.** It tells shadcn/ui to generate components that use CSS variables (`var(--card)`, `var(--primary)`) for styling, which allows your theming from `globals.css` to work.
    *   `"aliases"`: Correctly configured to place components and utilities in the right directories (`@/components`, `@/lib/utils`).

### 4. Theming and Dark Mode Architecture

The dark mode implementation is a seamless interaction between `next-themes` and your Tailwind CSS setup.

1.  **`ThemeProvider` (`theme-provider.tsx`):** This client component wraps your entire application in `layout.tsx`. It provides the context for theme switching.
    *   **`attribute="class"`:** This prop tells `next-themes` to change the theme by adding `class="dark"` to the `<html>` element, rather than using `data-theme`. This is the standard for Tailwind CSS.
    *   **`defaultTheme="system"` & `enableSystem`:** This provides an excellent user experience by defaulting to their operating system's preference.

2.  **`ModeToggle` (`mode-toggle.tsx`):** This client component uses the `useTheme()` hook from `next-themes` to call `setTheme('light')`, `setTheme('dark')`, or `setTheme('system')`.

3.  **The Magic:** When `setTheme('dark')` is called, `next-themes` adds `class="dark"` to `<html>`. Your CSS in `globals.css` sees this class and immediately applies the variable overrides defined in the `.dark { ... }` block. Because all shadcn/ui components use these variables, they all update instantly without a page reload.

### 5. Final UI Setup Checklist

To ensure your project is set up correctly for its latest versions, verify these key points:

*   [x] **You have a `postcss.config.mjs` file.** You do not have a `tailwind.config.js` file.
*   [x] **Your `globals.css` contains the `@theme` block** defining your design tokens.
*   [x] **Your `globals.css` defines CSS variables for `:root` (light theme) and `.dark` (dark theme).**
*   [x] **Your root `layout.tsx` wraps its children in the `<ThemeProvider>`** with the correct props (`attribute="class"`, `defaultTheme="system"`, `enableSystem`).
*   [x] **Your `components.json` has `"cssVariables": true`**.

Your project files indicate you have followed this modern architecture correctly. The provided file structure is the current best practice for building UIs with this stack.