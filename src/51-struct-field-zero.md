# Omitir Campos com Valor Zero em Estruturas

Ao inicializar estruturas com nomes de campos, omita os campos que têm valores zero
a menos que forneçam contexto significativo. Caso contrário, deixe que o Go defina esses valores automaticamente.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

Isso ajuda a reduzir o ruído para os leitores, omitindo valores que são padrão naquele contexto. Somente valores significativos são especificados.

Inclua valores zero onde os nomes dos campos fornecem contexto significativo. Por exemplo,
os casos de teste em [Tabelas de Teste](57-test-table.md) podem se beneficiar dos nomes dos campos
mesmo quando têm valor zero.

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```
