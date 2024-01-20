# nil é uma fatia válida

`nil` é uma fatia válida com comprimento 0. Isso significa que,

- Você não deve retornar explicitamente uma fatia de comprimento zero. Retorne `nil`
  em vez disso.

  <table>
  <thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
  }
  ```

  </td><td>

  ```go
  if x == "" {
    return nil
  }
  ```

  </td></tr>
  </tbody></table>

- Para verificar se uma fatia está vazia, sempre use `len(s) == 0`. Não verifique
  `nil`.

  <table>
  <thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- O valor zero (uma fatia declarada com `var`) pode ser usado imediatamente sem
  `make()`.

  <table>
  <thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // or, nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

Lembre-se de que, embora seja uma fatia válida, uma fatia nula não é equivalente a uma
fatia alocada com comprimento 0 - uma é nula e a outra não é - e as duas podem
ser tratadas de maneira diferente em situações diferentes (como serialização).
