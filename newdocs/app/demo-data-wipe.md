# Demo Data & Data Wipe

This section describes how demo data is generated and how all data can be wiped in the Preventa application.

## Demo Data Generation
- Accessed via overflow menu
- Single form with a grid for each table: Productos, Tarifas, Clientes, Pedidos (in this order)
- Each row: editable box for number of records to generate
- Default values: 50 (Productos), 9 (Tarifas), 150 (Clientes), 300 (Pedidos)
- Data is generated in order to maintain referential integrity
- Data must be realistic and representative of the Spanish market

### Generation Rules
- Productos: random price 1-100, Spanish product names
- Tarifas: each tarifa gets TarifasLineas for products, with random price factors
- Clientes: 50% companies, 50% people; realistic names/emails; 70% assigned a tarifa
- Pedidos: each assigned to a random client; 5-15 lines per order; lines use random products and quantities

## Data Wipe
- Accessed via overflow menu
- Option to delete all clients (and related data)
- Confirmation required before wiping

## Example: Demo Data Form UI

```
┌───────────────────────────────┐
│ Demo Data Generation         │
├─────────────┬───────────────┤
│ Table       │ # to Generate │
├─────────────┼───────────────┤
│ Productos   │ [   50   ]    │
│ Tarifas     │ [    9   ]    │
│ Clientes    │ [  150   ]    │
│ Pedidos     │ [  300   ]    │
└─────────────┴───────────────┘
[Generate] [Cancel]
``` 