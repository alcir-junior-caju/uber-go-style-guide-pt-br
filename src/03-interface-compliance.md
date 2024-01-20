# Verificar Conformidade de Interface

Verifique a conformidade de interface em tempo de compilação quando apropriado. Isso inclui:

- Tipos exportados que são obrigados a implementar interfaces específicas como parte do
  seu contrato de API
- Tipos exportados ou não exportados que fazem parte de uma coleção de tipos
  implementando a mesma interface
- Outros casos em que violar uma interface quebraria os usuários

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}



func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}

var _ http.Handler = (*Handler)(nil)

func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

A declaração `var _ http.Handler = (*Handler)(nil)` falhará na compilação se
`*Handler` deixar de corresponder à interface `http.Handler`.

O lado direito da atribuição deve ser o valor zero do tipo afirmado. Isso é `nil` para tipos de ponteiro (como `*Handler`), slices e maps, e
uma struct vazia para tipos de struct.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}

var _ http.Handler = LogHandler{}

func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```
