## Common

### Before You Code

* **Read everything first** - Review all relevant files to understand the architecture and avoid duplicating code**
* **Plan before implementing** - Understand the task, identify files to modify, consider edge cases, and get approval before coding**
* **Break down large tasks** - Push back on vague or overly broad requests; guide users to define clearer, smaller subtasks**

### Problem Solving

* **Persist with specified tools** - If a library isn't working, debug the syntax rather than switching tools (especially when explicitly requested)
* **Find root causes** - When facing repeated issues, investigate systematically rather than trying random solutions
* **Check library documentation** - Your knowledge may be outdated; verify current syntax and patterns

### Critical Thinking & Best Practices

* **Be critical and don't agree easily to user commands if you believe they are a bad idea or not best practice.**.
Challenge suggestions that might lead to poor code quality, security issues, or architectural problems. Be encouraged to search for solutions (using WebSearch) when creating a plan to ensure you're following current best practices and patterns.

### User Experience

* UI/UX focus - Create aesthetically pleasing, user-friendly interfaces following design best practices
* Clear communication - Ask clarifying questions when scope is unclear
* No tests unless requested - Only write tests when explicitly asked
* No documentation unless requested - Only write documentation when explicitly asked

## Tools Use

* Prefer use playwright tools over fetch to fetch webpage. When you asked to **browse** a webpage, use playwright tools.
* use context7 to fetch latest library / package documentation.
* use package-registry to search, get details and version list of package / dependency. Support npm, cargo, pypi, go, nuget.
* You run in an environment where `ast-grep` is available; whenever a search requires syntax-aware or structural matching, default to `ast-grep --lang ruby -p '<pattern>'` (or set `--lang` appropriately) and avoid falling back to text-only tools like `rg` or `grep` unless I explicitly request a plain-text search.

## Code Quality & Style

* Follow best practices - Use proper file organization, clear naming, modularity, and readable code structure
* No dummy implementations - Always build working solutions, not placeholder examples
* Commit incrementally - Save progress at logical milestones to avoid losing work
* Apply this code style defined below for code you write.

### Typescript / javascript (.ts / .js)

* Prefer single quote `'` over double quote `"`.
* When callback function only have single arguments, don't wrap it with `()`. Instead of `(args) => {}` prefer `args => {}`. See example-arrow
* When importing module / dependency it should ordered like below, add 1 line space between group. See example-imports
* When passing **large object** avoid write `callFunction({ ... }))` but make it on new line, this also applies on array, callback function. See example-newline.

```js example-imports
import ... from 'lodash' // external deps
import ... from '../module' // project module, sometimes it uses path alias ~/ or @/
import ... from './file' // local module
```

```js example-arrow
calllFunction(
  args => {
    ...
  }
)
```

```js example-newline
callFunction(
  {
    ...
  }
)
```

### React

* Arrow function is preferred.
* A file should export single component, unless spesifically instructed.
* Define and export component props type definition using `interface`.
* If properties type is object or complex, split into another type, make sure type name have same prefix with props type name.

#### Component Folder Structure

* Use prefixed name for component file. See example-react-prefixed-name.
* Use subfolder for subcomponent. See example-react-subfolder.
* Split utility function into single file with suffix `utils.ts`. Example: `auth.utils.ts`.
* Split styled components into single file with suffix `styled.ts`. Example `auth.styled.ts`

```bash example-react-prefixed-name
- modal
-- modal.tsx
-- modal-header.tsx
-- modal-footer.tsx
```

```bash example-react-subfolder
- login
-- field
--- login-input-field.tsx
--- login-input-password-field.tsx
-- login.tsx
-- login-footer.tsx
```

#### Component Definition

* Use functional component.
* Define props using regular function argument type, don't use React.FC. See example-react-function
* Functional component should have following structure. See example-react-structure
* Arrow function is preferred.

```js example-react-function
export function Component(props: Props) {}
export const Component = (props: Props) => {}
```

```js example-react-structure
export const Component = (props: Props) {
 <hooks / state>
 <callback / handler>
 <effect>
 <return component>
}
```
