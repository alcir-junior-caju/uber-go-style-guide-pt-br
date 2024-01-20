# Tamanho do Canal é Um ou Nenhum

Os canais geralmente devem ter um tamanho de um ou serem não armazenados em buffer. Por padrão,
os canais não são armazenados em buffer e têm um tamanho de zero. Qualquer outro tamanho
deve ser sujeito a um alto nível de escrutínio. Considere como o tamanho é
determinado, o que impede que o canal seja preenchido sob carga e bloqueie
escritores, e o que acontece quando isso ocorre.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// Deve ser suficiente para qualquer pessoa!
c := make(chan int, 64)
```

</td><td>

```go
// Tamanho de um
c := make(chan int, 1) // ou
// Canal não armazenado em buffer, tamanho zero
c := make(chan int)
```

</td></tr>
</tbody></table>
