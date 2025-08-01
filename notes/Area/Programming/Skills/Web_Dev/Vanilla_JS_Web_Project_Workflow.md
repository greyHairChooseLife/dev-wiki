# 󰏢 Vanilla JS Web Project Workflow



## Minimal HTML Structure

- Use only essential tags for layout.
- Include semantic elements (`<main>`, `<nav>`, etc.) for accessibility.
- Add ARIA attributes where needed for assistive technologies.


## JavaScript Logic

1. **Query DOM Elements**
   - Use `document.querySelector` or `getElementById` to select elements.
   - Cache references if used often.

2. **Fetch and Parse Data**
   - Use `fetch()` to get data from the backend.
   - Handle errors gracefully (try/catch or `.catch()`).

3. **Create Elements Dynamically**
   - Use `document.createElement` to build new elements.
   - Set text with `textContent` (safer than `innerHTML`).
   - Add classes for styling.
   - Add ARIA attributes for accessibility if needed.
   - Add event listeners for interactivity.
   - For many elements, use a `DocumentFragment` to batch DOM updates for better performance.

4. **Event Delegation**
   - For lists or dynamic content, add event listeners to parent elements.
   - Use `event.target` to handle events from child elements.

5. **Conditional Rendering**
   - Append or remove elements based on your logic.
   - Use clear conditions and comments if complex.


## 3. CSS Styling

- Style using class names for maintainability.
- Consider naming conventions (like BEM) for scalability.
- Use media queries for responsiveness.


## 4. Accessibility & Performance

- Use semantic HTML and ARIA attributes.
- Batch DOM updates with `DocumentFragment`.
- Use event delegation for dynamic lists.
- Test with keyboard and screen reader if possible.

### Example Pseudocode

```javascript
// 1. Get DOM elements
const list = document.querySelector('ul');

// 2. Fetch data
fetch('/api/items')
  .then(res => res.json())
  .then(data => {
    // 3. Create elements with DocumentFragment
    const fragment = document.createDocumentFragment();
    data.forEach(item => {
      const li = document.createElement('li');
      li.textContent = item.name;
      li.className = 'item';
      li.setAttribute('aria-label', item.name);
      fragment.appendChild(li);
    });
    list.appendChild(fragment);
  });

// 4. Event delegation
list.addEventListener('click', event => {
  if (event.target.classList.contains('item')) {
    alert(`Clicked: ${event.target.textContent}`);
  }
});
```
