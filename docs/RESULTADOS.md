# Análise dos testes de mutação

## Objetivo e procedimento

Avaliar a qualidade dos 43 testes unitários do primeiro trabalho, aplicando mutações à classe `Cartao` com StrykerJS 10.0.0 e seu runner Jest 10.0.0.

1. Executar os testes originais e confirmar que passam antes de introduzir mutações.
2. Medir a cobertura com `npm run coverage`.
3. Executar `npm run test:mutation`, com todos os arquivos `src/**/*.js` no escopo.
4. Inspecionar o relatório JSON e o resumo do Stryker para identificar sobreviventes, ausência de cobertura e erros.

Ambiente da execução de 10/10/2026: Linux, Node.js 26.4.0, npm 12.0.2, Jest 30.5.0. A configuração limita a concorrência a dois processos e não exclui operadores de mutação.

## Evidência da execução

Resumo apresentado pelo Stryker:

```text
File       | Mutation score | Killed | Timeout | Survived | No coverage | Errors
All files  |         100.00 |     99 |       0 |        0 |           0 |      0
 cartao.js |         100.00 |     99 |       0 |        0 |           0 |      0
```

A suíte passou com 43 testes. A cobertura tradicional foi de 100% para instruções, ramificações, funções e linhas. Todos os 99 mutantes receberam o estado `Killed`, confirmado no relatório `reports/mutation/mutation.json`.

## Exemplos reais de mutantes detectados

Os IDs abaixo correspondem ao relatório desta execução; podem mudar se o código ou a versão da ferramenta mudar.

| ID | Mutação | Teste que detectou | Motivo da falha |
| --- | --- | --- | --- |
| 2 | `this.saldo - valor` → `this.saldo + valor` | Pagamento de 200 com saldo inicial de 1000 | O teste esperava 800, mas o mutante retornou 1200. |
| 5 | `saldoResultante < 0` → `saldoResultante <= 0` | Pagamento com valor igual ao saldo | O mutante lançou erro ao chegar a zero, embora o pagamento devesse ser permitido. |
| 38 | `this.saldo >= valor` → `this.saldo > valor` | Saldo suficiente com valor igual ao saldo | O teste esperava `true`, mas o mutante retornou `false`. |

Os exemplos evidenciam a importância de conferir o valor retornado e de testar as fronteiras de comparação, principalmente a igualdade entre valor e saldo. Executar um método sem verificar seu resultado não oferece a mesma proteção.

## Interpretação

A pontuação de 100% mostra que a suíte original detectou todas as 99 alterações artificiais geradas nesta configuração. Não houve sobreviventes a investigar nem necessidade de alterar a classe ou acrescentar testes para essa análise. Não foi feita uma comparação artificial com uma suíte enfraquecida.

A pontuação não demonstra que todas as regras de negócio possíveis estão corretas. Por exemplo, a rejeição de valores negativos em `fazerPagamento` não está implementada no código original; o Stryker mede a detecção de alterações no código existente, não a presença de requisitos ausentes. Uma revisão de requisitos continua sendo uma atividade distinta.

## Reproduzir

```bash
npm ci
npm run test:all
```

Abra `reports/mutation/mutation.html` no navegador para inspecionar as mutações e seus testes. Os relatórios são gerados novamente a cada execução; este documento registra o resultado observado, sem substituir a execução da ferramenta.
