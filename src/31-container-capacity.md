# Prefira Especificar a Capacidade do Contêiner

Especifique a capacidade do contêiner sempre que possível para alocar memória
para o contêiner antecipadamente. Isso minimiza alocações subsequentes (por cópia e
redimensionamento do contêiner) à medida que elementos são adicionados.

## Especificando Dicas de Capacidade para Mapas

Sempre que possível, forneça dicas de capacidade ao inicializar
mapas com `make()`.

```go
make(map[T1]T2, hint)
```

Fornecer uma dica de capacidade para `make()` tenta ajustar o tamanho certo
do mapa no momento da inicialização, o que reduz a necessidade de aumentar
o mapa e as alocações à medida que os elementos são adicionados ao mapa.

Observe que, ao contrário de slices, dicas de capacidade para mapas não garantem uma alocação completa e preventiva,
mas são usadas para aproximar o número de buckets do hashmap
necessários. Consequentemente, alocações ainda podem ocorrer ao adicionar elementos ao
mapa, mesmo até a capacidade especificada.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := os.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := os.ReadDir("./files")

m := make(map[string]os.DirEntry, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

`m` é criado sem uma dica de tamanho; pode haver mais
alocações no momento da atribuição.

</td><td>

`m` é criado com uma dica de tamanho; pode haver menos
alocações no momento da atribuição.

</td></tr>
</tbody></table>

## Especificando a Capacidade de um Slice

Sempre que possível, forneça dicas de capacidade ao inicializar slices com `make()`,
especialmente ao usar a função `append()`.

```go
make([]T, length, capacity)
```

Ao contrário dos mapas, a capacidade de um slice não é uma dica: o compilador alocará memória suficiente para a capacidade do slice conforme fornecida para `make()`, o que significa que as operações `append()` subsequentes não terão alocações (até que o comprimento do slice corresponda à capacidade, após o qual qualquer `append()` adicional exigirá um redimensionamento para acomodar elementos adicionais).

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```plain
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```plain
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>
