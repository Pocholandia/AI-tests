# Especificación de Filtrado de Pedidos

Este documento define la implementación detallada del sistema de filtrado de pedidos, un requisito crítico de la aplicación.

## Requisitos Funcionales

1. **Combinación de Filtros**: El sistema debe permitir filtrar por cualquier combinación de:
   - Cliente específico
   - Rango de fechas (inicio y fin)
   - Rango de total (mínimo y máximo)

2. **Comportamiento de Fecha**: Al filtrar por fecha, solo se considera la parte de fecha (YYYY-MM-DD) del timestamp del pedido, no la hora.

3. **Combinabilidad**: Todos los filtros son combinables, permitiendo al usuario aplicar cualquier combinación de criterios.

4. **Visualización Inicial**: No se muestran pedidos hasta que se aplique al menos un filtro.

5. **Experiencia de Usuario**: La lista y detalle sigue el mismo patrón UX que la sección de Clientes (moderna, clickable, dialog para detalle/editar/eliminar).

## Modelo de Datos para Filtros

```typescript
export interface IPedidosFiltros {
  clienteId: string | null;
  fechaInicio: string | null; // formato YYYY-MM-DD
  fechaFin: string | null;    // formato YYYY-MM-DD
  totalMinimo: number | null;
  totalMaximo: number | null;
  filtroAplicado: boolean;    // indica si ya se aplicó algún filtro
}

// Estado inicial - sin filtros aplicados
export const initialPedidosFiltros: IPedidosFiltros = {
  clienteId: null,
  fechaInicio: null,
  fechaFin: null,
  totalMinimo: null,
  totalMaximo: null,
  filtroAplicado: false
};
```

## Lógica de Filtrado

```typescript
export const filtrarPedidos = (pedidos: IPedido[], filtros: IPedidosFiltros): IPedido[] => {
  // Si no se ha aplicado ningún filtro, devolver array vacío
  if (!filtros.filtroAplicado) {
    return [];
  }
  
  return pedidos.filter(pedido => {
    // Filtro por cliente
    if (filtros.clienteId && pedido.IDcliente !== filtros.clienteId) {
      return false;
    }
    
    // Extraer solo la parte de fecha del timestamp (YYYY-MM-DD)
    const fechaPedido = pedido.FechaHora.split('T')[0];
    
    // Filtro por fecha inicio
    if (filtros.fechaInicio && fechaPedido < filtros.fechaInicio) {
      return false;
    }
    
    // Filtro por fecha fin
    if (filtros.fechaFin && fechaPedido > filtros.fechaFin) {
      return false;
    }
    
    // Calcular total del pedido
    const total = calcularTotalPedido(pedido);
    
    // Filtro por total mínimo
    if (filtros.totalMinimo !== null && total < filtros.totalMinimo) {
      return false;
    }
    
    // Filtro por total máximo
    if (filtros.totalMaximo !== null && total > filtros.totalMaximo) {
      return false;
    }
    
    // Si pasa todos los filtros, incluir el pedido
    return true;
  });
};

// Función auxiliar para calcular el total de un pedido
export const calcularTotalPedido = (pedido: IPedido): number => {
  // Esta función debe implementarse en features/pedidos/rules.ts
  // Debe sumar el precio * cantidad de cada línea del pedido
};
```

## Componente de Filtros de Pedidos

```tsx
import React, { useState } from 'react';
import { Button, Grid, TextField, MenuItem, Box, IconButton, Collapse } from '@mui/material';
import { DatePicker } from '@mui/x-date-pickers';
import FilterListIcon from '@mui/icons-material/FilterList';
import SearchIcon from '@mui/icons-material/Search';
import ClearIcon from '@mui/icons-material/Clear';
import { useClienteStore } from '../../stores/clienteStore';
import { IPedidosFiltros, initialPedidosFiltros } from '../../models/pedidoModels';

interface IPedidosFiltrosProps {
  onFiltroChange: (filtros: IPedidosFiltros) => void;
  filtros: IPedidosFiltros;
}

export const PedidosFiltros: React.FC<IPedidosFiltrosProps> = ({ onFiltroChange, filtros }) => {
  const [mostrarFiltrosAvanzados, setMostrarFiltrosAvanzados] = useState(false);
  const { clientes } = useClienteStore(state => ({ clientes: state.clientes }));
  
  // Clonar filtros para evitar mutación directa del estado
  const [filtrosLocales, setFiltrosLocales] = useState<IPedidosFiltros>({...filtros});
  
  const handleFiltroChange = (campo: keyof IPedidosFiltros, valor: any) => {
    setFiltrosLocales(prev => ({
      ...prev,
      [campo]: valor
    }));
  };
  
  const aplicarFiltros = () => {
    onFiltroChange({
      ...filtrosLocales,
      filtroAplicado: true
    });
  };
  
  const limpiarFiltros = () => {
    const nuevosFiltros = {...initialPedidosFiltros};
    setFiltrosLocales(nuevosFiltros);
    onFiltroChange(nuevosFiltros);
  };
  
  return (
    <Box sx={{ mb: 3, p: 2, bgcolor: 'background.paper', borderRadius: 1 }}>
      <Grid container spacing={2} alignItems="center">
        {/* Filtro básico por cliente */}
        <Grid item xs={12} sm={6} md={4}>
          <TextField
            select
            fullWidth
            label="Cliente"
            value={filtrosLocales.clienteId || ''}
            onChange={(e) => handleFiltroChange('clienteId', e.target.value || null)}
          >
            <MenuItem value="">Todos los clientes</MenuItem>
            {clientes.map(cliente => (
              <MenuItem key={cliente.RecordIdent} value={cliente.RecordIdent}>
                {cliente.Codigo} - {cliente.Descripcion}
              </MenuItem>
            ))}
          </TextField>
        </Grid>
        
        {/* Botones de acción */}
        <Grid item xs={12} sm={6} md={3}>
          <Box sx={{ display: 'flex', gap: 1 }}>
            <Button 
              variant="contained" 
              color="primary" 
              onClick={aplicarFiltros}
              startIcon={<SearchIcon />}
            >
              Buscar
            </Button>
            <Button 
              variant="outlined" 
              onClick={limpiarFiltros}
              startIcon={<ClearIcon />}
            >
              Limpiar
            </Button>
            <IconButton 
              onClick={() => setMostrarFiltrosAvanzados(!mostrarFiltrosAvanzados)}
              color={mostrarFiltrosAvanzados ? "primary" : "default"}
            >
              <FilterListIcon />
            </IconButton>
          </Box>
        </Grid>
        
        {/* Filtros avanzados */}
        <Grid item xs={12}>
          <Collapse in={mostrarFiltrosAvanzados}>
            <Grid container spacing={2}>
              {/* Rango de fechas */}
              <Grid item xs={12} sm={6} md={3}>
                <DatePicker
                  label="Fecha desde"
                  value={filtrosLocales.fechaInicio ? new Date(filtrosLocales.fechaInicio) : null}
                  onChange={(date) => {
                    const fechaStr = date ? date.toISOString().split('T')[0] : null;
                    handleFiltroChange('fechaInicio', fechaStr);
                  }}
                  slotProps={{ textField: { fullWidth: true } }}
                />
              </Grid>
              <Grid item xs={12} sm={6} md={3}>
                <DatePicker
                  label="Fecha hasta"
                  value={filtrosLocales.fechaFin ? new Date(filtrosLocales.fechaFin) : null}
                  onChange={(date) => {
                    const fechaStr = date ? date.toISOString().split('T')[0] : null;
                    handleFiltroChange('fechaFin', fechaStr);
                  }}
                  slotProps={{ textField: { fullWidth: true } }}
                />
              </Grid>
              
              {/* Rango de totales */}
              <Grid item xs={12} sm={6} md={3}>
                <TextField
                  type="number"
                  label="Total mínimo"
                  fullWidth
                  value={filtrosLocales.totalMinimo || ''}
                  onChange={(e) => handleFiltroChange('totalMinimo', e.target.value ? Number(e.target.value) : null)}
                  InputProps={{ inputProps: { min: 0 } }}
                />
              </Grid>
              <Grid item xs={12} sm={6} md={3}>
                <TextField
                  type="number"
                  label="Total máximo"
                  fullWidth
                  value={filtrosLocales.totalMaximo || ''}
                  onChange={(e) => handleFiltroChange('totalMaximo', e.target.value ? Number(e.target.value) : null)}
                  InputProps={{ inputProps: { min: 0 } }}
                />
              </Grid>
            </Grid>
          </Collapse>
        </Grid>
      </Grid>
    </Box>
  );
};
```

## Integración en el Store de Pedidos

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { IPedido, IPedidosFiltros, initialPedidosFiltros } from '../models/pedidoModels';
import { pedidoRepository } from '../repositories/implementations/pedidoRepository';
import { filtrarPedidos } from '../features/pedidos/rules';

interface IPedidoState {
  // Estado
  pedidos: IPedido[];
  pedidosFiltrados: IPedido[];
  filtros: IPedidosFiltros;
  loading: boolean;
  error: string | null;
  
  // Acciones
  fetchPedidos: () => Promise<void>;
  setFiltros: (filtros: IPedidosFiltros) => void;
  // Otras acciones...
}

export const usePedidoStore = create<IPedidoState>(
  persist(
    (set, get) => ({
      pedidos: [],
      pedidosFiltrados: [],
      filtros: initialPedidosFiltros,
      loading: false,
      error: null,
      
      fetchPedidos: async () => {
        set({ loading: true, error: null });
        try {
          const pedidos = await pedidoRepository.getAll();
          const filtros = get().filtros;
          const pedidosFiltrados = filtrarPedidos(pedidos, filtros);
          
          set({ 
            pedidos, 
            pedidosFiltrados,
            loading: false 
          });
        } catch (error) {
          set({ 
            error: (error as Error).message, 
            loading: false 
          });
        }
      },
      
      setFiltros: (filtros: IPedidosFiltros) => {
        const pedidos = get().pedidos;
        const pedidosFiltrados = filtrarPedidos(pedidos, filtros);
        
        set({ filtros, pedidosFiltrados });
      },
      
      // Implementar otras acciones...
    }),
    {
      name: 'pedido-storage',
      partialize: (state) => ({ 
        filtros: state.filtros // Persistir filtros entre sesiones 
      })
    }
  )
);
```

## Componente de Lista de Pedidos con Filtros

```tsx
import React, { useEffect } from 'react';
import { Box, Typography, List, CircularProgress, Alert } from '@mui/material';
import { PedidosFiltros } from './PedidosFiltros';
import { PedidoListItem } from './PedidoListItem';
import { usePedidoStore } from '../../stores/pedidoStore';

export const PedidosListContainer: React.FC = () => {
  const { pedidosFiltrados, filtros, loading, error, fetchPedidos, setFiltros } = usePedidoStore();
  
  useEffect(() => {
    fetchPedidos();
  }, [fetchPedidos]);
  
  return (
    <Box>
      <Typography variant="h5" sx={{ mb: 3 }}>
        Pedidos
      </Typography>
      
      <PedidosFiltros 
        filtros={filtros} 
        onFiltroChange={setFiltros} 
      />
      
      {loading ? (
        <Box sx={{ display: 'flex', justifyContent: 'center', my: 4 }}>
          <CircularProgress />
        </Box>
      ) : error ? (
        <Alert severity="error" sx={{ mb: 2 }}>{error}</Alert>
      ) : !filtros.filtroAplicado ? (
        <Alert severity="info" sx={{ mb: 2 }}>
          Aplique filtros para ver los pedidos
        </Alert>
      ) : pedidosFiltrados.length === 0 ? (
        <Alert severity="warning" sx={{ mb: 2 }}>
          No se encontraron pedidos con los filtros aplicados
        </Alert>
      ) : (
        <List>
          {pedidosFiltrados.map(pedido => (
            <PedidoListItem 
              key={pedido.RecordIdent} 
              pedido={pedido} 
            />
          ))}
        </List>
      )}
    </Box>
  );
};
```

## Pruebas para el Sistema de Filtrado

```typescript
// __tests__/pedidos/filtrado.test.ts
import { filtrarPedidos } from '../../rules/pedidoRules';
import { IPedido, IPedidosFiltros } from '../../models/pedidoModels';

// Mock data
const mockPedidos: IPedido[] = [
  {
    RecordIdent: '1',
    IDcliente: 'cliente1',
    FechaHora: '2025-05-01T10:30:00Z',
    // otros campos...
  },
  {
    RecordIdent: '2',
    IDcliente: 'cliente2',
    FechaHora: '2025-05-15T14:45:00Z',
    // otros campos...
  },
  // más pedidos...
];

// Mock para calcularTotalPedido
jest.mock('../../rules/pedidoRules', () => {
  const original = jest.requireActual('../../rules/pedidoRules');
  return {
    ...original,
    calcularTotalPedido: jest.fn((pedido) => {
      if (pedido.RecordIdent === '1') return 100;
      if (pedido.RecordIdent === '2') return 200;
      return 0;
    })
  };
});

describe('Filtrado de Pedidos', () => {
  test('debe retornar array vacío cuando no se ha aplicado ningún filtro', () => {
    const filtros: IPedidosFiltros = {
      clienteId: null,
      fechaInicio: null,
      fechaFin: null,
      totalMinimo: null,
      totalMaximo: null,
      filtroAplicado: false
    };
    
    const resultado = filtrarPedidos(mockPedidos, filtros);
    expect(resultado).toHaveLength(0);
  });
  
  test('debe filtrar por cliente correctamente', () => {
    const filtros: IPedidosFiltros = {
      clienteId: 'cliente1',
      fechaInicio: null,
      fechaFin: null,
      totalMinimo: null,
      totalMaximo: null,
      filtroAplicado: true
    };
    
    const resultado = filtrarPedidos(mockPedidos, filtros);
    expect(resultado).toHaveLength(1);
    expect(resultado[0].RecordIdent).toBe('1');
  });
  
  test('debe filtrar por rango de fechas correctamente', () => {
    const filtros: IPedidosFiltros = {
      clienteId: null,
      fechaInicio: '2025-05-10',
      fechaFin: '2025-05-20',
      totalMinimo: null,
      totalMaximo: null,
      filtroAplicado: true
    };
    
    const resultado = filtrarPedidos(mockPedidos, filtros);
    expect(resultado).toHaveLength(1);
    expect(resultado[0].RecordIdent).toBe('2');
  });
  
  test('debe filtrar por rango de total correctamente', () => {
    const filtros: IPedidosFiltros = {
      clienteId: null,
      fechaInicio: null,
      fechaFin: null,
      totalMinimo: 150,
      totalMaximo: 250,
      filtroAplicado: true
    };
    
    const resultado = filtrarPedidos(mockPedidos, filtros);
    expect(resultado).toHaveLength(1);
    expect(resultado[0].RecordIdent).toBe('2');
  });
  
  test('debe combinar múltiples filtros correctamente', () => {
    const filtros: IPedidosFiltros = {
      clienteId: 'cliente2',
      fechaInicio: '2025-05-01',
      fechaFin: '2025-05-20',
      totalMinimo: 150,
      totalMaximo: null,
      filtroAplicado: true
    };
    
    const resultado = filtrarPedidos(mockPedidos, filtros);
    expect(resultado).toHaveLength(1);
    expect(resultado[0].RecordIdent).toBe('2');
  });
});
```
