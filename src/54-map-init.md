# Inicializando Mapas

Prefira `make(..)` para mapas vazios e mapas populados
programaticamente. Isso torna a inicialização do mapa visualmente
diferente da declaração, e facilita a adição de dicas de tamanho
mais tarde, se disponíveis.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 é seguro para leitura e escrita;
  // m2 causará um pânico em operações de escrita.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 é seguro para leitura e escrita;
  // m2 causará um pânico em operações de escrita.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

Declaração e inicialização são visualmente semelhantes.

</td><td>

Declaração e inicialização são visualmente distintas.

</td></tr>
</tbody></table>

Sempre que possível, forneça dicas de capacidade ao inicializar
mapas com `make()`. Veja
[Especificando Dicas de Capacidade para Mapas](31-container-capacity.md#specifying-map-capacity-hints)
para mais informações.

Por outro lado, se o mapa contiver um conjunto fixo de elementos,
use literais de mapas para inicializá-lo.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

A regra básica é usar literais de mapas ao adicionar um conjunto fixo de
elementos durante a inicialização; caso contrário, use `make` (e especifique uma dica de tamanho
se disponível).
