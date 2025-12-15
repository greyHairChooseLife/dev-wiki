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


With fonts, layout shift happens when the browser initially renders text in a fallback or system
font and then swaps it out for a custom font once it has loaded. This swap can cause the text size,
spacing, or layout to change, shifting elements around it.

**Next.js automatically optimizes fonts in the application when you use the next/font module.** It
downloads font files **at build time** and hosts them **with** your other **static assets.** 

This means when a user visits your application, there are no additional network requests for fonts
which would impact performance.


### fonts



## Routing


aa


## Data Fetching


aa


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


