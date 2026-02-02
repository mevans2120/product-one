# Core Web Vitals Checklist

Use this checklist to optimize the three Core Web Vitals metrics.

## Overview

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP (Largest Contentful Paint) | ≤2.5s | 2.5-4s | >4s |
| INP (Interaction to Next Paint) | ≤200ms | 200-500ms | >500ms |
| CLS (Cumulative Layout Shift) | ≤0.1 | 0.1-0.25 | >0.25 |

## LCP (Largest Contentful Paint)

LCP measures when the largest content element becomes visible.

### Common LCP Elements

- [ ] Hero images
- [ ] Main heading (h1)
- [ ] Featured video thumbnails
- [ ] Large text blocks

### Optimization Checklist

- [ ] **Preload LCP image** - Add priority or preload link
- [ ] **Optimize image size** - Right dimensions, compressed
- [ ] **Use modern formats** - WebP/AVIF via next/image
- [ ] **Reduce server response time** - TTFB < 0.8s
- [ ] **Minimize render-blocking resources** - Defer non-critical CSS/JS

```tsx
// Hero image with priority
<Image
  src="/hero.jpg"
  alt="Hero"
  width={1200}
  height={600}
  priority
/>
```

```tsx
// Or preload in head
<link
  rel="preload"
  as="image"
  href="/hero.jpg"
/>
```

### Quick Wins

1. Add `priority` to hero image
2. Use CDN for static assets
3. Enable caching headers
4. Compress images to < 200KB

## INP (Interaction to Next Paint)

INP measures responsiveness to user interactions.

### Common Causes of Poor INP

- [ ] Long JavaScript tasks (>50ms)
- [ ] Heavy event handlers
- [ ] Layout thrashing
- [ ] Expensive rendering

### Optimization Checklist

- [ ] **Break up long tasks** - Use `setTimeout`, `requestIdleCallback`
- [ ] **Debounce/throttle handlers** - For scroll, resize, input
- [ ] **Use CSS transitions** - Instead of JS animations
- [ ] **Lazy load components** - Code split heavy features
- [ ] **Virtualize long lists** - Don't render everything

```tsx
// Debounce search input
import { useDebouncedCallback } from "use-debounce";

const handleSearch = useDebouncedCallback((value) => {
  search(value);
}, 300);
```

```tsx
// Lazy load heavy component
import dynamic from "next/dynamic";

const HeavyChart = dynamic(() => import("./Chart"), {
  loading: () => <ChartSkeleton />,
});
```

### Quick Wins

1. Move heavy computation to Web Workers
2. Use `startTransition` for non-urgent updates
3. Debounce form inputs
4. Lazy load below-fold content

## CLS (Cumulative Layout Shift)

CLS measures visual stability - how much layout shifts during page load.

### Common Causes of CLS

- [ ] Images without dimensions
- [ ] Ads or embeds without reserved space
- [ ] Dynamically injected content
- [ ] Web fonts causing reflow

### Optimization Checklist

- [ ] **Set explicit image dimensions** - Width and height attributes
- [ ] **Reserve space for embeds** - Fixed aspect ratio containers
- [ ] **Use font-display: swap** - Minimize font swap impact
- [ ] **Avoid inserting content above existing** - Only append below
- [ ] **Use transform for animations** - Instead of layout properties

```tsx
// Always set dimensions
<Image
  src="/photo.jpg"
  alt=""
  width={400}
  height={300}
/>
```

```tsx
// Reserve space for embeds
<div className="relative aspect-video">
  <iframe
    src="https://youtube.com/embed/..."
    className="absolute inset-0 w-full h-full"
  />
</div>
```

```tsx
// Use transform for animations
// BAD
.animate { margin-top: 10px; }

// GOOD
.animate { transform: translateY(10px); }
```

### Quick Wins

1. Add width/height to all images
2. Use aspect-ratio containers for embeds
3. Use next/font for fonts
4. Avoid layout animations

## Testing Core Web Vitals

### Tools

- **Lighthouse** - DevTools or CLI
- **PageSpeed Insights** - Real user data
- **Chrome DevTools** - Performance panel
- **Web Vitals Extension** - Real-time metrics

### Commands

```bash
# Lighthouse CLI
npx lighthouse https://your-site.com --only-categories=performance

# With specific form factor
npx lighthouse https://your-site.com --form-factor=mobile
```

### Verification

- [ ] Test on production (not localhost)
- [ ] Test mobile viewport (usually worse)
- [ ] Test with network throttling
- [ ] Test multiple page loads (cache effects)

## Priority Matrix

| Issue | Impact | Effort | Priority |
|-------|--------|--------|----------|
| Missing image dimensions | High CLS | Low | 1 |
| Unoptimized hero image | High LCP | Low | 1 |
| Render-blocking scripts | Med LCP | Med | 2 |
| Long JS tasks | High INP | High | 2 |
| Font loading | Med CLS | Low | 3 |
| Third-party scripts | Varies | High | 4 |

## Common Patterns

### Hero Section

```tsx
// Optimized hero
<section className="relative h-[60vh] min-h-[400px]">
  <Image
    src="/hero.jpg"
    alt="Hero banner"
    fill
    priority
    className="object-cover"
    sizes="100vw"
  />
  <div className="relative z-10">
    <h1 className="font-heading text-5xl">
      {headline}
    </h1>
  </div>
</section>
```

### Image Gallery

```tsx
// Grid with fixed aspect ratios
<div className="grid grid-cols-3 gap-4">
  {images.map((img) => (
    <div key={img.id} className="relative aspect-square">
      <Image
        src={img.url}
        alt={img.alt}
        fill
        className="object-cover"
        sizes="(max-width: 768px) 33vw, 25vw"
      />
    </div>
  ))}
</div>
```

### Video Embed

```tsx
// Reserved space for video
<div className="relative aspect-video w-full bg-gray-100">
  <iframe
    src={videoUrl}
    className="absolute inset-0 w-full h-full"
    loading="lazy"
    allowFullScreen
  />
</div>
```
