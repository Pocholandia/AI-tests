## Entity Relationship Model


erDiagram
    Clientes ||--o{ Pedidos : "places"
    Clientes ||--o{ Tarifas : "uses"
    Productos ||--o{ PedidosLineas : "included in"
    Productos ||--o{ TarifasLineas : "priced in"
    Pedidos ||--o{ PedidosLineas : "contains"
    Tarifas ||--o{ TarifasLineas : "contains"
    
    Clientes {
        string RecordIdent
        string RecordVersion
        string Codigo
        string Descripcion
        string Contacto
        string Telefono
        string Email
        string Web
        string IDtarifa
        boolean Inactivo
    }
    
    Productos {
        string RecordIdent
        string RecordVersion
        string Codigo
        string Descripcion
        float Precio
        float IVA
        boolean Inactivo
    }
    
    Tarifas {
        string RecordIdent
        string RecordVersion
        string Codigo
        string Descripcion
        boolean Inactivo
    }
    
    Pedidos {
        string RecordIdent
        string RecordVersion
        string IDcliente
        string FechaHora
    }
    
    PedidosLineas {
        string RecordIdent
        string RecordVersion
        string IDpedido
        string IDproducto
        float Cantidad
        float Precio
        string Observaciones
    }
    
    TarifasLineas {
        string RecordIdent
        string RecordVersion
        string IDtarifa
        string IDproducto
        float Precio
    }