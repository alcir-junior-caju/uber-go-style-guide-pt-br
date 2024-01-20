# Nomeando Funções no Estilo Printf

Ao declarar uma função no estilo `Printf`, certifique-se de que o `go vet` pode detectá-la e verificar a string de formato.

Isso significa que você deve usar nomes de função pré-definidos no estilo `Printf`, se possível. O `go vet` verificará esses por padrão. Veja [Printf family] para mais informações.

  [Printf family]: https://golang.org/cmd/vet/#hdr-Printf_family

Se usar os nomes pré-definidos não for uma opção, termine o nome que você escolher com f: `Wrapf`, não `Wrap`. O `go vet` pode ser configurado para verificar nomes específicos no estilo `Printf`, mas eles devem terminar com f.

```shell
go vet -printfuncs=wrapf,statusf
```

Veja também [go vet: Printf family check].

  [go vet: Printf family check]: https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/
