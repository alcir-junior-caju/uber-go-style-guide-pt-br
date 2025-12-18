# Prefixe Variáveis e Constantes Não Exportadas com _

Prefixe `var`s e `const`s não exportadas com `_` para deixar claro quando
elas são símbolos globais.

Justificativa: Variáveis e constantes de nível superior têm escopo de pacote.
Usar um nome genérico facilita o uso acidental do valor errado em um arquivo
diferente.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // Não veremos um erro de compilação se a primeira linha de
  // Bar() for excluída.
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

**Exceção**: Valores de erro não exportados podem usar o prefixo `err` sem o sublinhado.
Consulte [Nomeação de Erros](13-error-name.md).
