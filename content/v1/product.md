---
title: Product | ThreeSellers API
---

# Product API

## Obtener un listado de productos

    GET /api/v1/products.json

### Parámetros

per_page
: _Opcional_ **integer**. Número de resultados por página. Por defecto: `30`. Máximo: `3000`.

page
: _Opcional_ **integer**. Página para mostrar. Por defecto: `1`.

category_id
: _Opcional_ **String**. Filtra productos por categoría.

search
: _Optional_ **Hash**. Filtra productos en base a criterios de búsqueda. **TODO**

### Request
    curl -u "usuario:contraseña" http://login.3sellers.com/api/v1/products.json

### Response

<%= headers 200 %>
<%= json_data(:product) { |h| [h] } %>


## Obtener el número de productos

Obtiene el número de productos.

    GET /api/v1/products/count.json

### Parámetros

category_id
: _Opcional_ **String**. Filtra productos por categoría.

### Request
    curl -u "usuario:contraseña" http://login.3sellers.com/api/v1/products/count.json

### Response

<%= headers 200 %>
<%= json_data({:count => 1}) %>


## Obtener un producto

Obtiene un producto a partir de su ID.

    GET /api/v1/products/#{id}.json

### Request
    curl -u "usuario:contraseña" http://login.3sellers.com/api/v1/products/<%= resource(:product)["id"] %>.json

### Response
<%= headers 200 %>
<%= json_data(:product) %>

## Obtener un producto con su SKU

En vez de identificar al producto a través de su id, también es posible identificarlo a través del SKU de una de sus opciones de venta. Para ello, se pasa el SKU en vez del id, y se añade el parametro `by_sku=1` a la petición.

Sin embargo, esto sólo funciona si no existe otro producto distintos con el mismo SKU. Si no se puede identificar un producto único a partir del SKU, la petición fallará con un código de estado 400.

    GET /api/v1/products/#{sku}.json?by_sku=1

### Parámetros

by_sku
: _Obligatorio_ **integer**. `1`.

### Request
    curl -u "usuario:contraseña" http://login.3sellers.com/api/v1/products/<%= resource(:product)["sellOptions"].first["sku"] %>.json?by_sku=1

### Response
<%= headers 200 %>
<%= json_data(:product) %>


## Crear un nuevo producto

Crea un nuevo producto

    POST /api/v1/products.json

### Input

title
: _Opcional_ **string**

content
: _Opcional_ **string**

category_id
: _Optional_ **integer**

category_ids
: _Opcional_ **array** - Cada elemento del Array es un Hash `{"id" => integer}`.

isDraft
: _Opcional_ **boolean** - `true` para marcar el producto en borrador, `false`.
publicarlo.

highlighted
: _Opcional_ **boolean** - `true` para destacarlo, `false` para no destacarlo.

relevance
: _Opcional_ **integer**

tags
: _Opcional_ **array** - Cada elemento de la lista es una **string**.

fields
: _Opcional_ **array** **TODO**

<%= json \
  :title          => resource(:product)['title'],
  :content        => resource(:product)['content'],
  :categories     => resource(:product)['categories'],
  :tags           => ["tag1", "tag2"]
%>

### Request

    curl -u "usuario:contraseña"
         -d "title=<%= resource(:product)["title"] %>"
         -d "content=<%= resource(:product)["content"] %>"
         -d "categories[][id]=<%= resource(:product)["categories"][0]["id"] %>"
         -d "categories[][id]=<%= resource(:product)["categories"][1]["id"] %>"
         http://login.3sellers.com/api/v1/products.json

### Response
<%= headers 200 %>
<%= json_data(:product) %>

Los productos necesitan un título y una categoría. Intentar crear un producto sin esta información devolverá un error.

### Request

    curl -u "usuario:contraseña"
         -d "content=<%= resource(:product)['content'] %>"
         http://login.3sellers.com/api/v1/products.json

### Respuesta

<%= headers 422 %>


## Modificar un producto existente

Actualiza un producto

Identificando el producto a través de su id

    PUT /api/v1/products/#{id}.json

Identificando el producto a través de un SKU [Más info](#obtener-un-producto-con-su-sku)

    PUT /api/v1/products/#{sku}.json?by_sku=1

### Request

    curl -u "usuario:contraseña"
         -X PUT
         -d "title=Camiseta estampada en oferta"
         http://login.3sellers.com/api/v1/products/<%= resource(:product)["id"] %>.json

### Respuesta

<%= headers 200 %>
<%= json_data(:product){|h| h.merge("title" => "Camiseta estampada en oferta")} %>

## Actualizar el stock

Actualiza el stock de una opción de venta de un producto

Identificando el producto a través de su id

    POST /api/v1/sell_options/#{id}/update_stock.json

Identificando el producto a través de un SKU [Más info](#obtener-un-producto-con-su-sku)

    POST /api/v1/sell_options/#{sku}/update_stock.json?by_sku=1

### Input

delta
: _Obligatorio_ **número** - El número usado para actualizar el stock. Números positivos incrementan el stock, números negativos lo decrementan.

### Request

      curl -u "usuario:contraseña"
           -X POST
           -d "delta=1"
           http://login.3sellers.com/api/v1/sell_options/<%= resource(:product)["id"] %>/update_stock.json

### Respuesta

<%= headers 200 %>
<%= json_data(:product){|h| h["sellOptions"].first.merge!("stock" => 1); h} %>

## Eliminar un producto

Elimina un producto

Identificando el producto a través de su id

    DELETE /api/v1/products/#{id}.json

Identificando el producto a través de un SKU [Más info](#obtener-un-producto-con-su-sku)

    DELETE /api/v1/products/#{sku}.json?by_sku=1

### Request

      curl -u "login:contraseña" -X DELETE http://login.3sellers.com/api/v1/products/<%= resource(:product)["id"] %>.json

### Response

<%= headers 204 %>
