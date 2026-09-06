# Team Challenge SQL - Rubén Jiménez Gutiérrez

## Parte 1 - SQL Murder Mistery

### Introducción

SQL Murder Mistery es un pequeña experiencia de juego utilizada para aprender y asentar ciertos conceptos de SQL. En ella usamos nuestros conocimientos de SQL para desentrañar un caso de asesinato utilizando distintas consultas SQL.

### Solución

La investigación y resolución de caso se encunetra en el archivo .\parte_1_sql_murder_mystery\investigacion.ipynb.

## Parte 2 - Modelo Big Query

### Introducción

## Modelo entidad relación

Se ha diseñado el modelo ER de la base de datos incluyendo tablas para todas las entidades indicadas en el enunciado: customers, categories, products, orders, order_items, payments y reviews. Para esto se han tenido en cuenta las siguientes cuestiones:

Las tablas orders y products se relacionan a través de la tabla intermedia order_items por los siguientes motivos:
  - La relación directa entre products y orders sería muchos a muchos (N:M). Es decir, un pedido puede contener múltiples productos diferentes y un mismo producto puede encontrarse en múltiples pedidos. 
  - Esta relación directa no es viable en el modelo 3NF. Si quisieramos incluir una lista de productos en un pedido estaríamos violando la primera forma normal (1NF).
  - Además la transacción de compra genera atributos propios que no pertenecen ni al pedido global ni al producto del catálogo como son el número de unidades y el precio en el momento de la compra.
  Gracias a la tabla order_items podemos establecer la siguiente relación: 
  - Múltiples líneas de pedido (order_items) pueden tener un mismo número de pedido (order). Lo que significa que dentro del mismo pedido se pueden solicitar múltiples líneas de pedido.
  - Cada línea de pedido representa el producto solicitado con su cantidad, precio y posibles descuentos.
  - De este modo se enlaza a cada pedido los múltiples productos con sus cantidades y precios.

El precio de compra (buy_price) se congela en la tabla order_items en lugar de leerlo directamente de la tabla products ya que el precio de catálogo puede variar con el tiempo y no coincidir con el del momento de la compra.

Los campos country y city residen directamente como atributos de texto en customers en lugar de aislarse en una tabla normalizada countries o cities. Esto xumple un objetivo de eficiencia analítica orientado a Google BigQuery. Evita operaciones de JOIN innecesarias y costosas al realizar agrupaciones y segmentaciones geográficas frecuentes (GROUP BY country, city), además de simplificar la ingesta y generación de datos sintéticos desde Python sin requerir el mantenimiento de catálogos geográficos maestros.

Si en orders almacenásemos el nombre del cliente (customer_name) además de customer_number se violaría la Tercera Forma Normal (3NF) que exige que todos los atributos que no son clave dependan directa y únicamente de la clave primaria, eliminando así las dependencias transitivas (prohíbe las dependencias transitivas). En orders, la clave primaria es order_number. Si añadimos customer_name, este dependería funcionalmente de customer_number, el cual a su vez depende de la clave primaria orders.order_number. Esto generaría redundancia innecesaria y anomalías de actualización (si el cliente cambia de nombre, habría que actualizar múltiples registros históricos en orders).

La entidad reviews se vincula a nivel de línea de pedido (order_item_id) con una relación 1:1 estricta (UNIQUE). Esto garantiza la regla de negocio de "compra verificada" (solo se puede opinar sobre productos realmente adquiridos) y evita que un cliente valore varias veces el mismo producto dentro de un mismo pedido.

![er_diagram.png](.\parte_2_modelo_bigquery\docs\er_diagram.png)
