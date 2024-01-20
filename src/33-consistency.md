# Seja Consistente

Algumas das diretrizes delineadas neste documento podem ser avaliadas objetivamente;
outras são situacionais, contextuais ou subjetivas.

Acima de tudo, **seja consistente**.

Código consistente é mais fácil de manter, é mais fácil de racionalizar, requer menos
carga cognitiva e é mais fácil de migrar ou atualizar à medida que novas convenções surgem
ou classes de bugs são corrigidas.

Por outro lado, ter estilos múltiplos, díspares ou conflitantes dentro de um único
código gera sobrecarga de manutenção, incerteza e dissonância cognitiva,
todos os quais podem contribuir diretamente para uma menor velocidade, revisões de código dolorosas
e bugs.

Ao aplicar essas diretrizes a um código, é recomendado que as alterações
sejam feitas em um nível de pacote (ou superior): a aplicação em um nível de subpacote
viola a preocupação acima, introduzindo estilos múltiplos no mesmo código.
