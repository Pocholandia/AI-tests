# Table Metadata Registry

## Overview

The Table Metadata Registry provides a centralized system for managing table-related metadata in React applications. It allows you to define, extend, and consume table configurations from anywhere in your application.

## Core Features

- **Decentralized Registration**: Register slices of metadata from different modules
- **Deep Merging**: Updates merge cleanly with existing metadata
- **Component Discovery**: Dynamically access UI components based on table and mode
- **Type Safety**: Well-defined TypeScript interfaces
- **Event System**: Subscribe to registry changes
- **Testing Utilities**: Validate metadata consistency

## Integration Points

- Use the hooks API for React components
- Use the TableView component for dynamic rendering
- Explore table metadata with the MetadataExplorer
- Validate metadata consistency with testing utilities

## Best Practices

- Keep metadata registration close to its implementation
- Use feature-based organization for table metadata
- Validate metadata consistency as part of your CI process
- Use the hook-based API in React components
