# Declarações de Variáveis no Nível Superior

No nível superior, use a palavra-chave padrão `var`. Não especifique o tipo, a menos que não seja o mesmo tipo que o da expressão.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// Já que F já declara que retorna uma string, não precisamos especificar o tipo novamente.

func F() string { return "A" }
```
</td></tr>
</tbody></table>

Especifique o tipo se o tipo da expressão não corresponder exatamente ao tipo desejado.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F retorna um objeto do tipo myError, mas queremos um error.
```
