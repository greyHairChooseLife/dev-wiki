# next js basic



> [!rf]
> 
> https://nextjs.org/docs/app/getting-started



## Overview

Here's an overview of features you'll learn about in this course:

- **Styling**: The different ways to style your application in Next.js.
- **Optimizations**: How to optimize images, links, and fonts.
- **Routing**: How to create nested layouts and pages using file-system routing.
- **Data Fetching**: How to set up a Postgres database on Vercel, and best practices for fetching and streaming.
- **Search and Pagination**: How to implement search and pagination using URL search params.
- **Mutating Data**: How to mutate data using React Server Actions, and revalidate the Next.js cache.
- **Error Handling**: How to handle general and 404 not found errors.
- **Form Validation and Accessibility**: How to do server-side form validation and tips for improving accessibility.
- **Authentication**: How to add authentication to your application using NextAuth.js and Proxy.
- **Metadata**: How to add metadata and prepare your application for social sharing.



## Styling


- The create-next-app runs with tailwind.
- The clsx for conditional className.
- The tailwind or css-module is the way.


- e.g.
  ```tsx
  import clsx from 'clsx';
  
  export default function InvoiceStatus({ status }: { status: string }) {
    return (
      <span
        className={clsx(
          'inline-flex items-center rounded-full px-2 py-1 text-xs',
          {
            'bg-gray-100 text-gray-500': status === 'pending',
            'bg-green-500 text-white': status === 'paid',
          },
        )}
      >
  ```



## Optimizations


### fonts

With fonts, layout shift happens when the browser initially renders text in a fallback or system
font and then swaps it out for a custom font once it has loaded. This swap can cause the text size,
spacing, or layout to change, shifting elements around it.

**Next.js automatically optimizes fonts in the application when you use the next/font module.** It
downloads font files **at build time** and hosts them **with** your other **static assets.** 

This means when a user visits your application, there are no additional network requests for fonts
which would impact performance.

### The <Image> component

The <Image> Component is an extension of the HTML <img> tag, and comes with automatic image optimization, such as:

Preventing layout shift automatically when images are loading.
Resizing images to avoid shipping large images to devices with a smaller viewport.
Lazy loading images by default (images load as they enter the viewport).
Serving images in modern formats, like WebP and AVIF, when the browser supports it.



## Routing


### Layout and Pages

- Next.js uses **file system** for a routing.

- The "page" is a special filename; exposing to client. All others in the same directory works behind
  the scene. (e.g. testing, util function ...)

- The "layout" is also special. It makes "nested routing" and "partial rendering" working. States of
  the layout component in the client-side are preserved while rerender the nested components.


### Navigating between Pages

- Just replace <a> tag to <Link> from "next/link".
  Magically optimizes for prefetching on showing links on the viewport.

    - RSC will still be fetched after client manually navigated.


- Use `usePathname()` to get pathname.




## Data Fetching


Learn about some approaches to fetching data: APIs, ORMs, SQL, etc.

How Server Components can help you access back-end resources more securely.

What network waterfalls are.

How to implement parallel data fetching using a JavaScript Pattern.



## Search and Pagination


aa


## Mutating Data


aa


## Error Handling


aa


## Form Validation and Accessibility


aa


## Authentication


aa


## Metadata


aa


