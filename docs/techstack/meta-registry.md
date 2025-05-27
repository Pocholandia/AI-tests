# Flexible Table-Metadata Registry (TypeScript Edition)

This documentation covers how to build a decentralised and highly extensible registry
of table metadata in React + TypeScript projects.

* `components` is a Record<string, React.ComponentType<any>>
* `extra` is a Record<string, unknown> for any ad-hoc data (sync handlers, feature flags, etc.)

## Documentation

* [Registry Overview](./meta-registry/enhanced-registry.md) - Core features and best practices
* [Deep Merge](./meta-registry/deepMerge.md) - How the metadata merging system works
* [Usage Guide](./meta-registry/usage-guide.md) - How to use the meta-registry in Preventa

## Quick start

1. Copy everything under examples/lib into src/lib.
2. Ensure your bundler eagerly imports files under features/**/meta*.ts(x)
   (see tableRegistry.ts for a ready-made glob).
3. Register per-table slices of metadata via registerTable.
4. Retrieve it anywhere with:

   ```typescript
   import { getTableMeta } from "@/lib/tableRegistry";
   const meta = getTableMeta("Custo");
   console.log(meta.extra?.syncTarget); // "CRM"
   ```

## Key Features

* **Component Discovery** - Dynamically render components based on table type and view context
* **Hook-based API** - Easy consumption of metadata in React components
* **Metadata Explorer UI** - Debug and explore registered tables
* **Automated Testing** - Ensure metadata consistency

See the [analysis document](./meta-registry/analysis.md) for a detailed breakdown of benefits and enhancement possibilities.

MIT © 2025
