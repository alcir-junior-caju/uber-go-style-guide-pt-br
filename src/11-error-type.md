# Tipos de Erro

Existem poucas opções para declarar erros.
Considere o seguinte antes de escolher a opção mais adequada para o seu caso de uso.

- O chamador precisa corresponder ao erro para que ele possa manipulá-lo?
  Se sim, devemos suportar as funções [`errors.Is`] ou [`errors.As`]
  ao declarar uma variável de erro no nível superior ou um tipo de erro personalizado.
- A mensagem de erro é uma string estática
  ou é uma string dinâmica que requer informações contextuais?
  Para o primeiro caso, podemos usar [`errors.New`], mas para o segundo devemos
  usar [`fmt.Errorf`] ou um tipo de erro personalizado.
- Estamos propagando um novo erro retornado por uma função downstream?
  Se sim, consulte a [seção sobre encapsulamento de erros](error-wrap.md).

[`errors.Is`]: https://golang.org/pkg/errors/#Is
[`errors.As`]: https://golang.org/pkg/errors/#As

| Corresponder ao Erro? | Mensagem de Erro | Orientação                            |
|-----------------------|------------------|---------------------------------------|
| Não                   | estática        | [`errors.New`]                        |
| Não                   | dinâmica        | [`fmt.Errorf`]                        |
| Sim                   | estática        | `var` no nível superior com [`errors.New`] |
| Sim                   | dinâmica        | tipo de erro personalizado                 |

[`errors.New`]: https://golang.org/pkg/errors/#New
[`fmt.Errorf`]: https://golang.org/pkg/fmt/#Errorf

Por exemplo,
use [`errors.New`] para um erro com uma string estática.
Exporte este erro como uma variável para suportar a correspondência com `errors.Is`
se o chamador precisar corresponder e manipular este erro.


<table>
<thead><tr><th>Sem correspondência de erro</th><th>Correspondência de erro</th></tr></thead>
<tbody>
<tr><td>

```go
// pacote foo

func Abrir() error {
  return errors.New("não foi possível abrir")
}

// pacote bar

if err := foo.Abrir(); err != nil {
  // Não é possível manipular o erro.
  panic("erro desconhecido")
}
```

</td><td>

```go
// pacote foo

var ErrNaoFoiPossivelAbrir = errors.New("não foi possível abrir")

func Abrir() error {
  return ErrNaoFoiPossivelAbrir
}

// pacote bar

if err := foo.Abrir(); err != nil {
  if errors.Is(err, foo.ErrNaoFoiPossivelAbrir) {
    // manipular o erro
  } else {
    panic("erro desconhecido")
  }
}
```

</td></tr>
</tbody></table>

Para um erro com uma string dinâmica,
use [`fmt.Errorf`] se o chamador não precisar corresponder a ele,
e um tipo de `error` personalizado se o chamador precisar corresponder a ele.

<table>
<thead><tr><th>Sem correspondência de erro</th><th>Correspondência de erro</th></tr></thead>
<tbody>
<tr><td>

```go
// pacote foo

func Abrir(arquivo string) error {
  return fmt.Errorf("arquivo %q não encontrado", arquivo)
}

// pacote bar

if err := foo.Abrir("arquivoteste.txt"); err != nil {
  // Não é possível manipular o erro.
  panic("erro desconhecido")
}
```

</td><td>

```go
// pacote foo

type ErroNaoEncontrado struct {
  Arquivo string
}

func (e *ErroNaoEncontrado) Error() string {
  return fmt.Sprintf("arquivo %q não encontrado", e.Arquivo)
}

func Abrir(arquivo string) error {
  return &ErroNaoEncontrado{Arquivo: arquivo}
}

// pacote bar

if err := foo.Abrir("arquivoteste.txt"); err != nil {
  var naoEncontrado *foo.ErroNaoEncontrado
  if errors.As(err, &naoEncontrado) {
    // Manipular o erro
  } else {
    panic("erro desconhecido")
  }
}
```

</td></tr>
</tbody></table>

Observe que se você exportar variáveis de erro ou tipos de um pacote,
eles farão parte da API pública do pacote.
