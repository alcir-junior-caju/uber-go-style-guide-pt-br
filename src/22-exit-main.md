# Sair no Main

Programas Go utilizam [`os.Exit`] ou [`log.Fatal*`] para sair imediatamente. (Entrar em pânico
não é uma boa maneira de encerrar programas, por favor [não entre em pânico](16-panic.md).)

  [`os.Exit`]: https://golang.org/pkg/os/#Exit
  [`log.Fatal*`]: https://golang.org/pkg/log/#Fatal

Chame um dos `os.Exit` ou `log.Fatal*` **apenas em `main()`**. Todas as outras
funções devem retornar erros para sinalizar falha.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}

func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }

  b, err := io.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}

func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }

  b, err := io.ReadAll(f)
  if err != nil {
    return "", err
  }

  return string(b), nil
}
```

</td></tr>
</tbody></table>

Justificativa: Programas com várias funções que encerram apresentam alguns problemas:

- Fluxo de controle não óbvio: Qualquer função pode encerrar o programa, tornando
  difícil compreender o fluxo de controle.
- Difícil de testar: Uma função que encerra o programa também encerrará o teste
  que a chama. Isso torna a função difícil de testar e introduz o risco de
  pular outros testes que ainda não foram executados pelo `go test`.
- Falta de limpeza: Quando uma função encerra o programa, ela pula as chamadas
  de função enfileiradas com instruções `defer`. Isso aumenta o risco de pular tarefas importantes de limpeza.
