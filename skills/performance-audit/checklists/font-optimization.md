# Font Optimization Checklist

Use this checklist to ensure fonts are properly optimized.

## Use next/font

- [ ] **All fonts use next/font** - Self-hosted, no external requests
- [ ] **No Google Fonts CDN links** - Remove from `<head>`
- [ ] **Font files are local** - Or loaded via next/font/google

```tsx
// lib/fonts.ts
import { Fraunces, DM_Sans } from "next/font/google";

export const heading = Fraunces({
  subsets: ["latin"],
  display: "swap",
  variable: "--font-heading",
});

export const body = DM_Sans({
  subsets: ["latin"],
  display: "swap",
  variable: "--font-body",
});
```

## Font Display

- [ ] **All fonts have `display: "swap"`** - Prevents invisible text
- [ ] **No flash of invisible text (FOIT)** - Text visible during load
- [ ] **Minimal flash of unstyled text (FOUT)** - System font fallback OK

```tsx
const font = Inter({
  subsets: ["latin"],
  display: "swap", // Critical for performance
});
```

## Variable Fonts

- [ ] **Use variable fonts when possible** - Single file, multiple weights
- [ ] **Specify axes for variable fonts** - Optional optical size, etc.

```tsx
const heading = Fraunces({
  subsets: ["latin"],
  display: "swap",
  variable: "--font-heading",
  axes: ["SOFT", "WONK", "opsz"], // Variable font axes
});
```

## Font Subsets

- [ ] **Only include needed subsets** - Usually just "latin"
- [ ] **Remove unused subsets** - Reduces file size

```tsx
// GOOD - Only needed subset
const font = Inter({
  subsets: ["latin"],
});

// AVOID unless needed
const font = Inter({
  subsets: ["latin", "cyrillic", "greek", "vietnamese"],
});
```

## Font Weights

- [ ] **Only include used weights** - Don't load all weights
- [ ] **Variable fonts for multiple weights** - More efficient

```tsx
// Static font - specify only needed weights
const font = DM_Sans({
  subsets: ["latin"],
  weight: ["400", "500", "700"], // Only what you use
});

// Variable font - automatic
const font = Inter({
  subsets: ["latin"],
  // All weights available automatically
});
```

## CSS Variables

- [ ] **Fonts exposed as CSS variables** - For Tailwind integration
- [ ] **Variables applied in layout** - Once, at root level

```tsx
// layout.tsx
import { heading, body } from "@/lib/fonts";

export default function RootLayout({ children }) {
  return (
    <html className={`${heading.variable} ${body.variable}`}>
      <body className="font-body">
        {children}
      </body>
    </html>
  );
}
```

```tsx
// tailwind.config.ts
fontFamily: {
  heading: ["var(--font-heading)", "serif"],
  body: ["var(--font-body)", "sans-serif"],
}
```

## Local Fonts

For fonts not on Google Fonts:

- [ ] **Use localFont** - For custom font files
- [ ] **Provide multiple formats** - woff2 preferred
- [ ] **Include all needed weights** - As separate src entries

```tsx
import localFont from "next/font/local";

const customFont = localFont({
  src: [
    {
      path: "../public/fonts/Custom-Regular.woff2",
      weight: "400",
      style: "normal",
    },
    {
      path: "../public/fonts/Custom-Bold.woff2",
      weight: "700",
      style: "normal",
    },
  ],
  display: "swap",
  variable: "--font-custom",
});
```

## Remove Unused Fonts

- [ ] **No unused @font-face rules** - In CSS
- [ ] **No unused font imports** - In JavaScript
- [ ] **No external font stylesheet links** - In HTML head

## Common Issues

### Flash of Invisible Text (FOIT)

```tsx
// BAD - No display swap
const font = Inter({ subsets: ["latin"] });

// GOOD - Display swap
const font = Inter({ subsets: ["latin"], display: "swap" });
```

### Font Loading Delay

```tsx
// BAD - External CDN
<link href="https://fonts.googleapis.com/..." rel="stylesheet" />

// GOOD - Self-hosted via next/font
import { Inter } from "next/font/google";
```

### Unused Font Weights

```tsx
// BAD - Loading weights you don't use
const font = Inter({
  subsets: ["latin"],
  weight: ["100", "200", "300", "400", "500", "600", "700", "800", "900"],
});

// GOOD - Only needed weights
const font = Inter({
  subsets: ["latin"],
  weight: ["400", "600", "700"],
});
```

## Verification

After optimization:

- [ ] Run Lighthouse - No font-related warnings
- [ ] Check Network tab - Font files loaded efficiently
- [ ] Test slow connection - Text visible immediately
- [ ] Verify no CLS - From font swap
