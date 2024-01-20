# Sem goroutines em `init()`

Funções `init()` não devem criar goroutines.
Veja também [Evite init()](init.md).

Se um pacote precisa de uma goroutine em segundo plano,
ele deve expor um objeto responsável por gerenciar a vida útil da goroutine.
O objeto deve fornecer um método (`Close`, `Stop`, `Shutdown`, etc)
que sinalize para a goroutine de segundo plano parar e espere ela encerrar.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func init() {
  go doWork()
}

func doWork() {
  for {
    // ...
  }
}
```

</td><td>

```go
type Worker struct{ /* ... */ }

func NewWorker(...) *Worker {
  w := &Worker{
    stop: make(chan struct{}),
    done: make(chan struct{}),
    // ...
  }
  go w.doWork()
}

func (w *Worker) doWork() {
  defer close(w.done)
  for {
    // ...
    case <-w.stop:
      return
  }
}

// Shutdown diz ao worker para parar
// e espera até que tenha terminado.
func (w *Worker) Shutdown() {
  close(w.stop)
  <-w.done
}
```

</td></tr>
<tr><td>

Cria uma goroutine em segundo plano incondicionalmente quando o usuário exporta este pacote.
O usuário não tem controle sobre a goroutine nem um meio de interrompê-la.

</td><td>

Cria o worker apenas se o usuário solicitar.
Fornece um meio de desligar o worker para que o usuário possa liberar
recursos usados pelo worker.

Observe que você deve usar `WaitGroup`s se o worker gerenciar várias
goroutines.
Veja [Espere as goroutines terminarem](goroutine-exit.md).

</td></tr>
</tbody></table>
