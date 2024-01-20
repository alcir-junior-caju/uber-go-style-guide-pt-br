# Tabelas de Teste

Testes baseados em tabelas com [subtestes] podem ser um padrão útil para escrever testes
e evitar a duplicação de código quando a lógica central do teste é repetitiva.

Se um sistema em teste precisa ser testado contra _múltiplas condições_ em que
partes específicas das entradas e saídas mudam, um teste baseado em tabela deve
ser usado para reduzir a redundância e melhorar a legibilidade.

  [subtestes]: https://blog.golang.org/subtests

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

Tabelas de teste facilitam a adição de contexto às mensagens de erro, reduzem a lógica duplicada
e permitem a adição de novos casos de teste.

Seguimos a convenção de que a fatia de structs é referida como `tests`
e cada caso de teste como `tt`. Além disso, incentivamos a explicitação das entradas e saídas
para cada caso de teste com os prefixos `give` e `want`.

```go
tests := []struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

## Evite Complexidade Desnecessária em Testes de Tabela

Testes de tabela podem ser difíceis de ler e manter se os subtestes contiverem afirmações condicionais
ou outra lógica de ramificação. Testes de tabela **NÃO** devem ser usados sempre que
houver lógica complexa ou condicional dentro dos subtestes (ou seja, lógica complexa dentro do loop `for`).

Testes de tabela grandes e complexos prejudicam a legibilidade e a manutenção, pois os leitores dos testes podem
ter dificuldade em depurar falhas que ocorrem.

Testes de tabela como esse devem ser divididos em várias tabelas de teste ou várias
funções individuais `Test...`.

Alguns ideais a serem buscados são:

- Focar na unidade mais estreita de comportamento.
- Minimizar a "profundidade do teste" e evitar afirmações condicionais (veja abaixo).
- Garantir que todos os campos da tabela sejam usados em todos os testes.
- Garantir que toda a lógica de teste seja executada para todos os casos da tabela.

Nesse contexto, "profundidade do teste" significa "dentro de um teste dado, o número de
afirmações sucessivas que requerem que as afirmações anteriores sejam verdadeiras" (semelhante
à complexidade ciclomática).
Ter testes "mais rasos" significa que há menos relacionamentos entre
afirmações e, mais importante, que essas afirmações são menos propensas
a serem condicionais por padrão.

Concretamente, os testes de tabela podem se tornar confusos e difíceis de ler se usarem várias ramificações
(e.g., `shouldError`, `expectCall`, etc.), usarem muitos `if` para
expectativas específicas de simulação (e.g., `shouldCallFoo`), ou colocarem funções dentro da
tabela (e.g., `setupMocks func(*FooMock)`).

No entanto, ao testar comportamentos que apenas
mudam com base na entrada alterada, pode ser preferível agrupar casos semelhantes
juntos em um teste de tabela para ilustrar melhor como o comportamento muda em todas as entradas,
em vez de dividir unidades comparáveis em testes separados
e torná-los mais difíceis de comparar e contrastar.

Se o corpo do teste for curto e direto,
é aceitável ter um único caminho de ramificação para casos de sucesso versus falha
com um campo de tabela como `shouldErr` para especificar expectativas de erro.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
func TestComplicatedTable(t *testing.T) {
  tests := []struct {
    give          string
    want          string
    wantErr       error
    shouldCallX   bool
    shouldCallY   bool
    giveXResponse string
    giveXErr      error
    giveYResponse string
    giveYErr      error
  }{
    // ...
  }

  for _, tt := range tests {
    t.Run(tt.give, func(t *testing.T) {
      // setup mocks
      ctrl := gomock.NewController(t)
      xMock := xmock.NewMockX(ctrl)
      if tt.shouldCallX {
        xMock.EXPECT().Call().Return(
          tt.giveXResponse, tt.giveXErr,
        )
      }
      yMock := ymock.NewMockY(ctrl)
      if tt.shouldCallY {
        yMock.EXPECT().Call().Return(
          tt.giveYResponse, tt.giveYErr,
        )
      }

      got, err := DoComplexThing(tt.give, xMock, yMock)

      // verify results
      if tt.wantErr != nil {
        require.EqualError(t, err, tt.wantErr)
        return
      }
      require.NoError(t, err)
      assert.Equal(t, want, got)
    })
  }
}
```

</td><td>

```go
func TestShouldCallX(t *testing.T) {
  // setup mocks
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)
  xMock.EXPECT().Call().Return("XResponse", nil)

  yMock := ymock.NewMockY(ctrl)

  got, err := DoComplexThing("inputX", xMock, yMock)

  require.NoError(t, err)
  assert.Equal(t, "want", got)
}

func TestShouldCallYAndFail(t *testing.T) {
  // setup mocks
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)

  yMock := ymock.NewMockY(ctrl)
  yMock.EXPECT().Call().Return("YResponse", nil)

  _, err := DoComplexThing("inputY", xMock, yMock)
  assert.EqualError(t, err, "Y failed")
}
```
</td></tr>
</tbody></table>

Essa complexidade torna mais difícil alterar, entender e comprovar a
correção do teste.

Embora não haja diretrizes rigorosas, a legibilidade e a manutenibilidade devem
sempre ser as principais considerações ao decidir entre Testes de Tabela e testes separados
para múltiplas entradas/saídas em um sistema.

## Testes Paralelos

Testes paralelos, assim como alguns loops especializados (por exemplo, aqueles que iniciam
goroutines ou capturam referências como parte do corpo do loop),
devem tomar cuidado para atribuir explicitamente variáveis de loop dentro do escopo do loop para
garantir que elas contenham os valores esperados.

```go
tests := []struct{
  give string
  // ...
}{
  // ...
}

for _, tt := range tests {
  tt := tt // for t.Parallel
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

No exemplo acima, devemos declarar uma variável `tt` com escopo para a iteração do loop
devido ao uso de `t.Parallel()` abaixo.
Se não fizermos isso, a maioria ou todos os testes receberá um valor inesperado para
`tt`, ou um valor que muda enquanto estão sendo executados.

<!-- TODO: Explique como usar pacotes _test. -->
