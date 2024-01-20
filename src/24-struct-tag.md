# Use tags de campo em structs serializadas

Qualquer campo de estrutura que seja serializado em JSON, YAML,
ou outros formatos que suportam a nomenclatura de campo baseada em tags
deve ser anotado com a tag relevante.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Stock struct {
  Price int
  Name  string
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td><td>

```go
type Stock struct {
  Price int    `json:"price"`
  Name  string `json:"name"`
  // Seguro renomear Name para Symbol.
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

Justificativa:
A forma serializada da estrutura é um contrato entre diferentes sistemas.
Alterações na estrutura da forma serializada - incluindo nomes de campos - quebram
esse contrato. Especificar nomes de campos dentro de tags torna o contrato explícito
e protege contra quebras acidentais do contrato por meio de refatoração ou
renomeação de campos.
