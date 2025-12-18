# Use Raw String Literals Para Evitar Sequências de Escape

Go suporta [literais de string "raw"](https://golang.org/ref/spec#raw_string_lit),
que podem abranger várias linhas e incluir aspas. Use-as para evitar
strings com caracteres escapados manualmente, que são muito mais difíceis de ler.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>
