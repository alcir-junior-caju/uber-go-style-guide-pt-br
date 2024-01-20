# Evite Usar Nomes Embutidos

A [especificação da linguagem Go] delineia vários identificadores embutidos,
[chamados predefinidos] que não devem ser usados como nomes em programas Go.

Dependendo do contexto, reutilizar esses identificadores como nomes irá sombrear
o original dentro do escopo léxico atual (e quaisquer escopos aninhados) ou tornar
o código afetado confuso. No melhor dos casos, o compilador emitirá um aviso; no
pior dos casos, tal código pode introduzir bugs latentes difíceis de serem identificados.

  [especificação da linguagem Go]: https://golang.org/ref/spec
  [chamados predefinidos]: https://golang.org/ref/spec#Predeclared_identifiers


<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
var erro string
// `erro` sombreia o embutido

// ou

func lidarComMensagemDeErro(erro string) {
    // `erro` sombreia o embutido
}
```

</td><td>

```go
var mensagemDeErro string
// `erro` se refere ao embutido

// ou

func lidarComMensagemDeErro(msg string) {
    // `erro` se refere ao embutido
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // Embora esses campos tecnicamente não
    // constituam sombreamento, buscar por
    // strings `error` ou `string` agora é
    // ambíguo.
    erro   error
    cadeia string
}

func (f Foo) Error() error {
    // `erro` e `f.erro` são
    // visualmente semelhantes
    return f.erro
}

func (f Foo) String() string {
    // `cadeia` e `f.cadeia` são
    // visualmente semelhantes
    return f.cadeia
}

```

</td><td>

```go
type Foo struct {
    // As strings `erro` e `cadeia` agora são
    // inequívocas.
    erro   error
    cadeia string
}

func (f Foo) Error() error {
    return f.erro
}

func (f Foo) String() string {
    return f.cadeia
}

```

</td></tr>
</tbody></table>

Observe que o compilador não gerará erros ao usar identificadores predefinidos, mas ferramentas como `go vet` devem apontar corretamente esses casos e outros de sombreamento.
