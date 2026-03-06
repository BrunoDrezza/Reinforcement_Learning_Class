# Atividade: Análise Comparativa - Algoritmos SARSA e Q-Learning

Este repositório contém a implementação e a análise comparativa dos algoritmos de Aprendizado por Reforço Q-Learning (Off-policy) e SARSA (On-policy), aplicados aos ambientes `Taxi-v3` e `CliffWalking-v1` da biblioteca Gymnasium.

## 1. Análise do Ambiente Taxi-Driver

**Qual algoritmo tem os melhores resultados? A curva de aprendizado dos dois algoritmos é a mesma? O comportamento final do agente, depois de treinado, é ótimo em ambos os casos?**

No ambiente do Taxi-Driver, **ambos os algoritmos apresentam resultados equivalentes e excelentes**. 
* **Curva de Aprendizado:** As curvas de aprendizado são extremamente semelhantes e convergem praticamente ao mesmo tempo. O SARSA, contudo, pode apresentar uma curva marginalmente mais suave durante o treinamento por penalizar trajetórias exploratórias ruins (causadas pelo $\epsilon$).
* **Comportamento Final:** Após o treinamento, o comportamento final de ambos os agentes é ótimo. A validação em 100 episódios (com $\epsilon = 0$) comprova isso empírica e estatisticamente:
  * **SARSA:** Média de recompensas de 8.11 (Desvio Padrão: 2.58) | Média de ações: 12.89 (Desvio Padrão: 2.58)
  * **Q-Learning:** Média de recompensas de 8.02 (Desvio Padrão: 2.52) | Média de ações: 12.98 (Desvio Padrão: 2.52)
* **Nota Metodológica (Taxi-v3):** O desvio padrão não é nulo devido à inicialização estocástica do ambiente (onde o passageiro e o destino variam a cada episódio). A igualdade exata entre o desvio padrão das ações e o das recompensas prova matematicamente que os agentes já estão treinados e não cometem erros (como tentar "pegar" um passageiro inexistente ou "deixá-lo" no local errado), o que geraria penalidades de -10. O comportamento é, portanto, perfeitamente linear e ótimo para todas as configurações iniciais.

![Comparação Taxi-v3](https://github.com/BrunoDrezza/Reinforcement_Learning_Class/blob/main/Outputs/Assignment/comparacao_taxi_v3.png?raw=true)


## 2. Análise do Ambiente Cliff Walking

**Qual algoritmo tem os melhores resultados? A curva de aprendizado dos dois algoritmos é a mesma? O comportamento final do agente é ótimo? Qual tem comportamento conservador e qual é otimista?**

É no ambiente *Cliff Walking* que as diferenças teóricas se refletem drasticamente na prática. 
* **Conservador vs. Otimista:** O **Q-Learning** é **otimista**. Ele ignora a função de escolha de ação atual (que possui aleatoriedade) e assume que no futuro sempre executará o valor máximo $max_{A'} Q(s', A')$. Com isso, aprende a rota mais rápida possível tangenciando o abismo. O **SARSA** é **conservador**. Ele atualiza os valores considerando a política exploratória real (com os riscos do $\epsilon$), percebe o perigo de "tropeçar" no penhasco e aprende uma rota mais longa, porém segura.
* **Curva de Aprendizado:** As curvas são nitidamente diferentes. A curva do SARSA é amplamente superior (menos negativa) durante o treinamento *online*, pois ele cai menos no abismo. O Q-Learning sofre severas quedas na recompensa acumulada devido à sua exploração na beira do penhasco.
* **Comportamento Final:** A validação em 100 episódios ilustra que ambos convergem, mas para "ótimos" diferentes:
  * **SARSA:** Média de recompensas -15.0 (Desvio Padrão: 0.0) | Média de ações: 15.0
  * **Q-Learning:** Média de recompensas -13.0 (Desvio Padrão: 0.0) | Média de ações: 13.0
* **Nota Metodológica (Cliff Walking):** Como o ambiente é determinístico e o fator de exploração foi zerado (epsilon = 0) durante a validação, o desvio padrão de 0.0 prova que ambos os agentes convergiram para uma política estável. O Q-Learning encontrou o "ótimo teórico" de 13 passos (beirando o penhasco), enquanto o SARSA encontrou o "ótimo seguro" de 15 passos (rota conservadora).

![Comparação Cliff Walking-v1](https://github.com/BrunoDrezza/Reinforcement_Learning_Class/blob/main/Outputs/Assignment/comparacao_cliffwalking_v1.png?raw=true)


## 3. Diferença Geral: Q-Learning vs SARSA

A diferença elementar encontra-se na Equação de Bellman para a atualização dos valores na Q-table:
* **Q-Learning (Off-Policy):** O algoritmo Q-Learning é *off-policy* porque a diferença entre o novo valor e a estimativa antiga considera o valor máximo das possibilidades no novo estado: $\max_{A'}Q(s',A')$. Ele desvincula a política de aprendizado da política de comportamento atual.
* **SARSA (On-Policy):** O algoritmo SARSA é *on-policy* porque atualiza a estimativa considerando a real ação $a'$ executada pelo agente no novo estado $s'$, utilizando $Q(s',a')$.

**Comportamento e Curvas:** Os agentes treinados **não** terão necessariamente o mesmo comportamento, e as curvas **não** serão iguais caso o ambiente imponha penalidades severas próximas à rota ótima (como no Cliff Walking). O SARSA tende a ter uma performance *online* (curva de aprendizado) melhor e mais estável, aprendendo políticas conservadoras. O Q-Learning foca na política ótima global, independentemente dos custos incorridos durante a fase de treinamento exploratório.