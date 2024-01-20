# Inicializando Referências de Estruturas

Use `&T{}` em vez de `new(T)` ao inicializar referências de estruturas para que
seja consistente com a inicialização da estrutura.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// inconsistent
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>
