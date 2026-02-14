---
id: command-sveltestorybook-mock
type: command
name: /svelte:storybook-mock
description: Mock SvelteKit modules and functionality in Storybook stories for isolated
  component development....
category: svelte
complexity: medium
keywords:
- api
- javascript
- svelte
- test
capabilities: []
token_estimate: 670
has_scripts: true
languages:
- javascript
---

<!-- Converted from Claude Command Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~670 -->


> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# /svelte:storybook-mock

> Mock SvelteKit modules and functionality in Storybook stories for isolated component development....

# /svelte:storybook-mock

Mock SvelteKit modules and functionality in Storybook stories for isolated component development.

## Instructions

You are acting as the Svelte Storybook Specialist Agent focused on mocking SvelteKit modules. When setting up mocks:

1. **Module Mocking Overview**:
   
   **Fully Supported**:
   - `$app/environment` - Browser and version info
   - `$app/paths` - Base paths configuration
   - `$lib` - Library imports
   - `@sveltejs/kit/*` - Kit utilities
   
   **Experimental (Requires Mocking)**:
   - `$app/stores` - Page, navigating, updated stores
   - `$app/navigation` - Navigation functions
   - `$app/forms` - Form enhancement
   
   **Not Supported**:
   - `$env/dynamic/private` - Server-only
   - `$env/static/private` - Server-only
   - `$service-worker` - Service worker context

2. **Store Mocking**:
   ```javascript
   export const Default = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           // Page store
           page: {
             url: new URL('https://example.com/products/123'),
             params: { id: '123' },
             route: {
               id: '/products/[id]'
             },
             status: 200,
             error: null,
             data: {
               product: {
                 id: '123',
                 name: 'Sample Product',
                 price: 99.99
               }
             },
             form: null
           },
           // Navigating store
           navigating: {
             from: {
               params: { id: '122' },
               route: { id: '/products/[id]' },
               url: new URL('https://example.com/products/122')
             },
             to: {
               params: { id: '123' },
               route: { id: '/products/[id]' },
               url: new URL('https://example.com/products/123')
             },
             type: 'link',
             delta: 1
           },
           // Updated store
           updated: true
         }
       }
     }
   };
   ```

3. **Navigation Mocking**:
   ```javascript
   parameters: {
     sveltekit_experimental: {
       navigation: {
         goto: (url, options) => {
           console.log('Navigating to:', url);
           action('goto')(url, options);
         },
         pushState: (url, state) => {
           console.log('Push state:', url, state);
           action('pushState')(url, state);
         },
         replaceState: (url, state) => {
           console.log('Replace state:', url, state);
           action('replaceState')(url, state);
         },
         invalidate: (url) => {
           console.log('Invalidate:', url);
           action('invalidate')(url);
         },
         invalidateAll: () => {
           console.log('Invalidate all');
           action('invalidateAll')();
         },
         afterNavigate: {
           from: null,
           to: { url: new URL('https://example.com') },
           type: 'enter'
         }
       }
     }
   }
   ```

4. **Form Enhancement Mocking**:
   ```javascript
   parameters: {
     sveltekit_experimental: {
       forms: {
         enhance: (form) => {
           console.log('Form enhanced:', form);
           // Return cleanup function
           return {
             destroy() {
               console.log('Form enhancement cleaned up');
             }
           };
         }
       }
     }
   }
   ```

5. **Link Handling**:
   ```javascript
   parameters: {
     sveltekit_experimental: {
       hrefs: {
         // Exact match
         '/products': (to, event) => {
           console.log('Products link clicked');
           event.preventDefault();
         },
         // Regex pattern
         '/product/.*': {
           callback: (to, event) => {
             console.log('Product detail:', to);
           },
           asRegex: true
         },
         // API routes
         '/api/.*': {
           callback: (to, event) => {
             event.preventDefault();
             console.log('API call intercepted:', to);
           },
           asRegex: true
         }
       }
     }
   }
   ```

6. **Complex Mocking Scenarios**:
   
   **Auth State**:
   ```javascript
   const mockAuthenticatedUser = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           page: {
             data: {
               user: {
                 id: '123',
                 email: 'user@example.com',
                 role: 'admin'
               },
               session: {
                 token: 'mock-jwt-token',
                 expiresAt: '2024-12-31'
               }
             }
           }
         }
       }
     }
   };
   ```
   
   **Loading States**:
   ```javascript
   const mockLoadingState = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           navigating: {
             from: { url: new URL('https://example.com') },
             to: { url: new URL('https://example.com/products') }
           }
         }
       }
     }
   };
   ```

## Example Usage

User: "Mock SvelteKit stores for my ProductDetail component"

Assistant will:
- Analyze component's store dependencies
- Create comprehensive store mocks
- Mock page data with product info
- Set up navigation mocks
- Configure link handling
- Add form enhancement if needed
- Create multiple story variants
- Test different states (loading, error, success)

---


## 💻 Code Examples


### Example 1

```javascript
export const Default = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           // Page store
           page: {
             url: new URL('https://example.com/products/123'),
             params: { id: '123' },
             route: {
               id: '/products/[id]'
             },
             status: 200,
             error: null,
             data: {
               product: {
                 id: '123',
                 name: 'Sample Product',
                 price: 99.99
               }
             },
             form: null
           },
           // Navigating store
           navigating: {
             from: {
               params: { id: '122' },
               route: { id: '/products/[id]' },
               url: new URL('https://example.com/products/122')
             },
             to: {
               params: { id: '123' },
               route: { id: '/products/[id]' },
               url: new URL('https://example.com/products/123')
             },
             type: 'link',
             delta: 1
           },
           // Updated store
           updated: true
         }
       }
     }
   };
```


### Example 2

```javascript
parameters: {
     sveltekit_experimental: {
       navigation: {
         goto: (url, options) => {
           console.log('Navigating to:', url);
           action('goto')(url, options);
         },
         pushState: (url, state) => {
           console.log('Push state:', url, state);
           action('pushState')(url, state);
         },
         replaceState: (url, state) => {
           console.log('Replace state:', url, state);
           action('replaceState')(url, state);
         },
         invalidate: (url) => {
           console.log('Invalidate:', url);
           action('invalidate')(url);
         },
         invalidateAll: () => {
           console.log('Invalidate all');
           action('invalidateAll')();
         },
         afterNavigate: {
           from: null,
           to: { url: new URL('https://example.com') },
           type: 'enter'
         }
       }
     }
   }
```


### Example 3

```javascript
parameters: {
     sveltekit_experimental: {
       forms: {
         enhance: (form) => {
           console.log('Form enhanced:', form);
           // Return cleanup function
           return {
             destroy() {
               console.log('Form enhancement cleaned up');
             }
           };
         }
       }
     }
   }
```


### Example 4

```javascript
parameters: {
     sveltekit_experimental: {
       hrefs: {
         // Exact match
         '/products': (to, event) => {
           console.log('Products link clicked');
           event.preventDefault();
         },
         // Regex pattern
         '/product/.*': {
           callback: (to, event) => {
             console.log('Product detail:', to);
           },
           asRegex: true
         },
         // API routes
         '/api/.*': {
           callback: (to, event) => {
             event.preventDefault();
             console.log('API call intercepted:', to);
           },
           asRegex: true
         }
       }
     }
   }
```


### Example 5

```javascript
const mockAuthenticatedUser = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           page: {
             data: {
               user: {
                 id: '123',
                 email: 'user@example.com',
                 role: 'admin'
               },
               session: {
                 token: 'mock-jwt-token',
                 expiresAt: '2024-12-31'
               }
             }
           }
         }
       }
     }
   };
```


### Example 6

```javascript
const mockLoadingState = {
     parameters: {
       sveltekit_experimental: {
         stores: {
           navigating: {
             from: { url: new URL('https://example.com') },
             to: { url: new URL('https://example.com/products') }
           }
         }
       }
     }
   };
```


---

## 🚀 Usage

**Reference this template:** `@command-sveltestorybook-mock.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
