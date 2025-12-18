# Nomeação de Erros

Para valores de erro armazenados como variáveis globais,
use o prefixo `Err` ou `err` dependendo se eles são exportados.
Esta orientação substitui a [Prefixo para Globais Não Exportadas com _](43-global-name.md).

```go
var (
  // Os dois erros a seguir são exportados
  // para que os usuários deste pacote possam correspondê-los
  // com errors.Is.

  ErrLinkQuebrado = errors.New("link quebrado")
  ErrNaoFoiPossivelAbrir = errors.New("não foi possível abrir")

  // Este erro não é exportado porque
  // não queremos torná-lo parte de nossa API pública.
  // Ainda podemos usá-lo dentro do pacote
  // com errors.Is.

  errNaoEncontrado = errors.New("não encontrado")
)
```

Para tipos de erro personalizados, use o sufixo `Error`.

```go
// Da mesma forma, este erro é exportado
// para que os usuários deste pacote possam correspondê-lo
// com errors.As.

type ErroNaoEncontrado struct {
  Arquivo string
}

func (e *ErroNaoEncontrado) Error() string {
  return fmt.Sprintf("arquivo %q não encontrado", e.Arquivo)
}

// E este erro não é exportado porque
// não queremos torná-lo parte da API pública.
// Ainda podemos usá-lo dentro do pacote
// com errors.As.

type erroResolucao struct {
  Caminho string
}

func (e *erroResolucao) Error() string {
  return fmt.Sprintf("resolver %q", e.Caminho)
}
```
