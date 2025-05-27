## Datos Demo: Generación Masiva

La generación de datos demo se realiza mediante un único formulario que muestra una cuadrícula con las siguientes tablas en este orden: Productos, Tarifas, Clientes, Pedidos. Cada fila tiene una caja editable para indicar la cantidad de registros a generar, con los siguientes valores por defecto:

- Productos: 50
- Tarifas: 9
- Clientes: 150
- Pedidos: 300

El usuario puede modificar estos valores antes de generar los datos. Al confirmar, los datos se crean en el mismo orden (Productos → Tarifas → Clientes → Pedidos) para asegurar la integridad referencial (por ejemplo, los pedidos hacen referencia a clientes, productos y tarifas ya existentes). Los datos deben ser realistas y representativos del mercado español.

1. **Productos**
Para generar los productos, se aplica esta regla:
- Se generan tantos productos como se indique en el formulario.
- Se asigna un precio aleatorio entre 1 y 100, calculado como Math.random()*Math.random()*100
- Se asigna un nombre que razonablemente pueda corresponder a un artículo español, usando una lista de 100 nombres de productos aleatorios. 
2. **Tarifas**
Para cada Tarifa que se genere, se generarán las TarifaLineas correspondientes. 
Para generar las líneas, se aplica esta regla:
- Primero calcula el FactorPrecio, un valor aleatorio entre 0,70 y 0,90.
- Luego calcula la Completitud, un valor aleatorio entre 0,90 y 1.
- Itera por todos los Productos, calculando cada vez un valor aleatorio entre 0 y 1. Si el valor es menor que Completitud, añádelo  a la Tarifa, con un precio calculado como el precio del Producto multiplicado por FactorPrecio.
3. **Clientes**
Para generar los clientes, se aplica esta regla:
- un 50% de los clientes serán empresas, y un 50% de los clientes serán personas.
- Los nombres de las empresas serán nombres de empresas aleatorios (en base a una lista de 50 palabras y componiendo 2 de ellas), y los nombres de las personas serán nombres de personas aleatorios (nombre apellido apellido, en base a una lista de 50 nombres y 50 apellidos).
- Los emails de las empresas tendrán la forma info@empresa.com, y los emails de las personas tendrán la forma nombre.apellidos@gmail.com.
- El 70% de los clientes tendrán asignada una de las tarifas generadas, y el 30% restante no tendrán tarifa asignada.
4. **Pedidos**
Para generar los pedidos, se aplica esta regla:
- Se generan tantos pedidos como se indique en el formulario.
- Para cada pedido, se elige un cliente aleatorio.
- Se generan entre 5 y 15 líneas de pedido para cada pedido.
- Para cada línea de pedido, se elige un producto aleatorio.
- Se asigna una cantidad aleatoria entre 1 y 10.
- Si el cliente tiene una tarifa asignada, se asigna el precio de la tarifa, y si no la tiene, se asigna un precio aleatorio entre el precio del producto y el precio del producto - 5%.
- A un 10% de las líneas se asigna una observación aleatoria.