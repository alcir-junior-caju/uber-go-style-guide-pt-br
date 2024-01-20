# Agrupamento e Ordenação de Funções

- As funções devem ser ordenadas de acordo com a ordem de chamada aproximada.
- As funções em um arquivo devem ser agrupadas pelo receptor.

Portanto, as funções exportadas devem aparecer primeiro em um arquivo, após
definições de `struct`, `const`, `var`.

Um `newXYZ()`/`NewXYZ()` pode aparecer após o tipo ser definido, mas antes do
resto dos métodos no receptor.

Como as funções são agrupadas pelo receptor, funções utilitárias simples devem
aparecer no final do arquivo.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>
