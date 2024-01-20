# Não entre em Pânico

Código em produção deve evitar panics. Panics são uma fonte principal de [falhas em cascata]. Se ocorrer um erro, a função deve retornar um erro e permitir que o chamador decida como lidar com ele.

  [falhas em cascata]: https://en.wikipedia.org/wiki/Cascading_failure


<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("é necessário um argumento")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("é necessário um argumento")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

Panic/recover não é uma estratégia de tratamento de erros. Um programa deve entrar em pânico apenas quando algo irrecuperável acontece, como uma referência nula. Uma exceção a isso é a inicialização do programa: coisas ruins no início do programa que devem abortá-lo podem causar um panic.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

Mesmo nos testes, prefira `t.Fatal` ou `t.FailNow` em vez de panics para garantir que o teste seja marcado como falhado.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  panic("falha ao configurar o teste")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  t.Fatal("falha ao configurar o teste")
}
```

</td></tr>
</tbody></table>
