# Validación en MongoDB

La validación en MongoDB se establece, de manera voluntaria, a nivel de colección y se define
con un operador $jsonSchema que utiliza un objeto con una serie de propiedades.

## Validación cuando se crea la colección

La propiedad validator del objeto de opciones a la cual se le pasa el operador $jsonSchema con el
objeto de validación.

```
use clinica

db.createCollection("pacientes", {
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["nombre","apellidos","dni"],
            properties: {
                nombre: {
                    bsonType: "string",
                    description: "debe ser string y obligatorio"
                },
                apellidos: {
                    bsonType: "string",
                    description: "debe ser string y obligatorio"
                },
                dni: {
                    bsonType: "string",
                    description: "debe ser string y obligatorio"
                },
                saldo: {
                    bsonType: "number",
                    minimum: 500,
                    maximum: 50000,
                    description: "deber ser number entre 500 y 50000"
                },
                fechaNacimiento: {
                    bsonType: "date",
                    description: "debe ser fecha"
                },
                fechaSubscripcion: {
                    bsonType: ["date","null"],
                    description: "debe ser fecha o null"
                },
                direccion: {
                    bsonType: "object",
                    properties: {
                        calle: {
                            bsonType: "string",
                            description: "debe ser string"      
                        },
                        cp: {
                            bsonType: "string",
                            description: "debe ser string"      
                        },
                        localidad: {
                            bsonType: "string",
                            enum: ["Madrid","Alcorcón","Getafe"],
                            description: "debe ser localidad de Comunidad Madrid"      
                        }
                    }
                }
            }
        }
    }
})


```

Comprobamos:

```
db.pacientes.insert({nombre: "Juan", apellidos: "Gómez"}) // Devolverá error porque le falta dni
```

 { index: 0,
             code: 121,
             errmsg: 'Document failed validation',


```
db.pacientes.insert({nombre: "Juan", apellidos: "Gómez", dni: "46875646T"}) // Ok

db.pacientes.insert({nombre: "Juan", apellidos: "Gómez", dni: 46875646T}) // Error porque dni es string

db.pacientes.insert({
    nombre: "Raquel", 
    apellidos: "Gómez", 
    dni: "53534545T",
    direccion: {
        calle: 'Me falta un tornillo, 12',
        cp: '35653',
        localidad: "Valladolid" // Error al no estar en el enum
    }})

db.pacientes.insert({
    nombre: "Raquel", 
    apellidos: "Gómez", 
    dni: "53534545T",
    direccion: {
        calle: 'Gran Vía',
        cp: '28300',
        localidad: "Alcorcón" // ok
    }})
```

```
db.pacientes.replaceOne( // Nuevo método que sustituye a save-update All credits to Carlos & partners
    {_id: ObjectId("6185717cb774d0e727f420fe")},
    {nombre: "Gonzalo", apellidos: "Pérez", dni: "523478587U"} // El documento que actualiza debe cumplir con el schema
)
```

Para comprobar la validación disponemos de

```
db.getCollectionInfos({name: "pacientes"}) // Ojo solo en la shell clásica
```



De entrada esta validación no comprueba los campos que no están en el esquema

```
db.pacientes.insert({nombre: "José", apellidos: "López", dni: "3535353T", genero: 'Prefiero no contestar'}) // ok
```