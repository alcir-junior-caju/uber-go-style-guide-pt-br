# Inicie Enums em Um

A maneira padrão de introduzir enumerações em Go é declarar um tipo personalizado
e um grupo `const` com `iota`. Como as variáveis têm um valor padrão de 0, você
normalmente deve começar suas enums com um valor não nulo.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

Existem casos em que usar o valor zero faz sentido, por exemplo, quando o caso de valor zero é o comportamento padrão desejado.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

<!-- TODO: section on String methods for enums -->
