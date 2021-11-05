# Esquemas de datos en MongoDB

## Modelos de datos

Disponemos de dos modelos de datos:

### Modelo de datos denormalizado (documentos o arrays de documentos embebidos)

El modelo denormalizado es el óptimo e ideal para usar en MongoDB y al
que habremos de recurrir siempre que se pueda.

Ejemplo:
Si tenemos una colección productos y esos datos tienen unos distribuidores enlazados, siempre que
se pueda los introduciremos como datos embebidos.

Colección productos

{
    producto: "Zapatillas ZXV",
    marca: "Nike",
    ...,
    distribuidores: [
        {nombre: 'Servizapas', contacto: '...', ...},
        {nombre: 'Distribuciones', contacto: '...', ...},

    ]
}

### Modelo de datos normalizado (relacional)

Implementa referencias entre campos de distintas colecciones. Al contrario que el denormalizado, este 
modelo de datos se evitará siempre que se pueda en MongoDB.

Siguiendo el mismo ejemplo anterior:

Colección productos

{
    producto: "Zapatillas ZXV",
    marca: "Nike",
    ...,
    distribuidores: ['A123','G456']
}

Colección distribuidores

{codigo: 'A123', nombre: 'Servizapas', contacto: '...', ...}, // La referencia también se puede usar con _id
{nombre: 'G456', 'Distribuciones', contacto: '...', ...},

## Patrones de datos

### Patrón de relaciones One-to-One

- Siempre se usará el modelo denormalizado.
- Con una sola consulta se consiguen todos los datos.

Ejemplo, datos de usuario y datos de dirección/es:

Colección usuarios.

{
    _id: 'sddvkjsdvkjh',
    nombre: 'John Doe',
    direccion: {
        calle: 'Gran Vía, 100',
        cp: '28001',
        localidad: 'Madrid
    }
}

### Patrón de relaciones One-to-few

Intentaremos usar el modelo denormalizado, siempre que:
    - El lado one de la relación sea el que más consultas recibe.
    - No serán frecuentes las escrituras en el lado few.

Ejemplo, datos de productos y datos de las imágenes de los productos.

Colección productos.

{
    producto: 'Camiseta VBBDSH',
    marca: 'Adidas',
    imagenes: [
        {url: 'https://repositorio/kdsckjhds.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/fsdfsdfsd.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/kdfeqwewhds.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/ewscfewfewf.jpg', textoAlt: 'lorem ipsum...'},
        ...
    ]
}

### Patrón de relaciones One-to-many

- Hay que estudiar cada caso y dependiendo de la evolución previsible
de los datos seleccionar denormalizado (si es posible) o normalizado.
- Si se prevé que la parte many escalará hacia muchos valores sería necesario
el modelo normalizado (ver One-to-skillions).

### Patrón de relaciones One-to-skillions

- Habrá que usar el modelo normalizado para evitar superar el límite de 16MB por
documento de MongoDB.

Por ejemplo, datos de productos y datos de opiniones.

Colección productos

{
    sku: 'HSH123',
    producto: 'Camiseta VBBDSH',
    marca: 'Adidas',
    imagenes: [
        {url: 'https://repositorio/kdsckjhds.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/fsdfsdfsd.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/kdfeqwewhds.jpg', textoAlt: 'lorem ipsum...'},
        {url: 'https://repositorio/ewscfewfewf.jpg', textoAlt: 'lorem ipsum...'},
        ...
    ],
    opiniones: ['csjgcjdgsj','dasdasdasf','uikuikuiku', ..., 'vbfrtrtghrt',...]
}

Colección opiniones

{_id: 'csjgcjdgsj', sku: 'HSH123', opinion: 'Lorem ipsum...', stars: 3, user: ...}
{_id: 'dasdasdasf', sku: 'HSH123', opinion: 'Lorem ipsum...', stars: 3, user: ...}
{_id: 'uikuikuiku', sku: 'HSH123', opinion: 'Lorem ipsum...', stars: 3, user: ...}
... miles de opiniones
{_id: 'vbfrtrtghrt', sku: 'HSH123', opinion: 'Lorem ipsum...', stars: 3, user: ...}
...

### Patrón many-to-many