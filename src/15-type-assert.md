# Lidar com Falhas na Asserção de Tipo

A forma de retorno único de uma [asserção de tipo] gerará um pânico em um tipo incorreto.
Portanto, sempre utilize o "idioma do 'comma ok'" (vírgula ok).

  [asserção de tipo]: https://golang.org/ref/spec#Type_assertions

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // lidar com o erro de forma adequada
}
```

</td></tr>
</tbody></table>

<!-- TODO: Existem algumas situações em que a forma de atribuição única é
aceitável. -->
