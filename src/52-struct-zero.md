# Use `var` para Estruturas com Valor Zero

Quando todos os campos de uma estrutura são omitidos em uma declaração, use a forma `var`
para declarar a estrutura.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{}
```

</td><td>

```go
var user User
```

</td></tr>
</tbody></table>

Isso diferencia estruturas com valores zero daquelas com campos não nulos
de maneira semelhante à distinção criada para [inicialização de mapas](54-map-init.md), e coincide com a forma
como preferimos [declarar slices vazias].

  [declarar slices vazias]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices
