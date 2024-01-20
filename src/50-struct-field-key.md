# Use Nomes de Campos para Inicializar Estruturas

Você deve quase sempre especificar os nomes dos campos ao inicializar estruturas. Isso
agora é aplicado por [`go vet`].

  [`go vet`]: https://golang.org/cmd/vet/

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

Exceção: Os nomes dos campos *podem* ser omitidos em tabelas de teste quando houver 3 ou
menos campos.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```
