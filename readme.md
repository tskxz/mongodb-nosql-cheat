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
