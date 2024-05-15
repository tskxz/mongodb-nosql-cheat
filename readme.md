# Entrar no mongoshell
```
monghosh
```

# Criar uma base de dados
```
use stand
```

# Criação da coleção
```
db.createCollection("carros")
```

# Inserir documento
```
db.carros.insertOne({
    marca: "Mitsubishi",
    linha_producao: "Lancer Evolution",
    modelo: 9,
    cv: 283
})
```

## Inserir múltiplos documentos
```
db.carros.insertMany([
    {
        marca: "Ferrari",
        linha_producal: 812,
        modelo: "Superfast",
        cv: 800
    },
    {
        marca: "Nissan",
        linha_producao: "Skyline",
        modelo: "R34",
        cv: 280
    },
])
```

# Encontrar um documento
```
db.carros.find({marca: "Ferrari"})
```

## Projeção do documento
### Mostrar apenas a marca e modelo
```
db.carros.find({}, {marca: 1, modelo: 1})
```

### Excluir um campo e mostrar campos específicos
```
db.carros.find({}, _id: 0, marca: 1, modelo: 1)
```
### Mostrar todos os campos mas excluir um campo específico
```
db.carros.find({}, {cv: 0})
```
# Atualizar um documento
Supostamente o seguinte cv é 280.
```
db.carros.find({marca: "Nissan"})
```
Atualiza-se para 285
```
db.carros.updateOne({marca: "Nissan"}, {$set: {cv: 285}})
```

## Atualizar ou inserir um documento se não existir
```
db.carros.updateOne(
    {marca: "Dodge"},
    {
        $set: {
            marca: "Dodge",
            linha_producao: "Charger",
            modelo: "SRT8",
            cv: 807
        }
    },
    {upsert: true}
)
```

## Atualizar múltiplos documentos
Aproveitando, adicionou-se um novo campo visualizacoes que incrementa o valor +1
db.carros.updateMany({}, {$inc: {visualizacoes: 1}})

# Apagar um documento
```
db.carros.deleteOne({marca: "Dodge"})
```

## Apagar múltiplos documentos
```
Apaga os carros com o cv 285
db.carros.deleteMany({cv: 285})
```

# Agregações
## Agrupar documentos
Mostra apenas os documentos que têm mais que uma visualização e agrupoa os documentos pela marca e soma cada marca as visualizações
```
db.carros.aggregate([
    {
        $match: {visualizacoes: {$gt: 1}}
    },

    {
        $group: {
            _id: "$marca",
            totalVisualizacoes: {$sum: "$visualizacoes"}
        }
    },
])
```

## Agrupa os documentos com o id único
```
db.carros.aggregate(
    [
        {
            $group: {_id: "$marca"}
        }
    ]
)
```

## Limitar o número de documentos passados
```
db.carros.aggregate(
    [
        {
            $limit: 1
        }
    ]
)
```

## Documentos com campos específicos com limite
```
db.carros.aggregate([
    {
        $project: {
            "marca": 1,
            "modelo": 1
        }
    },

    {
        $limit: 1
    }
])
```

## Ordenar por cada documento
Ordena por ordem decrescente com limite 4
```
db.carros.aggregate([
    {
        $sort: {"cv": -1}
    },

    {
        $project: {
            "marca": 1,
            "modelo": 1,
            "cv": 1
        }
    },
    {
        $limit: 4
    }
])
```

## Correspondência de documentos
Documentos que correspondem com um campo
```
db.carros.aggregate([
    {
        $match: {marca: "Ferrari"}
    },
    {
        $limit: 2
    },
    {
        $project: {
        "marca": 1,
        "modelo": 1,
        }
    }
])
```

## Adicionar um campo novo num documento
```
db.carros.aggregate([
    {
        $addFields: {
            "tracao": "awd"
        }
    },
    {
        $project: {
            "marca": 1,
            "modelo": 1,
            "tracao": 1
        }
    },
    {
        $limit: 5
    }
])
```

## Contagem de documentos
```
db.carros.aggregate([
    {
        $match: {"marca":"Ferrari"}
    },
    {
        $count: "totalFerraris"
    }
])
```

## Lookup - Juntar documentos
Criação de uma nova coleção e inserir um comentário de um carro
```
db.createCollection("comentarios")
db.comentarios.insertOne(
  {
    "_id": ObjectId("663a54904cd9045b54ef6350"),
    "carro_id": ObjectId("663a4be84cd9045b54ef634b"),
    "comentario": "Este carro é incrível!"

  }
)
```

```
db.comentarios.aggregate([
  {
    $lookup: {
    from: "carros",
    localField: "carro_id",
    foreignField: "_id",
    as: "carro_detalhes",
    },
  },
  {
    $limit: 1
  }
])
```

## Validação
```
db.createCollection("motas", {
  validator: {
    $jsonSchema: {
    bsonType: "object",
    required: [ "marca", "modelo", "cilindrada" ],
    properties: {
        marca: {
        bsonType: "string",
        description: "Marca da moto - Obrigatório."
        },
        modelo: {
        bsonType: "string",
        description: "Modelo da moto - Obrigatório."
        },
        cilindrada: {
        bsonType: "int",
        description: "Cilindrada da moto (em cc) - Obrigatório."
        },
        cor: {
        bsonType: "string",
        description: "Cor da moto - Opcional."
        },
        preco: {
        bsonType: "decimal",
        description: "Preço da moto - Opcional."
        }
    }
    }
  }
})
```