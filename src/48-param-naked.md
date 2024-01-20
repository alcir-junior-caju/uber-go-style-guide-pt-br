# Evite Parâmetros Desnecessários

Parâmetros nus em chamadas de função podem prejudicar a legibilidade. Adicione comentários no estilo C (`/* ... */`) para os nomes dos parâmetros quando o significado deles não for óbvio.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

Melhor ainda, substitua tipos `bool` nus por tipos personalizados para um código mais legível e seguro em relação ao tipo. Isso permite mais do que apenas dois estados (true/false) para esse parâmetro no futuro.

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status = iota + 1
  StatusDone
  // Talvez tenhamos um StatusInProgress no futuro.
)

func printInfo(name string, region Region, status Status)
```
