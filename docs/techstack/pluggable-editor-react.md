# Framework Pluggable Editor Registry (TypeScript/React)

This document describes the concrete implementation of the pluggable editor registry in this framework, where "framework" refers to both the programming language (TypeScript) and the supporting libraries/runtime (React, etc.).

For cross-language, cross-framework principles and generic API, see `generic/PLUGGABLE_EDITOR_REGISTRY.md`.

## Implementation Details (TypeScript/React)

The registry is implemented in `src/components/generic/EntityEditRegistry.tsx` as follows:

```tsx
import React from "react";

export interface EntityEditorProps<T = any> {
  value: T;
  onChange: (value: T) => void;
  label?: string;
  [key: string]: any;
}

const editorRegistry: Record<string, React.ComponentType<EntityEditorProps>> = {};

export function registerEditor(name: string, component: React.ComponentType<EntityEditorProps>) {
  editorRegistry[name] = component;
}

export function unregisterEditor(name: string) {
  delete editorRegistry[name];
}

export function getEditor(name: string): React.ComponentType<EntityEditorProps> | undefined {
  return editorRegistry[name];
}

export function listEditors(): string[] {
  return Object.keys(editorRegistry);
}

export function getEditorOrDefault(
  name: string,
  fallback: React.ComponentType<EntityEditorProps>
): React.ComponentType<EntityEditorProps> {
  return editorRegistry[name] || fallback;
}
```

> **Note:** This implementation is specific to the TypeScript/React stack. If you are using a different language or runtime, adapt the registry pattern to your environment's idioms (see the generic doc for guidance).

## Usage in This Framework

### Registering a Custom Editor

```tsx
import { registerEditor, EntityEditorProps } from "../../src/components/generic/EntityEditRegistry";

function ClienteSelectorEditor({ value, onChange, label }: EntityEditorProps<string>) {
  // ...custom UI
}

registerEditor('cliente.selector', ClienteSelectorEditor);
```

### Using a Custom Editor in EntityEdit

When rendering a field in `EntityEdit`, the framework looks up the editor by name using `getEditor`. If a matching editor is found, it is rendered with the appropriate props. If not, a default editor is used as fallback.

### Namespacing and Discovery

- Editor keys may use namespaces (e.g. `cliente.selector`).
- You can list all registered editors for debugging or dynamic UI-building with `listEditors()`.
- Editors can be overridden or unregistered at runtime.

---

For conceptual principles, generic API, and cross-framework patterns, see `generic/PLUGGABLE_EDITOR_REGISTRY.md`.
import { getEditorOrDefault, EntityEditorProps } from "../../src/components/generic/EntityEditRegistry";

const Editor = getEditorOrDefault(field.editor, DefaultEditor);
<Editor value={formState[field.name]} onChange={...} label={field.label} />
```

### Listing All Registered Editors

```ts
import { listEditors } from "../../src/components/generic/EntityEditRegistry";
console.log(listEditors());
```

## Principles
- All custom editors must accept `EntityEditorProps`.
- Use namespaced keys for clarity and organization.
- Always provide a fallback editor for robustness.
- Editors can be overridden or unregistered at runtime.
- The registry is discoverable for debugging and dynamic UI.
