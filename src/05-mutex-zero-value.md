# Mutexes com Valor Zero são Válidos

O valor zero de `sync.Mutex` e `sync.RWMutex` é válido, então você quase
nunca precisa de um ponteiro para um mutex.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

Se você usar uma struct por ponteiro, então o mutex deve ser um campo não ponteiro
nele. Não aninhe o mutex na struct, mesmo que a struct não seja exportada.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type SMap struct {
  sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

<tr><td>

O campo `Mutex`, e os métodos `Lock` e `Unlock` fazem parte
involuntariamente da API exportada de `SMap`.

</td><td>

O mutex e seus métodos são detalhes de implementação de `SMap` ocultos de seus
chamadores.

</td></tr>
</tbody></table>
