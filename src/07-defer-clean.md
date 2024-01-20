# Utilize Defer para Limpeza

Use defer para limpar recursos como arquivos e travas.

<table>
<thead><tr><th>Ruim</th><th>Bom</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// fácil perder desbloqueios devido a múltiplos retornos
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// mais legível
```

</td></tr>
</tbody></table>

O defer tem uma sobrecarga extremamente pequena e deve ser evitado apenas se você puder
provar que o tempo de execução da sua função está na ordem de nanossegundos. O
ganho de legibilidade ao usar defers vale o custo minúsculo de usá-los. Isso
é especialmente verdadeiro para métodos maiores que têm mais do que simples acessos à memória, onde os outros cálculos são mais significativos do que o `defer`.
