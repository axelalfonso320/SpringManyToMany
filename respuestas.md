# Práctica Spring Many-to-Many

## 1. ¿Cómo afectará JPA a la base de datos? ¿Qué tabla creará y qué columnas tendrá?
La entidad `Department` crea la tabla `categorias`. Por la relación `@ManyToMany` con `@JoinTable`, JPA crea además una tabla intermedia llamada `producto_deparments` con dos columnas: `product_id` (llave foránea hacia `categorias`, por ser `joinColumns` del lado propietario) y `deparments_id` (llave foránea hacia `producto`, por ser `inverseJoinColumns`). Juntas forman la llave primaria compuesta. Una relación muchos a muchos no puede guardarse con una sola columna FK, por eso necesita esta tabla de unión. Aunque los nombres de las columnas parecen invertidos, `product_id` guarda el ID de la categoría y `deparments_id` el del producto.

## 2. ¿Qué significa `@JsonIgnore` y por qué se usa en `Producto`?
Indica a Jackson que no serialice ese campo al convertir el objeto a JSON. Se usa para evitar un ciclo infinito: `Department` contiene sus productos y cada `Producto` contendría sus `deparments`, que a su vez contienen sus productos, y así sin fin (provocaría un `StackOverflowError`). Se coloca en `Producto` porque es el lado inverso de la relación (`mappedBy`).

## 3. ¿Cuál es la función del endpoint `addProductoCategoria/{productId}/{categoriaId}`?
Asocia un producto existente con una categoría existente. Busca ambos por su ID, agrega el producto al conjunto `productos` de la categoría y la guarda; JPA inserta entonces una fila en `producto_deparments`.

## 4. ¿Por qué se declaran dos repositorios?
Cada repositorio trabaja con una sola entidad: `categoriaRepo` con `Department` y `productoRepo` con `Producto`. Para asociar ambos hay que buscar las dos entidades en la base de datos, y se guarda con `categoriaRepo` porque `Department` es el lado propietario de la relación.
