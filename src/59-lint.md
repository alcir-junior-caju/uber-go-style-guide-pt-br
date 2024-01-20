# Linting

Mais importante do que qualquer conjunto "bendito" de linters é aplicar lint de forma consistente em todo o
código.

Recomendamos o uso dos seguintes linters no mínimo, porque acreditamos que eles
ajudam a detectar os problemas mais comuns e também estabelecem um padrão elevado para a qualidade do código sem serem desnecessariamente prescritivos:

- [errcheck] para garantir que os erros sejam tratados
- [goimports] para formatar o código e gerenciar os imports
- [golint] para apontar erros de estilo comuns
- [govet] para analisar o código em busca de erros comuns
- [staticcheck] para realizar várias verificações de análise estática

  [errcheck]: https://github.com/kisielk/errcheck
  [goimports]: https://godoc.org/golang.org/x/tools/cmd/goimports
  [golint]: https://github.com/golang/lint
  [govet]: https://golang.org/cmd/vet/
  [staticcheck]: https://staticcheck.io/

## Lint Runners

Recomendamos o [golangci-lint] como o principal executor de lint para código Go, principalmente devido
à sua performance em código-bases maiores e à capacidade de configurar e usar muitos
linters canônicos de uma vez. Este repositório possui um exemplo de arquivo de configuração [.golangci.yml] com linters e configurações recomendados.

golangci-lint possui [vários linters] disponíveis para uso. Os linters mencionados acima são
recomendados como um conjunto base, e encorajamos as equipes a adicionar qualquer linter adicional
que faça sentido para seus projetos.

  [golangci-lint]: https://github.com/golangci/golangci-lint
  [.golangci.yml]: https://github.com/uber-go/guide/blob/master/.golangci.yml
  [vários linters]: https://golangci-lint.run/usage/linters/
