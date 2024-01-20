# Incorporação em Estruturas

Tipos incorporados devem estar no topo da lista de campos de uma
estrutura, e deve haver uma linha em branco separando campos incorporados de campos regulares.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

A incorporação deve fornecer benefícios tangíveis, como adicionar ou aprimorar
funcionalidade de maneira semanticamente apropriada. Deve fazer isso sem ter
efeitos adversos visíveis para o usuário (veja também: [Evite Incorporar Tipos em Estruturas Públicas]).

Exceção: Mutexes não devem ser incorporados, mesmo em tipos não exportados. Veja também: [Mutexes com Valor Zero são Válidos].

  [Evite Incorporar Tipos em Estruturas Públicas]: #evite-incorporar-tipos-em-estruturas-públicas
  [Mutexes com Valor Zero são Válidos]: #mutexes-com-valor-zero-são-válidos

A incorporação **não deve**:

- Ser puramente estética ou orientada à conveniência.
- Dificultar a construção ou utilização dos tipos externos.
- Afetar os valores zero dos tipos externos. Se o tipo externo tiver um valor zero útil, ele
  ainda deve ter um valor zero útil após a incorporação do tipo interno.
- Expor funções ou campos não relacionados do tipo externo como um efeito colateral
  da incorporação do tipo interno.
- Expor tipos não exportados.
- Afetar a semântica de cópia dos tipos externos.
- Alterar a API ou semântica de tipo do tipo externo.
- Incorporar uma forma não canônica do tipo interno.
- Expor detalhes de implementação do tipo externo.
- Permitir que os usuários observem ou controlem detalhes internos do tipo.
- Alterar o comportamento geral das funções internas através do encapsulamento de uma maneira que
  razoavelmente surpreenderia os usuários.

Simplesmente coloque, incorpore consciente e intencionalmente. Um bom teste é, "todos esses métodos/campos internos exportados seriam adicionados diretamente ao tipo externo?";
se a resposta for "alguns" ou "não", não incorpore o tipo interno - use um campo
em vez disso.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // Ruim: A.Lock() e A.Unlock() estão
    //      agora disponíveis, não fornecem
    //      benefício funcional e permitem
    //      que os usuários controlem detalhes
    //      sobre os internos de A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // Bom: Write() é fornecido nesta camada
    //      externa para um propósito específico
    //      e delega o trabalho para Write() do tipo interno.
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
  // Ruim: a mudança do ponteiro afeta a utilidade do valor zero
  io.ReadWriter

  // outros campos
}

// depois

var b Book
b.Read(...)  // pânico: ponteiro nulo
b.String()   // pânico: ponteiro nulo
b.Write(...) // pânico: ponteiro nulo
```

</td><td>

```go
type Book struct {
  // Bom: tem um valor zero útil
  bytes.Buffer

  // outros campos
}

// depois

var b Book
b.Read(...)  // ok
b.String()   // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>
