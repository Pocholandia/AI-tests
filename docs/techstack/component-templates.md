# Component Templates

This document defines the standard templates for different types of components in the application. These templates ensure consistency and facilitate AI-driven development (VIBE coding).

## Presentation Component

```tsx
import React from 'react';
import { Box, Typography } from '@mui/material';

interface INombreComponenteViewProps {
  // Specific props
  data: any;
  onAction?: () => void;
}

export const NombreComponenteView: React.FC<INombreComponenteViewProps> = ({
  data,
  onAction,
}) => {
  return (
    <Box>
      <Typography variant="h6">Component Title</Typography>
      {/* Component content */}
    </Box>
  );
};
```

## Container Component

```tsx
import React, { useEffect, useState } from 'react';
import { NombreComponenteView } from './NombreComponenteView';
import { useNombreStore } from '../../stores/nombreStore';

export const NombreComponente: React.FC = () => {
  // Local state if needed
  const [localState, setLocalState] = useState<any>(null);
  
  // Zustand selector
  const { data, actions } = useNombreStore(state => ({
    data: state.data,
    actions: {
      fetchData: state.fetchData,
      updateData: state.updateData
    }
  }));
  
  // Effects
  useEffect(() => {
    actions.fetchData();
  }, [actions]);
  
  // Handlers
  const handleAction = () => {
    // Handler logic
  };
  
  return (
    <NombreComponenteView 
      data={data}
      onAction={handleAction}
    />
  );
};
```

## Form with React Hook Form

```tsx
import React from 'react';
import { useForm, Controller } from 'react-hook-form';
import { TextField, Button, Box, Grid } from '@mui/material';
import { IEntidadModel } from '../../models/entidadModel';

interface INombreFormProps {
  initialData?: IEntidadModel;
  onSubmit: (data: IEntidadModel) => void;
  onCancel: () => void;
}

export const NombreForm: React.FC<INombreFormProps> = ({
  initialData,
  onSubmit,
  onCancel
}) => {
  const { control, handleSubmit, formState: { errors } } = useForm<IEntidadModel>({
    defaultValues: initialData || {
      // Default values
    }
  });
  
  return (
    <Box component="form" onSubmit={handleSubmit(onSubmit)} noValidate>
      <Grid container spacing={2}>
        <Grid item xs={12}>
          <Controller
            name="field1"
            control={control}
            rules={{ required: 'Required field' }}
            render={({ field }) => (
              <TextField
                {...field}
                label="Field Label"
                fullWidth
                error={!!errors.field1}
                helperText={errors.field1?.message}
              />
            )}
          />
        </Grid>
        
        {/* More fields here */}
        
        <Grid item xs={12} sx={{ mt: 2, display: 'flex', justifyContent: 'flex-end' }}>
          <Button onClick={onCancel} sx={{ mr: 1 }}>
            Cancel
          </Button>
          <Button type="submit" variant="contained" color="primary">
            Save
          </Button>
        </Grid>
      </Grid>
    </Box>
  );
};
```

## Modal Dialog

```tsx
import React from 'react';
import { Dialog, DialogTitle, DialogContent, DialogActions, Button, IconButton } from '@mui/material';
import CloseIcon from '@mui/icons-material/Close';

interface IPresentationDialogProps {
  open: boolean;
  onClose: () => void;
  title: string;
  maxWidth?: 'xs' | 'sm' | 'md' | 'lg' | 'xl';
  fullWidth?: boolean;
  fullScreen?: boolean;
}

export const PresentationDialog: React.FC<IPresentationDialogProps> = ({
  open,
  onClose,
  title,
  children,
  maxWidth = 'sm',
  fullWidth = true,
  fullScreen = false,
}) => {
  return (
    <Dialog
      open={open}
      onClose={onClose}
      maxWidth={maxWidth}
      fullWidth={fullWidth}
      fullScreen={fullScreen}
    >
      <DialogTitle sx={{ m: 0, p: 2, display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
        {title}
        <IconButton
          aria-label="close"
          onClick={onClose}
          sx={{ color: 'grey.500' }}
        >
          <CloseIcon />
        </IconButton>
      </DialogTitle>
      <DialogContent dividers>
        {children}
      </DialogContent>
    </Dialog>
  );
};
```

## List with Filtering

```tsx
import React, { useState } from 'react';
import { Box, TextField, IconButton, List, ListItem, Paper } from '@mui/material';
import SearchIcon from '@mui/icons-material/Search';
import FilterListIcon from '@mui/icons-material/FilterList';

interface IFilters {
  text: string;
  // Other specific filters
}

interface IPresentationListProps {
  data: any[];
  onItemClick: (item: any) => void;
}

export const PresentationList: React.FC<IPresentationListProps> = ({ data, onItemClick }) => {
  const [filters, setFilters] = useState<IFilters>({ text: '' });
  const [showAdvancedFilters, setShowAdvancedFilters] = useState(false);
  
  // Filtering function
  const filteredItems = data.filter(item => {
    // Filtering logic
    return item.name.toLowerCase().includes(filters.text.toLowerCase());
  });
  
  const handleFilterChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setFilters(prev => ({
      ...prev,
      [e.target.name]: e.target.value
    }));
  };
  
  return (
    <Box>
      <Box sx={{ display: 'flex', mb: 2 }}>
        <TextField
          name="text"
          value={filters.text}
          onChange={handleFilterChange}
          placeholder="Search..."
          fullWidth
          size="small"
          InputProps={{
            endAdornment: <SearchIcon />
          }}
        />
        <IconButton onClick={() => setShowAdvancedFilters(!showAdvancedFilters)}>
          <FilterListIcon />
        </IconButton>
      </Box>
      
      {showAdvancedFilters && (
        <Box sx={{ mb: 2 }}>
          {/* Advanced filters */}
        </Box>
      )}
      
      <Paper>
        <List>
          {filteredItems.length === 0 ? (
            <ListItem>No results found</ListItem>
          ) : (
            filteredItems.map(item => (
              <ListItem 
                key={item.id} 
                button 
                onClick={() => onItemClick(item)}
              >
                {/* Item content */}
              </ListItem>
            ))
          )}
        </List>
      </Paper>
    </Box>
  );
};
```

## Zustand Store

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { IEntityRepository } from '../repositories/interfaces/IEntityRepository';
import { entityRepository } from '../repositories/implementations/entityRepository';

export interface IEntityState {
  // State
  items: any[];
  loading: boolean;
  error: string | null;
  syncStatus: 'synced' | 'pending' | 'error';
  
  // Actions
  fetchItems: () => Promise<void>;
  addItem: (item: any) => Promise<void>;
  updateItem: (id: string, item: any) => Promise<void>;
  deleteItem: (id: string) => Promise<void>;
  syncItems: () => Promise<void>;
}

// Initialize with dependency injection for testing
export const createEntityStore = (repository: IEntityRepository) => {
  return create<IEntityState>(
    persist(
      (set, get) => ({
        items: [],
        loading: false,
        error: null,
        syncStatus: 'synced',
        
        fetchItems: async () => {
          set({ loading: true, error: null });
          try {
            const items = await repository.getAll();
            set({ items, loading: false });
          } catch (error) {
            set({ error: (error as Error).message, loading: false });
          }
        },
        
        addItem: async (item) => {
          set({ loading: true, error: null });
          try {
            const id = await repository.save(item);
            set(state => ({
              items: [...state.items, { ...item, id }],
              loading: false,
              syncStatus: 'pending'
            }));
          } catch (error) {
            set({ error: (error as Error).message, loading: false });
          }
        },
        
        updateItem: async (id, item) => {
          set({ loading: true, error: null });
          try {
            await repository.update(id, item);
            set(state => ({
              items: state.items.map(i => i.id === id ? { ...i, ...item } : i),
              loading: false,
              syncStatus: 'pending'
            }));
          } catch (error) {
            set({ error: (error as Error).message, loading: false });
          }
        },
        
        deleteItem: async (id) => {
          set({ loading: true, error: null });
          try {
            await repository.delete(id);
            set(state => ({
              items: state.items.filter(i => i.id !== id),
              loading: false,
              syncStatus: 'pending'
            }));
          } catch (error) {
            set({ error: (error as Error).message, loading: false });
          }
        },
        
        syncItems: async () => {
          if (!navigator.onLine) {
            set({ error: 'No internet connection' });
            return;
          }
          
          set({ loading: true, error: null });
          try {
            await repository.sync();
            set({ syncStatus: 'synced', loading: false });
          } catch (error) {
            set({ 
              error: (error as Error).message, 
              loading: false,
              syncStatus: 'error'
            });
          }
        },
      }),
      {
        name: 'entity-storage',
        partialize: (state) => ({ items: state.items })
      }
    )
  );
};

// Real instance with concrete implementation
export const useEntityStore = createEntityStore(entityRepository);
```

## Custom Hook

```typescript
import { useState, useCallback } from 'react';

export function useCustomHook(initialValue: any) {
  const [value, setValue] = useState(initialValue);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const handleOperation = useCallback(async (operationFn: () => Promise<any>) => {
    setLoading(true);
    setError(null);
    try {
      const result = await operationFn();
      setLoading(false);
      return result;
    } catch (error) {
      setError((error as Error).message);
      setLoading(false);
      throw error;
    }
  }, []);
  
  // Specific functions of the hook
  const customOperation = useCallback(async (param: any) => {
    return handleOperation(async () => {
      // Sorting logic
      return /* resultado */;
    });
  }, [handleOperation]);
  
  return {
    value,
    loading,
    error,
    customOperation,
    // Otras funciones o propiedades
  };
}
```

## Repositorio

```typescript
import { IEntidadModel } from '../../models/entidadModel';
import { IEntidadRepository } from '../interfaces/IEntidadRepository';
import { db } from '../../config/db';

class EntidadIndexedDBRepository implements IEntidadRepository {
  async getAll(): Promise<IEntidadModel[]> {
    try {
      return await db.entidades.toArray();
    } catch (error) {
      console.error('Error en getAll:', error);
      throw new Error('Error al obtener las entidades');
    }
  }
  
  async getById(id: string): Promise<IEntidadModel | null> {
    try {
      return await db.entidades.get(id);
    } catch (error) {
      console.error('Error en getById:', error);
      throw new Error(`Error al obtener la entidad con id ${id}`);
    }
  }
  
  async save(entidad: IEntidadModel): Promise<string> {
    try {
      const timestamp = new Date().toISOString();
      const entidadToSave = {
        ...entidad,
        SyncStatus: 'pending_create',
        LocalModifiedDate: timestamp,
        RecordVersion: 1
      };
      
      const id = await db.entidades.add(entidadToSave);
      return id.toString();
    } catch (error) {
      console.error('Error en save:', error);
      throw new Error('Error al guardar la entidad');
    }
  }
  
  async update(id: string, entidad: Partial<IEntidadModel>): Promise<void> {
    try {
      const existing = await this.getById(id);
      if (!existing) {
        throw new Error(`No existe entidad con id ${id}`);
      }
      
      const timestamp = new Date().toISOString();
      const updatedEntidad = {
        ...existing,
        ...entidad,
        SyncStatus: 'pending_update',
        LocalModifiedDate: timestamp,
        RecordVersion: existing.RecordVersion + 1
      };
      
      await db.entidades.update(id, updatedEntidad);
    } catch (error) {
      console.error('Error en update:', error);
      throw new Error(`Error al actualizar la entidad con id ${id}`);
    }
  }
  
  async delete(id: string): Promise<void> {
    try {
      const existing = await this.getById(id);
      if (!existing) {
        throw new Error(`No existe entidad con id ${id}`);
      }
      
      // Marcamos como pendiente de eliminar en lugar de eliminar directamente
      const timestamp = new Date().toISOString();
      await db.entidades.update(id, {
        SyncStatus: 'pending_delete',
        LocalModifiedDate: timestamp,
        RecordVersion: existing.RecordVersion + 1
      });
    } catch (error) {
      console.error('Error en delete:', error);
      throw new Error(`Error al eliminar la entidad con id ${id}`);
    }
  }
  
  async sync(): Promise<void> {
    try {
      // Synchronization logic con el servidor
      const pendingItems = await db.entidades
        .where('SyncStatus')
        .anyOf(['pending_create', 'pending_update', 'pending_delete'])
        .toArray();
      
      // Aquí iría la lógica de envío al servidor
      // ...
      
      // Actualizar estado de sincronización
      await Promise.all(
        pendingItems.map(async (item) => {
          if (item.SyncStatus === 'pending_delete') {
            // Si se sincronizó correctamente, ahora sí eliminamos
            await db.entidades.delete(item.RecordIdent);
          } else {
            // Marcar como sincronizado
            await db.entidades.update(item.RecordIdent, {
              SyncStatus: 'synced',
              LastSyncDate: new Date().toISOString()
            });
          }
        })
      );
    } catch (error) {
      console.error('Error en sync:', error);
      throw new Error('Error durante la sincronización');
    }
  }
}

export const entidadRepository = new EntidadIndexedDBRepository();
```