# Use go.uber.org/atomic

Operações atômicas com o pacote [sync/atomic] operam nos tipos brutos
(`int32`, `int64`, etc.), então é fácil esquecer de usar a operação atômica para
ler ou modificar as variáveis.

[go.uber.org/atomic] adiciona segurança de tipo a essas operações, ocultando o
tipo subjacente. Além disso, inclui um tipo conveniente `atomic.Bool`.

  [go.uber.org/atomic]: https://godoc.org/go.uber.org/atomic
  [sync/atomic]: https://golang.org/pkg/sync/atomic/

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>


```go
type foo struct {
  running int32  // atômico
}

func (f *foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // já está em execução...
     return
  }
  // iniciar o Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // corrida!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // já está em execução...
     return
  }
  // iniciar o Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>
