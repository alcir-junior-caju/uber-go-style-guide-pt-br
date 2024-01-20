# Introdução

Estilos são as convenções que governam nosso código. O termo estilo é um pouco enganoso, pois essas convenções abrangem muito mais do que apenas a formatação de arquivos de origem — gofmt cuida disso para nós.

O objetivo deste guia é gerenciar essa complexidade descrevendo detalhadamente os (O que fazer e o que não fazer) de escrever código Go na Uber. Essas regras existem para manter a base de código gerenciável, permitindo ainda que os engenheiros usem os recursos da linguagem Go de maneira produtiva.

Este guia foi originalmente criado por [Prashant Varanasi] e [Simon Newton] como uma maneira de atualizar alguns colegas sobre o uso do Go. Ao longo dos anos, ele foi modificado com base no feedback de outros.

  [Prashant Varanasi]: https://github.com/prashantv
  [Simon Newton]: https://github.com/nomis52

Este documento descreve convenções idiomáticas no código Go que seguimos na Uber. Muitas dessas são diretrizes gerais para Go, enquanto outras se baseiam em recursos externos:

1. [Guia Efetivo Go](https://golang.org/doc/effective_go.html)
2. [Erros Comuns em Go](https://github.com/golang/go/wiki/CommonMistakes)
3. [Comentários para Revisão de Código em Go](https://github.com/golang/go/wiki/CodeReviewComments)

Temos como objetivo que os exemplos de código sejam precisos para as duas versões menores mais recentes das [versões](https://go.dev/doc/devel/release) do Go.

Todo o código deve estar sem erros ao passar por `golint` e `go vet`. Recomendamos configurar seu editor para:

- Executar `goimports` ao salvar
- Executar `golint` e `go vet` para verificar erros

Você pode encontrar informações sobre o suporte do editor para ferramentas Go aqui:
<https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins>
