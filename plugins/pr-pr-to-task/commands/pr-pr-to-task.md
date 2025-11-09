---
name: pr-comment-processor
description: Intelligently processes PR comments by analyzing project structure, dependencies, and version requirements to adapt suggestions appropriately. Uses pr_to_task CLI for structured task management and progress tracking. This skill fetches PR comments, examines project configuration files, organizes them into manageable tasks, and provides contextually-aware implementations that respect the project's actual technology stack and version constraints.
---

# PR Comment Processor

This skill enables intelligent processing of pull request comments by analyzing project context, organizing work into tasks, and adapting suggestions based on actual project requirements and constraints.

## Core Principles

**Goals of PR comment processing:**
- Organize PR comments into structured tasks
- Track progress through task completion
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
- Process comments without structure or tracking

## Task Management Workflow

### CRITICAL: Always Start with pr_to_task init

**Before processing any PR comments, you MUST run:**

```bash
pr_to_task init
```

This is **mandatory** and must be the **first action** before any comment processing. Do not skip this step.

### pr_to_task CLI Tool

The `pr_to_task` CLI provides structured task management for processing PR comments:

**Available commands:**
```bash
pr_to_task init            # Initialize task processing, generate AGENTS.md, show first task
pr_to_task next            # Show next uncompleted task
pr_to_task mark-complete   # Mark current task complete, show next task
pr_to_task status          # Show current status of all tasks
pr_to_task reset           # Reset all task states (viewed and completed flags)
```

### Workflow Integration

**1. Initialize Task Processing (REQUIRED FIRST STEP)**

**You must always start by initializing the task management system:**

```bash
pr_to_task init
```

**What this does:**
- Fetches all PR review comments
- Analyzes project structure and dependencies
- Generates AGENTS.md with organized tasks
- Groups related comments logically
- Prioritizes tasks by file/module
- Displays the first task to work on

**AGENTS.md structure:**
```markdown
# PR Comment Tasks

## Project Context
- Language: Python 3.13
- Framework: FastAPI
- Dependencies: httpx, pydantic, loguru
- Linting: ruff, mypy

## Tasks

### Task 1: Update Type Hints in src/core.py
**Status:** Not Started
**Files:** src/core.py
**Lines:** 42-45
**Priority:** High
**Original Comments:**
- Comment by @reviewer: "Use Dict from typing"
**Assessment:** Inappropriate for Python 3.13
**Adaptation Required:** Use native dict[K, V] syntax

### Task 2: Modernize Async Pattern in src/api.py
...
```

**2. Work Through Tasks**

Process tasks one at a time:

```bash
# Show current/next task
pr_to_task next
```

**Output example:**
```
=== Task 3: Fix React Hook Usage in components/Form.tsx ===
Status: In Progress
Files: components/Form.tsx
Lines: 15-20

Original Comment:
"Use useFormState for form handling"

Project Context:
- React: 19.0.0
- Next.js: 16.0.0

Assessment:
❌ Inappropriate - React 19 renamed useFormState to useActionState

Required Adaptation:
- Replace useFormState with useActionState
- Add isPending state for loading UI
- Update form action signature

Implementation:
[Shows adapted code example]
```

**3. Complete Tasks**

After implementing a task:

```bash
pr_to_task mark-complete
```

**What this does:**
- Marks current task as completed
- Updates AGENTS.md
- Automatically shows next uncompleted task
- Tracks overall progress

**4. Check Progress**

View overall status anytime:

```bash
pr_to_task status
```

**Output example:**
```
=== PR Comment Processing Status ===

Total Tasks: 12
Completed: 5 (42%)
In Progress: 1
Not Started: 6

Recent Activity:
✓ Task 1: Update Type Hints (Completed)
✓ Task 2: Modernize Async Pattern (Completed)
✓ Task 3: Fix React Hook Usage (Completed)
✓ Task 4: Update Next.js Dynamic APIs (Completed)
✓ Task 5: Migrate Plate.js Imports (Completed)
→ Task 6: Refactor Form Validation (In Progress)
  Task 7: Add Error Boundaries (Not Started)
  ...
```

**5. Reset if Needed**

Start over or reset states:

```bash
pr_to_task reset
```

Use this when:
- Need to reprocess all tasks
- Made mistakes in tracking
- Want to review completed tasks
- Project requirements changed significantly

## Project Analysis

### 1. Fetch PR Comments

**Extract all review comments from the PR:**
- Comment text and context
- File path and line numbers
- Author and severity level
- Suggested code changes
- Related discussion threads

**Organize into task groups:**
- By file/module
- By comment type (bug, suggestion, style)
- By priority (breaking changes first)
- By dependency (sequential tasks)

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
- Task priority and dependencies

**Example assessment:**
```
Comment: "Add type Dict from typing_extensions"
Project: Python 3.13 with pyproject.toml
Assessment: Inappropriate - Python 3.13 supports native dict
Adaptation: Use dict[K, V] without imports
Priority: Medium (non-breaking improvement)
```

### Task Organization

**Group related comments:**
```markdown
### Task Group: Type System Modernization
- Task 1.1: Update type hints in core module
- Task 1.2: Remove typing_extensions imports
- Task 1.3: Update tests with new type syntax

### Task Group: React 19 Migration
- Task 2.1: Rename useFormState to useActionState
- Task 2.2: Remove forwardRef wrappers
- Task 2.3: Update component prop types
```

**Priority levels:**
1. **Critical**: Breaking changes, security issues
2. **High**: Performance issues, major bugs
3. **Medium**: Code quality, maintainability
4. **Low**: Style, minor improvements

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
- BREAKING: middleware.ts renamed to proxy.ts
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

**Next.js 16 proxy.ts migration:**
- middleware.ts → proxy.ts (file renamed)
- export function middleware() → export function proxy()
- Runs on Node.js runtime by default
- middleware.ts deprecated (still works for Edge runtime)
- Clarifies network boundary purpose
- Use for: redirects, rewrites, header modification
- Avoid for: heavy business logic, auth checks (move to data layer)

**Plate.js projects:**
- Package naming (platejs vs @udecode/plate)
- Plugin organization changes
- API method migrations
- Configuration pattern updates
- Component import paths

**Plate.js import patterns:**
- **With Next.js** (or `"moduleResolution": "bundler"`):
  - Can use subpath imports like `platejs/react` directly
  - TypeScript 5.0+ required for bundler resolution
- **Without Next.js** (or with `"moduleResolution": "node"`):
  - Must configure path aliases for /react subpaths
  - Example: `'platejs/react': '<rootDir>/node_modules/platejs/dist/react'`
- **Server Components** (Next.js App Router RSC):
  - Import base plugins WITHOUT /react: `import { BaseHeadingPlugin } from '@platejs/basic-nodes'`
  - Use PlateStatic component for server-side rendering
- **Client Components** (marked with 'use client'):
  - Import plugins WITH /react: `import { HeadingPlugin } from '@platejs/basic-nodes/react'`
  - Use Plate, usePlateEditor from `platejs/react`

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

### 5. Track Task Progress

Update task status:
- Mark as viewed
- Note in-progress work
- Record completion
- Link related changes

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

**Task entry:**
```markdown
### Task: Modernize Type Hints
Status: In Progress
Priority: Medium
Files: src/models.py

Original: Use typing_extensions.Dict
Adaptation: Use native dict syntax
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

**After completion:**
```bash
pr_to_task mark-complete
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

**Task workflow:**
```bash
# View task
pr_to_task next

# Output shows:
# Task 5: Update Form Hooks
# Status: Not Started
# Files: components/ContactForm.tsx
# [Shows assessment and adaptation needed]

# After implementing...
pr_to_task mark-complete
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

**Task tracking:**
```markdown
### Task 8: Update Dynamic API Calls
Status: Completed
Priority: Critical (Breaking Change)
Files: app/dashboard/page.tsx, app/profile/page.tsx
Estimated: 15 minutes
Actual: 12 minutes
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

### Example: Next.js 16 Proxy Migration

**Original comment:**
```
"Update middleware for authentication"
```

**Project context:**
```json
{
  "dependencies": {
    "next": "^16.0.0",
    "react": "^19.2.0"
  }
}
```

**Intelligent adaptation:**
```tsx
// Instead of (Next.js 15):
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  // Auth logic (deprecated pattern)
  return NextResponse.redirect(new URL('/login', request.url));
}

// Adapt to (Next.js 16):
// proxy.ts
import { NextRequest, NextResponse } from 'next/server';

export function proxy(request: NextRequest) {
  // Simple redirect/rewrite only
  // Move auth to data layer
  return NextResponse.redirect(new URL('/login', request.url));
}

// Reasoning:
# - Next.js 16 renamed middleware.ts to proxy.ts
# - Function renamed from middleware to proxy
# - Auth logic should move to server actions or API routes
# - proxy.ts for lightweight routing only
```

### Example: Plate.js Import Patterns

**Original comment:**
```
"Import HeadingPlugin for the editor"
```

**Project context:**
```json
{
  "dependencies": {
    "platejs": "^49.0.0",
    "@platejs/basic-nodes": "^49.0.0",
    "next": "^16.0.0"
  }
}
```

**Intelligent adaptation:**

**For Client Component:**
```tsx
// app/components/Editor.tsx
'use client';

import { HeadingPlugin } from '@platejs/basic-nodes/react';
import { Plate, usePlateEditor } from 'platejs/react';

export function Editor() {
  const editor = usePlateEditor({
    plugins: [HeadingPlugin]
  });

  return <Plate editor={editor}>...</Plate>;
}

// Reasoning:
# - Next.js App Router defaults to Server Components
# - Client Component needs 'use client' directive
# - Use /react imports for client components
# - platejs/react works with "bundler" moduleResolution
```

**For Server Component:**
```tsx
// app/preview/page.tsx
import { BaseHeadingPlugin } from '@platejs/basic-nodes';
import { PlateStatic, createSlateEditor } from 'platejs';

export default async function PreviewPage() {
  const editor = createSlateEditor({
    plugins: [BaseHeadingPlugin]  // Base plugin, no /react
  });

  return <PlateStatic editor={editor} value={content} />;
}

// Reasoning:
# - Server Component can be async
# - Use base imports WITHOUT /react for SSR
# - PlateStatic for non-interactive rendering
# - No 'use client' directive needed
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
- Add to task notes

### Highlight Better Alternatives

**When project has superior options:**
- Note existing dependencies
- Compare capabilities
- Demonstrate project patterns
- Explain advantages
- Suggest in task description

### Preserve Intent

**When adapting suggestions:**
- Understand the goal
- Maintain functionality
- Improve implementation
- Document changes
- Track in task system

## Output Format

### Assessment Report

**For each comment provide:**
```json
{
  "task_id": "T-005",
  "original": "Comment text",
  "file": "src/module.py",
  "line": 42,
  "status": "in_progress",
  "priority": "medium",
  "assessment": {
    "appropriate": false,
    "reason": "Uses Python 3.7 patterns in 3.13 project"
  },
  "adaptation": {
    "code": "Modern implementation",
    "changes": ["List of changes"],
    "reasoning": "Explanation"
  },
  "completed_at": null
}
```

### AGENTS.md Format

**Generated by pr_to_task init:**
```markdown
# PR Comment Processing

## Repository Context
**Repository:** myorg/myproject
**Primary Language:** Python 3.13
**Type System:** mypy (strict mode)
**Linting:** ruff, pylint
**Formatting:** black

## Framework Stack
**Backend:** FastAPI 0.115.0
**Async Runtime:** asyncio with uvloop
**Database:** PostgreSQL 16 with SQLAlchemy 2.0 (async)
**Caching:** Redis 7

## Frontend Stack (if applicable)
**Framework:** Next.js 16.0.0
**React Version:** 19.2.0
**TypeScript:** 5.6.3
**Module Resolution:** bundler
**Styling:** Tailwind CSS 4.0
**State Management:** Zustand
**Forms:** React Hook Form + Zod

## Dependencies of Note
**HTTP Client:** httpx (async)
**Validation:** Pydantic 2.10
**Logging:** loguru
**Testing:** pytest + pytest-asyncio
**Rich Text Editor:** Plate.js 49.0.0

## Build Tools
**Python Packaging:** uv
**Node Package Manager:** pnpm
**Bundler:** Turbopack (Next.js 16 default)
**Type Checking:** mypy + TypeScript

## Project Conventions
**Import Style:** Absolute imports preferred
**Async Pattern:** Async by default for I/O operations
**Error Handling:** Result types preferred over exceptions
**Type Hints:** Full coverage required (mypy strict)
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
- middleware.ts → proxy.ts
- export function middleware → export function proxy
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

**Import patterns by context:**

**Next.js with bundler resolution (default):**
```tsx
// Client Component
'use client';
import { HeadingPlugin } from '@platejs/basic-nodes/react';
import { Plate, usePlateEditor } from 'platejs/react';

// Server Component
import { BaseHeadingPlugin } from '@platejs/basic-nodes';
import { PlateStatic, createSlateEditor } from 'platejs';
```

**Non-Next.js or node resolution:**
```tsx
// Requires path aliases in tsconfig.json:
{
  "compilerOptions": {
    "paths": {
      "platejs/react": ["./node_modules/platejs/dist/react"],
      "@platejs/*/react": ["./node_modules/@platejs/*/dist/react"]
    }
  }
}

// Or use bundler resolution (TypeScript 5.0+):
{
  "compilerOptions": {
    "moduleResolution": "bundler"
  }
}
```

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
❌ **Skipping task tracking** - Always use pr_to_task workflow
❌ **Working without init** - Initialize before processing
❌ **Not marking completion** - Track progress properly

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
- [ ] pr_to_task init has been run
- [ ] AGENTS.md has been generated
- [ ] Project configuration analyzed
- [ ] Version requirements identified
- [ ] Dependencies catalogued
- [ ] Framework patterns recognized
- [ ] Linting rules understood
- [ ] Tasks properly prioritized

### During Processing

For each task:
- [ ] Run pr_to_task next to view task
- [ ] Review assessment and adaptation
- [ ] Implement changes
- [ ] Test implementation
- [ ] Run pr_to_task mark-complete
- [ ] Verify next task appears

### Post-Adaptation Validation

Confirm after adapting:
- [ ] Code remains functional
- [ ] Type checking passes
- [ ] Linting succeeds
- [ ] Tests still pass
- [ ] Style matches project
- [ ] Task marked complete
- [ ] Progress tracked in AGENTS.md

### Final Verification

Before completing:
- [ ] Run pr_to_task status
- [ ] All tasks completed
- [ ] No tasks in error state
- [ ] All changes committed
- [ ] PR ready for final review

## Advanced Features

### Multi-Language Support

**Handle polyglot projects:**
- Identify primary language
- Check for multiple configs
- Adapt per-file basis
- Maintain consistency
- Track tasks per language

**React + Python projects:**
- Frontend: React/TypeScript patterns
- Backend: FastAPI/Django patterns
- Shared: API contracts and types
- Tooling: Coordinated linting
- Separate task groups per language

### Cascading Adaptations

**When one change requires others:**
- Track dependencies
- Update imports
- Modify related code
- Document chain
- Link tasks in AGENTS.md

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
- Track in task notes

### React Migration Detection

**Identify migration needs:**
```javascript
// Detect React 18 patterns that need updating:
if (hasUseFormState && reactVersion >= 19) {
  createTask("Rename useFormState to useActionState", priority: "critical");
}

if (hasForwardRef && reactVersion >= 19) {
  createTask("Remove forwardRef, use ref prop directly", priority: "high");
}

if (hasCookiesSync && nextVersion >= 16) {
  createTask("Make component async, await cookies()", priority: "critical");
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

### Task Dependencies

**Track related tasks:**
```markdown
### Task 3: Update Component Props
Dependencies: Task 2 (Remove forwardRef)
Blocks: Task 4 (Update tests)
Related: Task 5 (Type definitions)
```

## Quick Reference

**Processing workflow:**
1. `pr_to_task init` - Initialize and generate AGENTS.md
2. `pr_to_task next` - View first/next task
3. Analyze project structure
4. For each task:
   - Assess appropriateness
   - Generate adaptation
   - Document reasoning
   - Implement changes
   - `pr_to_task mark-complete`
5. `pr_to_task status` - Check progress
6. Repeat until all tasks complete

**Key commands:**
```bash
# Start processing
pr_to_task init

# Work on tasks
pr_to_task next               # View current task
pr_to_task mark-complete      # Complete and move to next

# Track progress
pr_to_task status             # View all task statuses

# Reset if needed
pr_to_task reset              # Clear all progress
```

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
- middleware.ts → proxy.ts
- export function proxy (not middleware)
- Turbopack default
- React 19 required

**Plate.js (v49+):**
- All @udecode/plate* → @platejs/*
- Main import: 'platejs' not '@udecode/plate'
- With Next.js (bundler resolution): Use /react subpaths directly
- Without bundler resolution: Configure path aliases
- Server Components: Import WITHOUT /react (e.g., `@platejs/basic-nodes`)
- Client Components: Import WITH /react (e.g., `@platejs/basic-nodes/react`)
- Plugin renames (AlignPlugin → TextAlignPlugin)
- API changes (editor.api → editor.tf)
- Package consolidations

**TypeScript:**
- TypeScript 5+: Const type parameters
- satisfies operator
- Decorator metadata

**Common breaking changes to catch:**
- React 18→19: useFormState renamed
- Next.js 15→16: Dynamic APIs async
- Next.js 16: middleware.ts → proxy.ts
- React 19: ref prop handling
- Next.js 16: No React 18 support
- Plate.js pre-v49→v49: Major package restructure
- Plate.js v49: Server vs Client import patterns

**Remember:**
- Always start with pr_to_task init
- Use pr_to_task for all task management
- Track progress with mark-complete
- Context determines correctness
- Modern syntax when available
- Existing tools over new dependencies
- Document every adaptation
- Preserve functional intent
- Monitor overall progress with status
