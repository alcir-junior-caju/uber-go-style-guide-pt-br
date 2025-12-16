# Lide com os Erros Apenas uma Vez

Quando um chamador recebe um erro de um chamado,
ele pode tratá-lo de várias maneiras diferentes
dependendo do que sabe sobre o erro.

Isso inclui, mas não está limitado a:

- se o contrato do chamado define erros específicos,
  correspondendo ao erro com `errors.Is` ou `errors.As`
  e tratando os ramos de forma diferente
- se o erro é recuperável,
  registrando o erro e degradando graciosamente
- se o erro representa uma condição de falha específica do domínio,
  retornando um erro bem definido
- retornando o erro, seja [encapsulado](12-error-wrap.md) ou de forma direta

Independentemente de como o chamador lida com o erro,
ele deve geralmente lidar com cada erro apenas uma vez.
O chamador não deve, por exemplo, registrar o erro e depois retorná-lo,
porque *seus* chamadores também podem lidar com o erro.

Por exemplo, considere os seguintes casos:

<table>
<thead><tr><th>Descrição</th><th>Codigo</th></tr></thead>
<tbody>
<tr><td>

**Ruim**: Registrar o erro e retorná-lo

Chamadores mais acima na pilha provavelmente tomarão uma ação semelhante com o erro.
Fazer isso gera muito ruído nos logs da aplicação com pouco benefício.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  // Ruim: Veja a descrição
  log.Printf("Não foi possível obter o usuário %q: %v", id, err)
  return err
}
```

</td></tr>
<tr><td>

**Bom**: Encapsule o erro e o retorne

Chamadores mais acima na pilha lidarão com o erro.
O uso de `%w` garante que eles possam corresponder o erro com `errors.Is` ou `errors.As`
se relevante.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  return fmt.Errorf("obter usuário %q: %w", id, err)
}
```

</td></tr>
<tr><td>

**Bom**: Registre o erro e degrade graciosamente

Se a operação não for estritamente necessária,
podemos fornecer uma experiência degradada, mas sem quebrar
ao nos recuperarmos dela.

</td><td>

```go
if err := emitMetrics(); err != nil {
  // Falha ao escrever métricas não deve
  // quebrar a aplicação.
  log.Printf("Não foi possível emitir métricas: %v", err)
}
```

</td></tr>
<tr><td>

**Bom**: Corresponda ao erro e degrade graciosamente

Se o chamado define um erro específico em seu contrato,
e a falha é recuperável,
corresponda a esse caso de erro e degradação graciosamente.
Para todos os outros casos, encapsule o erro e o retorne.

Chamadores mais acima na pilha lidarão com outros erros.

</td><td>

```go
tz, err := getUserTimeZone(id)
if err != nil {
  if errors.Is(err, ErrUsuarioNaoEncontrado) {
    // Usuário não existe. Use o UTC.
    tz = time.UTC
  } else {
    return fmt.Errorf("obter usuário %q: %w", id, err)
  }
}
```

</td></tr>
</tbody></table>
