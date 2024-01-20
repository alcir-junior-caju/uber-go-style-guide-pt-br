# Evite `init()`

Evite `init()` sempre que possível. Quando `init()` é inevitável ou desejável, o código
deve tentar:

1. Ser completamente determinístico, independentemente do ambiente ou invocação do programa.
2. Evitar depender da ordem ou efeitos colaterais de outras funções `init()`.
   Embora a ordem das funções `init()` seja bem conhecida, o código pode mudar, e assim
   os relacionamentos entre as funções `init()` podem tornar o código frágil e
   propenso a erros.
3. Evitar acessar ou manipular o estado global ou de ambiente, como informações da máquina,
   variáveis de ambiente, diretório de trabalho, argumentos/entradas do programa, etc.
4. Evitar I/O, incluindo chamadas de sistema de arquivos, de rede e do sistema.

Código que não pode atender a esses requisitos provavelmente deve ser um ajudante
chamado como parte do `main()` (ou em outro lugar no ciclo de vida de um programa), ou
ser escrito como parte do próprio `main()`. Em particular, bibliotecas destinadas
a serem usadas por outros programas devem ter um cuidado especial para serem completamente
determinísticas e não realizar "mágica de init".

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}

var _defaultFoo Foo

func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}

// ou, melhor, para testabilidade:

var _defaultFoo = defaultFoo()

func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}

var _config Config

func init() {
    // Ruim: baseado no diretório atual
    cwd, _ := os.Getwd()

    // Ruim: I/O
    raw, _ := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )

    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}

func loadConfig() Config {
    cwd, err := os.Getwd()
    // lidar com err

    raw, err := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // lidar com err

    var config Config
    yaml.Unmarshal(raw, &config)

    return config
}
```

</td></tr>
</tbody></table>

Considerando o acima, algumas situações em que `init()` pode ser preferível ou
necessário incluem:

- Expressões complexas que não podem ser representadas como atribuições únicas.
- Hooks plugáveis, como dialetos `database/sql`, registros de tipos de codificação, etc.
- Otimizações para [Google Cloud Functions] e outras formas de pré-computação determinística.

  [Google Cloud Functions]: https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations
