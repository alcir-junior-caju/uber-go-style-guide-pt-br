# Receptores e Interfaces

Métodos com receptores de valor podem ser chamados em ponteiros, assim como em valores.
Métodos com receptores de ponteiro só podem ser chamados em ponteiros ou [valores endereçáveis].

  [valores endereçáveis]: https://golang.org/ref/spec#Method_values

Por exemplo,

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

// Não podemos obter ponteiros para valores armazenados em mapas, porque eles não são
// valores endereçáveis.
sVals := map[int]S{1: {"A"}}

// Podemos chamar o método Read em valores armazenados no mapa porque o método Read
// tem um receptor de valor, que não requer que o valor seja endereçável.
sVals[1].Read()

// Não podemos chamar o método Write em valores armazenados no mapa porque o método Write
// tem um receptor de ponteiro, e não é possível obter um ponteiro
// para um valor armazenado em um mapa.
//
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// Podemos chamar tanto Read quanto Write se o mapa armazenar ponteiros,
// porque os ponteiros são intrinsecamente endereçáveis.
sPtrs[1].Read()
sPtrs[1].Write("test")
```

De forma semelhante, uma interface pode ser satisfeita por um ponteiro, mesmo que o método tenha um
receptor de valor.

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// O seguinte não compila, já que s2Val é um valor, e não há um receptor de valor para f.
//   i = s2Val
```

O Effective Go tem uma boa explicação sobre [Ponteiros vs. Valores].

  [Ponteiros vs. Valores]: https://golang.org/doc/effective_go.html#pointers_vs_values
