# Use `"time"` para lidar com o tempo

O tempo é complicado. Suposições incorretas frequentemente feitas sobre o tempo incluem o seguinte.

1. Um dia tem 24 horas.
2. Uma hora tem 60 minutos.
3. Uma semana tem 7 dias.
4. Um ano tem 365 dias.
5. [E muitos outros](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

Por exemplo, *1* significa que adicionar 24 horas a um instante de tempo nem sempre resultará em um novo dia no calendário.

Portanto, sempre use o pacote [`"time"`] ao lidar com o tempo, pois ele ajuda a lidar com essas suposições incorretas de maneira mais segura e precisa.

[`"time"`]: https://golang.org/pkg/time/

## Use `time.Time` para instantes de tempo

Use [`time.Time`] ao lidar com instantes de tempo e os métodos em `time.Time` ao comparar, adicionar ou subtrair tempo.

[`time.Time`]: https://golang.org/pkg/time/#Time

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

## Use `time.Duration` para períodos de tempo

Use [`time.Duration`] ao lidar com períodos de tempo.

  [`time.Duration`]: https://golang.org/pkg/time/#Duration

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}

poll(10) // eram segundos ou milissegundos?
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}

poll(10*time.Second)
```

</td></tr>
</tbody></table>

Voltando ao exemplo de adicionar 24 horas a um instante de tempo, o método que
usamos para adicionar tempo depende da intenção. Se quisermos a mesma hora do
dia, mas no próximo dia do calendário, devemos usar [`Time.AddDate`]. No entanto,
se quisermos um instante de tempo garantido para ser 24 horas após o tempo
anterior, devemos usar [`Time.Add`].

  [`Time.AddDate`]: https://golang.org/pkg/time/#Time.AddDate
  [`Time.Add`]: https://golang.org/pkg/time/#Time.Add

```go
newDay := t.AddDate(0 /* anos */, 0 /* meses */, 1 /* dias */)
maybeNewDay := t.Add(24 * time.Hour)
```

## Use `time.Time` e `time.Duration` com sistemas externos

Use `time.Duration` e `time.Time` em interações com sistemas externos sempre que
possível. Por exemplo:

- Flags da linha de comando: [`flag`] suporta `time.Duration` via
  [`time.ParseDuration`]
- JSON: [`encoding/json`] suporta a codificação de `time.Time` como uma string [RFC 3339]
  através do seu método [`UnmarshalJSON`]
- SQL: [`database/sql`] suporta a conversão de colunas `DATETIME` ou `TIMESTAMP`
  para `time.Time` e vice-versa se o driver subjacente o suportar
- YAML: [`gopkg.in/yaml.v2`] suporta `time.Time` como uma string [RFC 3339], e
  `time.Duration` via [`time.ParseDuration`].

  [`flag`]: https://golang.org/pkg/flag/
  [`time.ParseDuration`]: https://golang.org/pkg/time/#ParseDuration
  [`encoding/json`]: https://golang.org/pkg/encoding/json/
  [RFC 3339]: https://tools.ietf.org/html/rfc3339
  [`UnmarshalJSON`]: https://golang.org/pkg/time/#Time.UnmarshalJSON
  [`database/sql`]: https://golang.org/pkg/database/sql/
  [`gopkg.in/yaml.v2`]: https://godoc.org/gopkg.in/yaml.v2

Quando não for possível usar `time.Duration` nessas interações, use
`int` ou `float64` e inclua a unidade no nome do campo.

Por exemplo, como `encoding/json` não suporta `time.Duration`, a unidade
é incluída no nome do campo.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

Quando não for possível usar `time.Time` nessas interações, a menos que uma
alternativa seja acordada, use `string` e formate os carimbos de data e hora conforme definido em
[RFC 3339]. Este formato é usado por padrão por [`Time.UnmarshalText`] e está
disponível para uso em `Time.Format` e `time.Parse` através de [`time.RFC3339`].

  [`Time.UnmarshalText`]: https://golang.org/pkg/time/#Time.UnmarshalText
  [`time.RFC3339`]: https://golang.org/pkg/time/#RFC3339

Embora isso geralmente não seja um problema na prática, tenha em mente que o
pacote `"time"` não suporta a análise de carimbos de data e hora com segundos bissextos
([8728]), nem considera segundos bissextos em cálculos ([15190]). Se
você comparar dois instantes de tempo, a diferença não incluirá os segundos bissextos
que possam ter ocorrido entre esses dois instantes.

  [8728]: https://github.com/golang/go/issues/8728
  [15190]: https://github.com/golang/go/issues/15190
