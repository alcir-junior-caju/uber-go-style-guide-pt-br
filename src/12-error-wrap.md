# Envoltório de Erro

Existem três opções principais para propagar erros se uma chamada falhar:

- retornar o erro original como está
- adicionar contexto com `fmt.Errorf` e o verbo `%w`
- adicionar contexto com `fmt.Errorf` e o verbo `%v`

Retorne o erro original como está se não houver contexto adicional a ser adicionado.
Isso mantém o tipo e a mensagem de erro originais.
Isso é adequado para casos em que a mensagem de erro subjacente
tem informações suficientes para rastrear de onde veio.

Caso contrário, adicione contexto à mensagem de erro sempre que possível,
para que, em vez de um erro vago como "conexão recusada",
você obtenha erros mais úteis como "chamar o serviço foo: conexão recusada".

Use `fmt.Errorf` para adicionar contexto aos seus erros,
escolhendo entre os verbos `%w` ou `%v`
com base em se o chamador deve poder
corresponder e extrair a causa subjacente.

- Use `%w` se o chamador deve ter acesso ao erro subjacente.
  Este é um bom padrão para a maioria dos erros encapsulados,
  mas esteja ciente de que os chamadores podem começar a depender desse comportamento.
  Portanto, para casos em que o erro encapsulado é um `var` ou tipo conhecido,
  documente e teste-o como parte do contrato da sua função.
- Use `%v` para obscurecer o erro subjacente.
  Os chamadores não poderão correspondê-lo,
  mas você pode mudar para `%w` no futuro, se necessário.

Ao adicionar contexto a erros retornados, mantenha o contexto sucinto, evitando
frases como "falha ao", que afirmam o óbvio e se acumulam à medida que o erro
se propaga pela pilha:

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "ao criar novo armazenamento: %w", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "novo armazenamento: %w", err)
}
```

</td></tr><tr><td>

```plain
falha ao x: falha ao y: ao criar novo armazenamento: o erro
```

</td><td>

```plain
x: y: novo armazenamento: o erro
```

</td></tr>
</tbody></table>

Entretanto, assim que o erro é enviado para outro sistema, deve ficar claro que
a mensagem é um erro (por exemplo, uma etiqueta err ou um prefixo "Falha" nos logs).

Veja também [Não apenas verifique erros, manipule-os com elegância].

  [Não apenas verifique erros, manipule-os com elegância]: https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully
