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