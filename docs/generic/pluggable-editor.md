# Pluggable Editor Registry: Principles & Generic API

## Principles

- **Pluggability**: Allow arbitrary editor components to be registered and used for any field by string key.
- **Type Safety**: All editors must conform to a generic props interface or contract.
- **Namespacing**: Editor keys may use namespaces (e.g. `cliente.selector`) for organization.
- **Override & Unregister**: Editors can be overridden or removed at runtime.
- **Editor Discovery**: All registered editors can be listed for debugging or UI-building.
- **Default Fallback**: Always provide a fallback editor if a custom one is not found.

These principles apply regardless of programming language or supporting libraries. The registry concept is language-agnostic and can be implemented in any stack (e.g., JavaScript/TypeScript, Python, Java, C#, etc.).

## Generic Registry API

Below is a generic interface for a pluggable editor registry. The API signatures are illustrative, using TypeScript/React for clarity, but should be adapted to the conventions and idioms of your language and framework ("framework" here means both the language and any supporting libraries or runtime):

```pseudo
// Editor component contract (example in TypeScript/React)
interface EntityEditorProps<T = any> {
  value: T;
  onChange: (value: T) => void;
  label?: string;
  // ...other props as needed
}

// Register an editor
registerEditor(name: string, component: EditorComponent): void;

// Unregister an editor
unregisterEditor(name: string): void;

// Get an editor by name
getEditor(name: string): EditorComponent | undefined;

// List all editor names
listEditors(): string[];

// Get editor or fallback
getEditorOrDefault(name: string, fallback: EditorComponent): EditorComponent;
```

> **Note:** The actual implementation of these functions and the editor component contract will vary by language and runtime. For example, in Python, you might use classes or callables; in Java, interfaces and reflection; in C#, delegates and generics. Refer to your framework or language documentation for concrete examples.

## Example Usage

```ts
// Custom editor component
function ClienteSelectorEditor({ value, onChange, label }: EntityEditorProps<string>) {
  // ...custom UI
}

// Register
registerEditor('cliente.selector', ClienteSelectorEditor);

// Use in field definition
{
  name: 'IDcliente',
  label: 'Cliente',
  editor: 'cliente.selector',
}

// Render in form
const Editor = getEditorOrDefault(field.editor, DefaultEditor);
<Editor value={value} onChange={onChange} label={field.label} />
```

## Best Practices
- Register editors at app startup.
- Use clear, namespaced keys for organization.
- Always provide a fallback editor for robustness.
- Document new editors and their props.
