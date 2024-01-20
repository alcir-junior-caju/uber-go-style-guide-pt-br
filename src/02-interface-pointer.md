# Ponteiros para Interfaces

Você quase nunca precisa de um ponteiro para uma interface. Você deve estar passando
interfaces como valores - os dados subjacentes ainda podem ser um ponteiro.

Uma interface possui dois campos:

1. Um ponteiro para alguma informação específica do tipo. Você pode pensar nisso como
   "tipo."
2. Ponteiro de dados. Se os dados armazenados são um ponteiro, eles são armazenados diretamente. Se
   os dados armazenados são um valor, então um ponteiro para o valor é armazenado.

Se você deseja que os métodos da interface modifiquem os dados subjacentes, você deve usar um
ponteiro.
