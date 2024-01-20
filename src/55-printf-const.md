# Formate Strings fora do Printf

Se você declarar strings de formato para funções estilo `Printf` fora de um literal de string, faça delas valores `const`.

Isso ajuda o `go vet` a realizar uma análise estática da string de formato.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "valores inesperados %v, %v\n"
fmt.Printf(msg, 1, 2)

```

</td><td>

```go
const msg = "valores inesperados %v, %v\n"
fmt.Printf(msg, 1, 2)

```

</td></tr>
</tbody></table>
