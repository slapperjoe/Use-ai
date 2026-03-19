## Code Review Guidelines

When asked to review code or a diff, follow this checklist in order:

1. **Security** — Check for input validation gaps, hardcoded secrets, insecure
   dependencies, and missing authentication/authorisation checks.
2. **Error Handling** — All `await` calls and fallible operations must have
   error handling. Rust: use `?` or explicit `match`. TypeScript: use
   `try/catch` or `.catch()`. Never swallow errors silently.
3. **Test Coverage** — New public functions/methods should have corresponding
   unit tests. Flag untested code explicitly.
4. **SOLID Principles** — Highlight functions longer than ~40 lines, god-classes,
   and tight coupling to external systems that should be injected.
5. **Logging** — Ensure errors are logged with context. No `console.log` in
   production code; use the project logger instead.
6. **Cross-Platform** — File paths must use the `path` module (TypeScript) or
   `std::path::Path` (Rust), not hardcoded separators.
7. **Sensitive Data** — No PII, credentials, or classified data in log messages,
   error responses, or source files.

Format review output as a structured list: severity (error/warning/info),
category, and a concise actionable message.

## Unit Test Generation Guidelines

When generating unit tests, follow these rules:

### General Rules
- Always include: happy path, error/exception cases, boundary values, null/empty inputs.
- Mock all external dependencies (file system, HTTP, database, Tauri commands).
- Test files live alongside source files in `__tests__/` subdirectories or
  `.test.ts` / `.test.rs` sibling files.
- Add a comment at the top of generated files:
  `// AI-generated test stubs — review before committing.`

### TypeScript / JavaScript (Vitest)
```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest';

describe('MyModule', () => {
  beforeEach(() => {
    vi.resetAllMocks();
  });

  it('returns expected value for valid input', () => {
    expect(myFunction('valid')).toBe('expected');
  });

  it('throws on null input', () => {
    expect(() => myFunction(null)).toThrow();
  });
});
```

### Rust (#[cfg(test)])
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_happy_path() {
        // Arrange
        // Act
        // Assert
    }

    #[test]
    fn test_error_case() {
        // ...
    }
}
```

### Generating tests for an existing file
In Copilot Chat, use this prompt pattern:
```
/tests  Generate Vitest tests for the selected function. Include positive,
negative, and boundary cases. Mock Tauri invoke calls with vi.mock('@tauri-apps/api/core').
```