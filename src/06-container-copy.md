# Copie Slices e Maps nos Limites

Slices e maps contêm ponteiros para os dados subjacentes, então tenha cuidado com cenários
em que eles precisam ser copiados.

## Recebendo Slices e Maps

Lembre-se de que os usuários podem modificar um mapa ou slice que você recebeu como argumento
se você armazenar uma referência a ele.

<table>
<thead><tr><th>Ruim</th> <th>Bom</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// Você quis dizer modificar d1.trips?
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// Agora podemos modificar trips[0] sem afetar d1.trips.
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

## Retornando Slices e Maps

Da mesma forma, tenha cuidado com as modificações dos usuários em maps ou slices que expõem o estado interno.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot retorna as estatísticas atuais.
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// snapshot não está mais protegido pelo mutex, então qualquer
// acesso ao snapshot está sujeito a corridas de dados.
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// Snapshot agora é uma cópia.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>


