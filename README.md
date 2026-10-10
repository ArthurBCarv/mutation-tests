# Testes de mutação com StrykerJS

Trabalho de Automação e Testes baseado no primeiro trabalho de testes unitários da classe `Cartao`. O objetivo é avaliar a capacidade dos testes Jest de detectar alterações incorretas no código usando o **StrykerJS**.

## Projeto original

A classe em [`src/cartao.js`](src/cartao.js) implementa pagamentos, consultas e alterações de saldo e limite, bloqueio e desbloqueio, dados do titular e vínculo com conta. Os 43 testes em [`test/cartao.test.js`](test/cartao.test.js) verificam retornos, mudanças de estado, erros e valores de fronteira.

O código da classe e os testes originais foram preservados. Foram adicionados o StrykerJS, sua integração com Jest, comandos de execução e relatórios. O Jest foi mantido apenas nas dependências de desenvolvimento, eliminando sua declaração duplicada.

## Executar

Requisitos: **Node.js 22 ou superior** e npm. O GitHub Actions está configurado para Node.js 22. A validação local foi realizada com Node.js 26.4.0 e npm 12.0.2.

Na pasta do projeto:

```bash
npm ci
npm test
npm run coverage
npm run test:mutation
```

Para executar cobertura e mutação em sequência:

```bash
npm run test:all
```

O comando `npm run test:mutation` executa `stryker run`, usando [`stryker.config.json`](stryker.config.json). As versões do Stryker e do runner Jest são 10.0.0; o `package-lock.json` fixa a árvore de dependências para instalação com `npm ci`.

## Como a técnica funciona

O Stryker introduz pequenas alterações, chamadas **mutantes**, em cópias temporárias do código. Exemplos: trocar uma subtração por uma soma, alterar um operador de comparação ou remover o corpo de um método. Depois, executa os testes contra essas alterações.

- **Killed (morto):** algum teste falhou com a alteração, detectando o defeito artificial.
- **Survived (sobrevivente):** os testes passaram mesmo com a alteração; é necessário investigar uma lacuna nos testes ou uma mutação equivalente.
- **NoCoverage:** nenhum teste executou o trecho alterado.
- **Timeout:** a execução do mutante excedeu o tempo permitido.
- **CompileError / RuntimeError:** houve erro de compilação ou execução, que precisa ser analisado separadamente.

A cobertura tradicional indica quais trechos foram executados. Os testes de mutação avaliam se as verificações percebem mudanças no comportamento desses trechos.

## Configuração adotada

- **Escopo:** todos os arquivos JavaScript em `src/**/*.js`; os arquivos de teste não são mutados.
- **Runner:** Jest, reutilizando a configuração de `package.json`.
- **Análise por teste:** `coverageAnalysis: "perTest"`, para selecionar os testes relacionados a cada mutante.
- **Concorrência:** dois processos de teste.
- **Relatórios:** terminal, HTML e JSON.
- **Limiares:** alto a partir de 90%, baixo abaixo de 80%; a execução falha se o score ficar abaixo de 80%. Esse mínimo é uma escolha deste projeto, não uma exigência informada pelo professor.

Nenhum operador de mutação foi excluído e nenhum trecho de `src` foi desabilitado para melhorar a pontuação.

## Resultados obtidos

Execução local em **10/10/2026**:

| Indicador | Resultado |
| --- | ---: |
| Testes unitários aprovados | 43 de 43 |
| Cobertura de instruções, ramificações, funções e linhas | 100% em cada métrica |
| Mutantes gerados | 99 |
| Mutantes mortos | 99 |
| Sobreviventes | 0 |
| Sem cobertura | 0 |
| Timeouts | 0 |
| Erros | 0 |
| **Mutation score** | **100%** |

Nesta execução, sem timeouts ou erros, a pontuação corresponde a `99 / 99 × 100 = 100%`. A suíte original já detectou todos os mutantes gerados, portanto não foi necessário adicionar testes para eliminar sobreviventes.

Veja a análise com exemplos reais em [`docs/RESULTADOS.md`](docs/RESULTADOS.md). O resultado se refere ao código, aos testes e aos operadores utilizados nesta execução; 100% não garante ausência de bugs ou de requisitos ainda não implementados.

## Consultar os relatórios

Após executar `npm run test:mutation`, abra no navegador:

```text
reports/mutation/mutation.html
```

O relatório permite inspecionar cada mutante e os testes que o detectaram. Os dados completos também ficam em `reports/mutation/mutation.json`.

Os relatórios gerados e a pasta temporária `.stryker-tmp/` são ignorados pelo Git. A análise resumida em `docs/RESULTADOS.md` faz parte da entrega e os relatórios completos podem ser regenerados com o comando acima.

## GitHub Actions e entrega

O workflow [`Build and Tests`](.github/workflows/node.js.yml) executa a instalação com `npm ci`, a cobertura e os testes de mutação em pushes e pull requests para `master`, diariamente e por execução manual. Após a execução no GitHub, os relatórios ficam no artefato **stryker-mutation-report**, na página da execução em **Actions**. Baixe e extraia o artefato para abrir o HTML. O job SonarCloud existente continua dependendo do segredo `SONAR_TOKEN`; os testes e o Stryker não dependem dele.

Para entregar, publique as alterações no repositório GitHub e envie o link por e-mail ao professor até o prazo informado, **10/10/2026, ao final da noite**. Confira se o professor tem acesso ao repositório. Este projeto não envia o e-mail automaticamente.

## Referências

- [Stryker Mutator](https://stryker-mutator.io/)
- [Instalação e execução do StrykerJS](https://stryker-mutator.io/docs/stryker-js/getting-started/)
- [Integração oficial com Jest](https://stryker-mutator.io/docs/stryker-js/jest-runner/)
- [Opções de configuração](https://stryker-mutator.io/docs/stryker-js/configuration/)


### Adicionando uma linha ao commit para os actions acionarem automaticamente
