# Image Optimization Checklist

Use this checklist to ensure all images are properly optimized.

## Next.js Image Component

- [ ] **All images use `next/image`** - Never use raw `<img>` tags
- [ ] **Width and height specified** - Prevents CLS
- [ ] **Alt text is descriptive** - Not just "image" or empty

```tsx
import Image from "next/image";

<Image
  src="/photo.jpg"
  alt="Description of what's in the image"
  width={800}
  height={600}
/>
```

## Above-the-Fold Images

- [ ] **Hero image has `priority`** - Preloads for LCP
- [ ] **Other ATF images have `priority`** - No lazy loading above fold
- [ ] **No `loading="lazy"` on ATF images** - Priority handles this

```tsx
<Image
  src="/hero.jpg"
  alt="Hero banner"
  width={1200}
  height={600}
  priority // Critical for LCP
/>
```

## Below-the-Fold Images

- [ ] **Default lazy loading** - next/image does this automatically
- [ ] **Placeholder blur** - Smooth loading experience

```tsx
<Image
  src="/gallery-photo.jpg"
  alt="Gallery photo"
  width={400}
  height={300}
  placeholder="blur"
  blurDataURL="/gallery-photo-blur.jpg" // Or use static import
/>
```

## Responsive Images

- [ ] **Use `fill` for unknown dimensions** - With container sizing
- [ ] **Use `sizes` for responsive** - Tells browser expected display size

```tsx
// For images that fill their container
<div className="relative h-[400px]">
  <Image
    src="/banner.jpg"
    alt="Banner"
    fill
    className="object-cover"
    sizes="100vw"
  />
</div>

// For responsive images
<Image
  src="/photo.jpg"
  alt="Photo"
  width={800}
  height={600}
  sizes="(max-width: 768px) 100vw, 50vw"
/>
```

## CMS Images (Sanity)

- [ ] **Use Sanity image URL builder** - Proper sizing and format
- [ ] **Specify dimensions in query** - Or use asset metadata

```tsx
import imageUrlBuilder from "@sanity/image-url";

const builder = imageUrlBuilder(client);

function urlFor(source) {
  return builder.image(source);
}

// In component
<Image
  src={urlFor(image).width(800).height(600).url()}
  alt={image.alt || ""}
  width={800}
  height={600}
/>
```

## Image Formats

- [ ] **Use WebP/AVIF for modern browsers** - next/image handles this
- [ ] **Fallback formats for older browsers** - Automatic with next/image
- [ ] **SVG for icons and logos** - Scalable, small file size

## File Sizes

- [ ] **Hero image < 200KB** - After optimization
- [ ] **Content images < 100KB** - Most images
- [ ] **Thumbnails < 20KB** - Small preview images
- [ ] **No images > 500KB** - Unless absolutely necessary

## Common Issues

### CLS from Images

```tsx
// BAD - No dimensions
<Image src="/photo.jpg" alt="" />

// GOOD - Explicit dimensions
<Image src="/photo.jpg" alt="" width={400} height={300} />
```

### Slow LCP

```tsx
// BAD - Lazy loading hero
<Image src="/hero.jpg" alt="" width={1200} height={600} loading="lazy" />

// GOOD - Priority loading
<Image src="/hero.jpg" alt="" width={1200} height={600} priority />
```

### Oversized Images

```tsx
// BAD - Full resolution for thumbnail
<Image src="/photo-4000x3000.jpg" width={100} height={75} />

// GOOD - Use srcset or resize on server
<Image
  src="/photo.jpg"
  width={100}
  height={75}
  sizes="100px"
/>
```

## Verification

After optimization:

- [ ] Run Lighthouse - LCP should be < 2.5s
- [ ] Check Network tab - Image sizes reasonable
- [ ] Test on mobile - Often worse than desktop
- [ ] Verify CLS - No layout shifts from images
