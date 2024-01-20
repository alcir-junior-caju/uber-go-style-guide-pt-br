# Opções Funcionais

Opções funcionais são um padrão no qual você declara um tipo opaco `Option`
que registra informações em alguma struct interna. Você aceita um número
variável dessas opções e age sobre as informações completas registradas pelas
opções na struct interna.

Use esse padrão para argumentos opcionais em construtores e outras APIs
públicas que você prevê precisar expandir, especialmente se você já tiver
três ou mais argumentos nessas funções.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// package db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// package db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

O parâmetro de cache e o de logger devem sempre ser fornecidos, mesmo que o usuário queira usar os valores padrão.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

Opções são fornecidas apenas se necessárias.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

A maneira sugerida de implementar esse padrão é com uma interface `Option`
que possui um método não exportado, registrando opções em uma struct não
exportada chamada `options`.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open cria uma conexão.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

Observe que há uma maneira de implementar esse padrão com closures, mas acreditamos que o padrão acima oferece mais flexibilidade para os autores e é mais fácil de depurar e testar para os usuários. Em particular, ele permite que as opções sejam comparadas entre si em testes e mocks, ao contrário das closures, onde isso é impossível. Além disso, permite que as opções implementem outras interfaces, incluindo `fmt.Stringer`, o que permite representações de string legíveis para o usuário das opções.

Veja também:

- [Funções auto-referenciais e o design de opções]
- [Opções funcionais para APIs amigáveis]

  [Funções auto-referenciais e o design de opções]: https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html
  [Opções funcionais para APIs amigáveis]: https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis

<!-- TODO: substituir isso por structs de parâmetros e opções funcionais, quando usar um em vez do outro -->
