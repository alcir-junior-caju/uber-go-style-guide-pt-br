# Declarações Locais de Variáveis

Declarações curtas de variáveis (`:=`) devem ser utilizadas se uma variável estiver sendo atribuída a algum valor explicitamente.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

No entanto, há casos em que o valor padrão é mais claro quando a palavra-chave `var` é utilizada. Por exemplo, [Declarando Fatias Vazias].

  [Declarando Fatias Vazias]: https://github.com/golang/go/wiki/CodeReviewComments#declaring-empty-slices

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>
