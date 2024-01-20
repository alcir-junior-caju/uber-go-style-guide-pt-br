# Encerrar Apenas uma Vez

Se possível, prefira chamar `os.Exit` ou `log.Fatal` **no máximo uma vez** em seu
`main()`. Se houver vários cenários de erro que interrompem a execução do programa,
coloque essa lógica em uma função separada e retorne erros dela.

Isso tem o efeito de encurtar a função `main()` e colocar toda a lógica principal
de negócios em uma função separada e testável.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
package main

func main() {
  if err := executar(); err != nil {
    log.Fatal(err)
  }
}

func executar() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("arquivo ausente")
  }
  nome := args[0]

  f, err := os.Open(nome)
  if err != nil {
    return err
  }
  defer f.Close()

  // Se um erro ocorrer após esta linha,
  // f.Close ainda será chamado.

  b, err := io.ReadAll(f)
  if err != nil {
    return err
  }

  // ...

  return nil
}
```

</td><td>

```go
package main

func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}

func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("arquivo ausente")
  }
  nome := args[0]

  f, err := os.Open(nome)
  if err != nil {
    return err
  }
  defer f.Close()

  b, err := io.ReadAll(f)
  if err != nil {
    return err
  }

  // ...
}
```

</td></tr>
</tbody></table>

O exemplo acima utiliza `log.Fatal`, mas a orientação também se aplica a
`os.Exit` ou qualquer código de biblioteca que chame `os.Exit`.

```go
func main() {
  if err := run(); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

Você pode alterar a assinatura de `run()` de acordo com suas necessidades.
Por exemplo, se seu programa precisar sair com códigos específicos para falhas,
`run()` pode retornar o código de saída em vez de um erro.
Isso permite que testes unitários verifiquem esse comportamento diretamente.

```go
func main() {
  os.Exit(run(args))
}

func run() (exitCode int) {
  // ...
}
```

De forma mais geral, observe que a função `run()` usada nesses exemplos
não é destinada a ser prescritiva.
Há flexibilidade no nome, assinatura e configuração da função `run()`.
Entre outras coisas, você pode:

- aceitar argumentos de linha de comando não analisados (por exemplo, `run(os.Args[1:])`)
- analisar os argumentos da linha de comando em `main()` e passá-los para `run`
- usar um tipo de erro personalizado para transmitir o código de saída de volta para `main()`
- colocar a lógica de negócios em uma camada de abstração diferente de `package main`

Essa orientação apenas exige que haja um único local em seu `main()`
responsável por encerrar efetivamente o processo.
