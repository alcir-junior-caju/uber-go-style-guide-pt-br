# Não dispare e esqueça goroutines

Goroutines são leves, mas não são gratuitas:
no mínimo, elas consomem memória para suas pilhas e CPU para serem agendadas.
Enquanto esses custos são pequenos para usos típicos de goroutines,
eles podem causar problemas de desempenho significativos
quando são criados em grande quantidade sem controle de tempo de vida.
Goroutines com tempos de vida não gerenciados também podem causar outros problemas
como impedir que objetos não utilizados sejam coletados pelo coletor de lixo
e reter recursos que não são mais utilizados.

Portanto, não vaze goroutines em código de produção.
Use [go.uber.org/goleak](https://pkg.go.dev/go.uber.org/goleak)
para testar vazamentos de goroutine dentro de pacotes que podem iniciar goroutines.

Em geral, cada goroutine:

- deve ter um momento previsível em que ela vai parar de ser executada; ou
- deve haver uma maneira de sinalizar para a goroutine que ela deve parar

Em ambos os casos, deve haver uma maneira para o código bloquear e aguardar a goroutine terminar.

Por exemplo:

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
go func() {
  for {
    flush()
    time.Sleep(delay)
  }
}()
```

</td><td>

```go
var (
  stop = make(chan struct{}) // informa a goroutine para parar
  done = make(chan struct{}) // nos informa que a goroutine saiu
)
go func() {
  defer close(done)

  ticker := time.NewTicker(delay)
  defer ticker.Stop()
  for {
    select {
    case <-ticker.C:
      flush()
    case <-stop:
      return
    }
  }
}()

// Em outro lugar...
close(stop)  // sinaliza para a goroutine parar
<-done       // e espera ela sair
```

</td></tr>
<tr><td>

Não há maneira de parar essa goroutine.
Ela continuará sendo executada até que a aplicação seja encerrada.

</td><td>

Esta goroutine pode ser interrompida com `close(stop)`,
e podemos aguardar sua saída com `<-done`.

</td></tr>
</tbody></table>
