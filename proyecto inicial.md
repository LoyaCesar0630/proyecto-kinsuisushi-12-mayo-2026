actua como un administrador de base de datos: proyecto Kinsui Sushi. Que entidades se necesitan para su gestion.

🍣 Entidades del Sistema — Kinsui Sushi

🧑‍🤝‍🧑 CLIENTES & PERSONAL
Cliente — datos del comensal/delivery

id_cliente, nombre, teléfono, email, dirección, fecha_registro, puntos_fidelidad

Empleado — staff del restaurante

id_empleado, nombre, rol (chef, mesero, cajero, repartidor), turno, salario, fecha_ingreso

Proveedor — suministro de insumos

id_proveedor, nombre, contacto, productos_suministrados, tiempo_entrega


🍱 MENÚ & PRODUCTOS
Categoría — clasificación del menú

id_categoria, nombre (Rolls, Nigiri, Sashimi, Bebidas, Postres…)

Producto — cada ítem del menú

id_producto, nombre, descripción, precio, id_categoria, imagen, disponible, calorias

Ingrediente — insumos de cocina

id_ingrediente, nombre, unidad_medida, stock_actual, stock_minimo, id_proveedor

Producto_Ingrediente (relación N:M)

id_producto, id_ingrediente, cantidad_requerida


📦 PEDIDOS
Pedido — orden del cliente

id_pedido, id_cliente, id_empleado, fecha_hora, tipo (local/delivery/takeout), estado, total, notas

Detalle_Pedido — líneas del pedido

id_detalle, id_pedido, id_producto, cantidad, precio_unitario, subtotal, personalizacion

Mesa — para servicio en local

id_mesa, numero, capacidad, zona, estado (libre/ocupada/reservada)

Reservacion — reservas anticipadas

id_reservacion, id_cliente, id_mesa, fecha_hora, num_personas, estado, notas


🚚 DELIVERY
Zona_Entrega — áreas de reparto

id_zona, nombre, costo_envio, tiempo_estimado, activa

Entrega — seguimiento de delivery

id_entrega, id_pedido, id_empleado (repartidor), id_zona, direccion_destino, hora_salida, hora_entrega, estado


<img width="826" height="556" alt="image" src="https://github.com/user-attachments/assets/e28a6689-d2d1-43b1-9e3e-cd1d56f745d9" />
