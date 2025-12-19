# Aguarde as goroutines terminarem

Dada uma goroutine iniciada pelo sistema, deve haver uma maneira de aguardar a saída da goroutine. Existem duas maneiras populares de fazer isso:

- Use um `sync.WaitGroup`.
  Faça isso se houver várias goroutines das quais você deseja aguardar a conclusão:

    ```go
    var wg sync.WaitGroup
    for i := 0; i < N; i++ {
      wg.Go(...)
    }

    // Para aguardar todas finalizarem:
    wg.Wait()
    ```

- Adicione outro `chan struct{}` que a goroutine fecha quando termina.
  Faça isso se houver apenas uma goroutine:

    ```go
    done := make(chan struct{})
    go func() {
      defer close(done)
      // ...
    }()

    // Para aguardar a goroutine terminar:
    <-done
    ```
