# Evite Incorporar Tipos em Structs Públicas

Esses tipos incorporados vazam detalhes de implementação, inibem a evolução do tipo e
obscurecem a documentação.

Supondo que você tenha implementado uma variedade de tipos de listas usando uma
`AbstractList` compartilhada, evite incorporar a `AbstractList` em suas implementações concretas de lista.
Em vez disso, escreva manualmente apenas os métodos para sua lista concreta que delegarão
para a lista abstrata.

```go
type AbstractList struct {}

// Add adiciona uma entidade à lista.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove remove uma entidade da lista.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList é uma lista de entidades.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList é uma lista de entidades.
type ConcreteList struct {
  list *AbstractList
}

// Add adiciona uma entidade à lista.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove remove uma entidade da lista.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go permite [incorporação de tipos] como um compromisso entre herança e composição.
O tipo externo obtém cópias implícitas dos métodos do tipo incorporado.
Esses métodos, por padrão, delegam para o mesmo método da instância incorporada.

  [incorporação de tipos]: https://golang.org/doc/effective_go.html#embedding

A struct também ganha um campo com o mesmo nome do tipo.
Portanto, se o tipo incorporado for público, o campo também é público.
Para manter a compatibilidade com versões anteriores, cada versão futura do tipo externo deve
manter o tipo incorporado.

Um tipo incorporado raramente é necessário.
É uma conveniência que ajuda a evitar a escrita de métodos delegados tediosos.

Mesmo incorporar uma interface *AbstractList* compatível, em vez da struct,
ofereceria ao desenvolvedor mais flexibilidade para mudanças no futuro, mas ainda
vazaria o detalhe de que as listas concretas usam uma implementação abstrata.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList é uma implementação generalizada
// para vários tipos de listas de entidades.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList é uma lista de entidades.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList é uma lista de entidades.
type ConcreteList struct {
  list AbstractList
}

// Add adiciona uma entidade à lista.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes uma entidade da lista.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Quer seja com uma struct incorporada ou uma interface incorporada, o tipo incorporado
impõe limites à evolução do tipo.

- Adicionar métodos a uma interface incorporada é uma mudança que quebra a compatibilidade.
- Remover métodos de uma struct incorporada é uma mudança que quebra a compatibilidade.
- Remover o tipo incorporado é uma mudança que quebra a compatibilidade.
- Substituir o tipo incorporado, mesmo por um alternativo que satisfaça a mesma
  interface, é uma mudança que quebra a compatibilidade.

Embora escrever esses métodos delegados seja tedioso, o esforço adicional esconde
um detalhe de implementação, deixa mais oportunidades para mudanças e também
elimina a indireção para descobrir a interface completa da Lista na
documentação.
