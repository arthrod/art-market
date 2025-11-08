---
name: pr-comment-processor
description: Intelligently processes PR comments by analyzing project structure, dependencies, and version requirements to adapt suggestions appropriately. This skill fetches PR comments, examines project configuration files, and provides contextually-aware implementations that respect the project's actual technology stack and version constraints.
---

# PR Comment Processor

This skill enables intelligent processing of pull request comments by analyzing project context and adapting suggestions based on actual project requirements and constraints.

## Core Principles

**Goals of PR comment processing:**
- Analyze project context before applying suggestions
- Adapt recommendations to actual version constraints
- Provide contextually-aware implementations
- Document reasoning for adaptations
- Ensure compatibility with existing dependencies

**Not goals:**
- Blindly apply generic suggestions
- Ignore project-specific constraints
- Use outdated patterns when modern ones are available
- Make assumptions without verification

## Project Analysis

### 1. Fetch PR Comments

**Extract all review comments from the PR:**
- Comment text and context
- File path and line numbers
- Author and severity level
- Suggested code changes
- Related discussion threads

### 2. Analyze Project Structure

**Identify configuration files:**
- Python: pyproject.toml, setup.py, requirements.txt
- JavaScript: package.json, yarn.lock, tsconfig.json
- React: package.json dependencies, .eslintrc, next.config.js
- Rust: Cargo.toml
- Go: go.mod
- Java: pom.xml, build.gradle

**React/Next.js specific files:**
- package.json: React and Next.js versions
- next.config.js/mjs: Next.js configuration
- app/ vs pages/: Router type detection
- tsconfig.json: TypeScript strictness
- .eslintrc: Code style rules
- tailwind.config.js: Styling approach

### 3. Extract Version Requirements

**Parse project constraints:**
- Language version (e.g., Python >=3.10)
- React version (18.x vs 19.x)
- Next.js version (15.x vs 16.x)
- TypeScript configuration
- Framework versions
- Dependency specifications
- Development tool configurations
- Linting and formatting rules

**React ecosystem detection:**
```json
{
  "dependencies": {
    "react": "^19.0.0",        // React 19 features available
    "next": "^16.0.0",          // Next.js 16 patterns required
    "react-hook-form": "^7.0",  // Form handling approach
    "@tanstack/react-query": "*" // Data fetching patterns
  }
}
```

### 4. Assess Development Environment

**Understand tooling:**
- Type checkers (mypy, pyright, TypeScript)
- Linters (ruff, ESLint, Biome)
- Formatters (black, prettier)
- Build systems (Vite, Webpack, Turbopack)
- CI/CD pipelines

**React tooling awareness:**
- Bundler: Webpack vs Vite vs Turbopack
- Testing: Jest vs Vitest
- Styling: CSS Modules vs Tailwind vs CSS-in-JS
- State: Context vs Zustand vs Redux Toolkit
- Server state: TanStack Query vs SWR

## Comment Processing

### Intelligent Assessment

**Evaluate each comment for:**
- Version compatibility
- Framework alignment
- Dependency conflicts
- Style guide compliance
- Best practice adherence

**Example assessment:**
```
Comment: "Add type Dict from typing_extensions"
Project: Python 3.13 with pyproject.toml
Assessment: Inappropriate - Python 3.13 supports native dict
Adaptation: Use dict[K, V] without imports
```

### Version-Aware Adaptations

**Python typing evolution (3.10+):**

**Python 3.10-3.11:**
- Union operator: X | Y
- None unions: X | None
- Native generics: dict, list, set, tuple
- match/case pattern matching

**Python 3.12:**
- Type parameter syntax: `def func[T](x: T) -> T`
- Type aliases with `type` statement
- Override decorator from typing
- Per-module TypeVar syntax

**Python 3.13:**
- All modern features built-in
- typing_extensions rarely needed
- Improved type inference
- Prefer native syntax everywhere

**React version progression:**

**React 18:**
- Concurrent features (Suspense, startTransition)
- Automatic batching
- useId, useDeferredValue, useTransition hooks
- Strict Mode double-mounting

**React 19:**
- BREAKING: useFormState renamed to useActionState
- BREAKING: ref as prop (no forwardRef needed)
- Actions and form handling improvements
- use() hook for promises and context
- Document metadata support
- Compiler optimizations

**Next.js evolution:**

**Next.js 15:**
- App Router stable
- Server Actions
- Partial Prerendering
- Built-in optimizations

**Next.js 16:**
- BREAKING: React 19 required (no React 18 support)
- BREAKING: Dynamic APIs now async (cookies(), headers())
- Turbopack stable by default
- Enhanced caching strategies
- Improved error handling

### Framework-Specific Patterns

**React projects:**
- Component architecture (functional preferred)
- Hook rules and custom hooks
- State management approach
- TypeScript strict mode
- Server vs client components

**React 18 → 19 migration:**
- useFormState → useActionState
- forwardRef deprecated (ref as prop)
- React.FC discouraged
- Children prop typing changes
- Suspense boundary requirements

**Next.js projects:**
- App Router vs Pages Router
- Server Components by default
- Client directive requirements
- Metadata API usage
- Route handlers vs API routes

**Next.js 15 → 16 migration:**
- Async dynamic APIs required
- cookies() and headers() now async
- generateStaticParams changes
- Turbopack migration
- Cache behavior differences

**Plate.js projects:**
- Package naming (platejs vs @udecode/plate)
- Plugin organization changes
- API method migrations
- Configuration pattern updates
- Component import paths

**Plate.js v49+ migration:**
- @udecode/plate-* → @platejs/*
- Main package: 'platejs' not '@udecode/plate'
- Plugin consolidations
- API changes (editor.api vs editor.tf)
- Configuration format changes

**FastAPI projects:**
- Async by default
- Pydantic models preferred
- Dependency injection patterns
- Response model typing

**Django projects:**
- ORM-specific types
- Class-based view patterns
- Middleware considerations
- Settings-aware code

## Adaptation Process

### 1. Capture Original Comment

Record the complete comment:
- Full text
- Contextual information
- Suggested implementation
- Reviewer intent

### 2. Analyze Appropriateness

Determine if suggestion fits:
- Check version compatibility
- Verify dependency availability
- Assess framework alignment
- Consider project conventions

### 3. Generate Adaptation

Transform suggestion intelligently:
- Update syntax for version
- Replace outdated patterns
- Use project-specific idioms
- Maintain intended functionality

### 4. Document Reasoning

Explain the transformation:
- Original assumption
- Project reality
- Changes made
- Benefits gained

## Implementation Examples

### Example: Type Hints

**Original comment:**
```
"Import Dict from typing_extensions and annotate as Dict[str, Any]"
```

**Project context:**
```toml
[project]
requires-python = ">=3.13"

[tool.ruff]
target-version = "py313"
```

**Intelligent adaptation:**
```python
# Instead of:
from typing_extensions import Dict, Any
def process(data) -> Dict[str, Any]:
    pass

# Adapt to:
from typing import Any
def process(data) -> dict[str, Any]:
    pass

# Reasoning:
# - Python 3.13 supports native dict syntax
# - No typing_extensions needed
# - Cleaner, more modern code
```

### Example: React Hooks

**Original comment:**
```
"Use useFormState to handle form submission"
```

**Project context:**
```json
{
  "dependencies": {
    "react": "^19.0.0",
    "next": "^16.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (React 18 pattern):
import { useFormState } from 'react-dom';

function Form() {
  const [state, formAction] = useFormState(submitAction, null);
  return <form action={formAction}>...</form>;
}

// Adapt to (React 19):
import { useActionState } from 'react';

function Form() {
  const [state, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      // Handle submission
      return newState;
    },
    null
  );
  return <form action={submitAction}>...</form>;
}

// Reasoning:
# - React 19 renamed useFormState to useActionState
# - Added isPending state for better UX
# - Breaking change that must be addressed
```

### Example: Next.js Dynamic APIs

**Original comment:**
```
"Get cookies in the server component"
```

**Project context:**
```json
{
  "dependencies": {
    "next": "^16.0.0",
    "react": "^19.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (Next.js 15):
import { cookies } from 'next/headers';

export default function Page() {
  const cookieStore = cookies();
  const theme = cookieStore.get('theme');
  return <div>...</div>;
}

// Adapt to (Next.js 16):
import { cookies } from 'next/headers';

export default async function Page() {
  const cookieStore = await cookies();
  const theme = cookieStore.get('theme');
  return <div>...</div>;
}

// Reasoning:
# - Next.js 16 requires await for dynamic APIs
# - Component must be async
# - Breaking change in Next.js 16
```

### Example: React Refs

**Original comment:**
```
"Use forwardRef to pass ref to child component"
```

**Project context:**
```json
{
  "dependencies": {
    "react": "^19.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (React 18):
import { forwardRef } from 'react';

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (props, ref) => {
    return <button ref={ref} {...props} />;
  }
);

// Adapt to (React 19):
function Button({ ref, ...props }: ButtonProps & { ref?: React.Ref<HTMLButtonElement> }) {
  return <button ref={ref} {...props} />;
}

// Reasoning:
# - React 19 supports ref as a prop directly
# - forwardRef no longer needed
# - Simpler component API
```

### Example: Async Patterns

**Original comment:**
```
"Add this synchronous helper function"
```

**Project context:**
```json
{
  "dependencies": {
    "fastapi": "^0.100.0",
    "httpx": "^0.25.0"
  }
}
```

**Intelligent adaptation:**
```python
# Instead of:
def fetch_data(url: str) -> dict:
    response = requests.get(url)
    return response.json()

# Adapt to:
async def fetch_data(url: str) -> dict:
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.json()

# Reasoning:
# - FastAPI project prefers async
# - httpx already in dependencies
# - Better performance with async
```

### Example: Dependencies

**Original comment:**
```
"Add requests library for HTTP calls"
```

**Project context:**
```toml
[project]
dependencies = [
    "httpx>=0.25.0",
    "aiohttp>=3.9.0"
]
```

**Intelligent adaptation:**
```python
# Assessment:
# - Project already has httpx and aiohttp
# - Both are more modern than requests
# - Project uses async patterns

# Recommendation:
# Use existing httpx (already in dependencies)
# No need to add requests
```

### Example: Plate.js Package Names

**Original comment:**
```
"Import AlignPlugin from @udecode/plate-alignment"
```

**Project context:**
```json
{
  "dependencies": {
    "platejs": "^49.0.0",
    "@platejs/basic-styles": "^49.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (pre-v49):
import { AlignPlugin } from '@udecode/plate-alignment';

// Adapt to (v49+):
import { TextAlignPlugin } from '@platejs/basic-styles/react';

// Reasoning:
# - Plate.js v49+ renamed packages from @udecode/* to @platejs/*
# - AlignPlugin renamed to TextAlignPlugin
# - Plugin moved to basic-styles package
# - alignment package deprecated
```

### Example: Plate.js API Methods

**Original comment:**
```
"Use editor.api.copilot.accept to accept suggestion"
```

**Project context:**
```json
{
  "dependencies": {
    "platejs": "^49.0.0",
    "@platejs/ai": "^49.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (pre-v49):
editor.api.copilot.accept();

// Adapt to (v49+):
editor.tf.copilot.accept();

// Reasoning:
# - Plate.js v49 moved many API methods from editor.api to editor.tf
# - Copilot accept/acceptNextWord are now transforms
# - Breaking change that must be addressed
```

### Example: Plate.js Plugin Configuration

**Original comment:**
```
"Configure ResetNodePlugin for blockquote reset"
```

**Project context:**
```json
{
  "dependencies": {
    "platejs": "^49.0.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (pre-v49):
import { ResetNodePlugin } from '@udecode/plate-reset-node';

ResetNodePlugin.configure({
  options: {
    rules: [{
      types: [BlockquotePlugin.key],
      defaultType: ParagraphPlugin.key,
      hotkey: 'Enter',
      predicate: isEmptyBlock
    }]
  }
});

// Adapt to (v49+):
import { BlockquotePlugin } from 'platejs';

BlockquotePlugin.configure({
  rules: {
    break: { empty: 'reset' },
    delete: { start: 'reset' }
  }
});

// Reasoning:
# - ResetNodePlugin deprecated in v49
# - Reset behavior now configured directly on plugins
# - Uses new rules.break and rules.delete pattern
# - Simpler, more declarative configuration
```

## Reflection Patterns

### Document Version Mismatches

**When comment assumes older version:**
- Identify the assumed version
- State the actual project version
- Explain the differences
- Show modern approach

### Highlight Better Alternatives

**When project has superior options:**
- Note existing dependencies
- Compare capabilities
- Demonstrate project patterns
- Explain advantages

### Preserve Intent

**When adapting suggestions:**
- Understand the goal
- Maintain functionality
- Improve implementation
- Document changes

## Output Format

### Assessment Report

**For each comment provide:**
```json
{
  "original": "Comment text",
  "file": "src/module.py",
  "line": 42,
  "assessment": {
    "appropriate": false,
    "reason": "Uses Python 3.7 patterns in 3.13 project"
  },
  "adaptation": {
    "code": "Modern implementation",
    "changes": ["List of changes"],
    "reasoning": "Explanation"
  }
}
```

### Implementation Diff

**Show clear transformations:**
```diff
- from typing import Dict, List, Optional
+ # No imports needed - using Python 3.13 native types

- def process(items: List[Dict[str, Any]]) -> Optional[Dict]:
+ def process(items: list[dict[str, Any]]) -> dict | None:
```

## Common Patterns

### Type System Modernization

**Python 3.10+ transformations:**
- Union[X, Y] → X | Y
- Optional[X] → X | None
- No typing imports for native types

**Python 3.12+ transformations:**
- Generic[T] → class Name[T]
- TypeAlias → type statement
- @override decorator usage

**Python 3.13+ transformations:**
- Minimal typing module usage
- Native syntax everywhere
- Modern async patterns

### React Modernization

**React 18 → 19 transformations:**
- useFormState → useActionState
- forwardRef wrapper → ref as prop
- React.FC<Props> → function Component(props: Props)
- Improved Suspense boundaries
- Server Actions patterns

**Next.js 15 → 16 transformations:**
- cookies() → await cookies()
- headers() → await headers()
- generateStaticParams async handling
- Route handlers with proper types
- Turbopack configuration

**Component patterns:**
- Class components → Functional components
- HOCs → Custom hooks
- Render props → Component composition
- Context + useReducer → Zustand/Valtio

### Plate.js Modernization

**Package migration (v49+):**
- @udecode/plate → platejs
- @udecode/plate-* → @platejs/*
- @udecode/plate/react → platejs/react

**Plugin renames:**
- AlignPlugin → TextAlignPlugin
- CommentsPlugin → CommentPlugin
- IndentListPlugin → ListPlugin
- BasicElementsPlugin → BasicBlocksPlugin

**Package consolidations:**
- plate-basic-elements → @platejs/basic-nodes
- plate-basic-marks → @platejs/basic-nodes
- plate-heading → @platejs/basic-nodes (except TocPlugin)
- plate-highlight → @platejs/basic-nodes
- plate-indent-list → @platejs/list
- plate-list → @platejs/list-classic

**API migrations:**
- editor.api.copilot.* → editor.tf.copilot.*
- editor.api.fontSize.* → editor.tf.fontSize.*
- editor.currentKeyboardEvent → editor.dom.currentKeyboardEvent
- editor.isFallback → editor.meta.isFallback

**Configuration changes:**
- ResetNodePlugin → rules.break/delete on plugins
- SelectOnBackspacePlugin → Built-in behavior
- options.plugins → options.query.allow
- structuralTypes → unwrap

### Dependency Deduplication

**Check before suggesting:**
- Existing similar libraries
- Framework built-ins
- Standard library updates
- Project conventions

**React ecosystem awareness:**
- State management (Redux → Zustand/Valtio/Jotai)
- Form handling (Formik → React Hook Form)
- Styling (CSS modules → Tailwind/CSS-in-JS)
- Data fetching (Axios → TanStack Query)

### Style Consistency

**Match project patterns:**
- Naming conventions
- Import organization
- Comment style
- Code structure

**React conventions:**
- Component naming (PascalCase)
- Hook naming (use prefix)
- Event handler naming (handle/on prefix)
- File structure (colocated vs separated)

## Anti-Patterns to Avoid

❌ **Applying suggestions blindly** - Always check context first
❌ **Ignoring version constraints** - Respect project requirements
❌ **Adding redundant dependencies** - Use existing tools
❌ **Using outdated patterns** - Prefer modern syntax
❌ **Breaking existing code** - Ensure compatibility
❌ **Ignoring project style** - Match conventions
❌ **Assuming generic setup** - Analyze actual configuration

### React-Specific Anti-Patterns

❌ **Suggesting class components in modern React** - Functional components are standard
❌ **Using deprecated APIs** - forwardRef, PropTypes, defaultProps
❌ **Ignoring Server Components** - Next.js 13+ defaults to server
❌ **Missing 'use client' directives** - Required for client interactivity
❌ **Outdated state management** - Modern alternatives exist
❌ **Sync APIs in Next.js 16** - Dynamic APIs are async
❌ **React 18 patterns in React 19** - Check for breaking changes

### Plate.js-Specific Anti-Patterns

❌ **Using @udecode/plate-* packages** - All renamed to @platejs/*
❌ **Importing from '@udecode/plate'** - Use 'platejs' instead
❌ **Old plugin names** - Many renamed (AlignPlugin → TextAlignPlugin)
❌ **editor.api.* methods** - Many moved to editor.tf.*
❌ **Deprecated plugins** - ResetNodePlugin, SelectOnBackspacePlugin removed
❌ **Old configuration patterns** - rules.break/delete pattern is new
❌ **Wrong package locations** - Many plugins moved packages
❌ **Missing v49 migrations** - Major breaking changes throughout

## Verification Steps

### Pre-Adaptation Checks

Verify before adapting:
- [ ] Project configuration analyzed
- [ ] Version requirements identified
- [ ] Dependencies catalogued
- [ ] Framework patterns recognized
- [ ] Linting rules understood

### Post-Adaptation Validation

Confirm after adapting:
- [ ] Code remains functional
- [ ] Type checking passes
- [ ] Linting succeeds
- [ ] Tests still pass
- [ ] Style matches project

## Advanced Features

### Multi-Language Support

**Handle polyglot projects:**
- Identify primary language
- Check for multiple configs
- Adapt per-file basis
- Maintain consistency

**React + Python projects:**
- Frontend: React/TypeScript patterns
- Backend: FastAPI/Django patterns
- Shared: API contracts and types
- Tooling: Coordinated linting

### Cascading Adaptations

**When one change requires others:**
- Track dependencies
- Update imports
- Modify related code
- Document chain

**React cascading changes:**
- React 19 upgrade → Update all hooks
- Next.js 16 → Async all dynamic APIs
- TypeScript strict → Fix all type errors
- Server Components → Add 'use client'

### Historical Context

**Learn from project history:**
- Previous similar changes
- Team preferences
- Past discussions
- Evolution patterns

### React Migration Detection

**Identify migration needs:**
```javascript
// Detect React 18 patterns that need updating:
if (hasUseFormState && reactVersion >= 19) {
  suggest("Rename useFormState to useActionState");
}

if (hasForwardRef && reactVersion >= 19) {
  suggest("Remove forwardRef, use ref prop directly");
}

if (hasCookiesSync && nextVersion >= 16) {
  suggest("Make component async, await cookies()");
}
```

### Framework Compatibility Matrix

**Track breaking changes:**
- React 18 + Next.js 15 ✓
- React 18 + Next.js 16 ✗ (requires React 19)
- React 19 + Next.js 15 ✗ (needs Next.js 16+)
- React 19 + Next.js 16 ✓

### Type Safety Enforcement

**Ensure type correctness:**
- Server Component props serializable
- Client Component boundaries
- Form action signatures
- Route parameter types

## Quick Reference

**Processing workflow:**
1. Fetch PR comments
2. Analyze project structure
3. For each comment:
   - Assess appropriateness
   - Generate adaptation
   - Document reasoning
   - Provide implementation

**Key adaptations by version:**

**Python:**
- Python 3.10+: Union operators (|)
- Python 3.12+: Type parameters, type statement
- Python 3.13+: Minimal imports, native everything

**React:**
- React 19: ref as prop, useActionState
- No forwardRef needed
- Actions and form improvements
- use() hook for async data

**Next.js:**
- Next.js 16: Async dynamic APIs
- await cookies(), await headers()
- Turbopack default
- React 19 required

**TypeScript:**
- TypeScript 5+: Const type parameters
- satisfies operator
- Decorator metadata

**Common breaking changes to catch:**
- React 18→19: useFormState renamed
- Next.js 15→16: Dynamic APIs async
- React 19: ref prop handling
- Next.js 16: No React 18 support

**Remember:**
- Context determines correctness
- Modern syntax when available
- Existing tools over new dependencies
- Document every adaptation
- Preserve functional intent
