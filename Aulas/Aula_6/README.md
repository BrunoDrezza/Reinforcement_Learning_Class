# Atividade: Algoritmo SARSA (On-Policy) vs Q-Learning (Off-Policy)## 1. Análise do Ambiente Taxi-Driver

**Qual algoritmo tem os melhores resultados? A curva de aprendizado dos dois algoritmos é a mesma? O comportamento final do agente, depois de treinado, é ótimo em ambos os casos?**

Ambos os algoritmos convergem para resultados excelentes e alcançam uma política ótima no ambiente do Taxi-Driver (retornos em torno de 8.0 a 11.0, dependendo do estado inicial).
* **Curva de Aprendizado:** As curvas são muito semelhantes. Contudo, o SARSA tende a ter uma curva de aprendizado ligeiramente mais suave durante o treinamento. Por ser *on-policy*, ele penaliza as ações exploratórias ruins (causadas pelo $\epsilon$), sofrendo menos quedas abruptas que o Q-Learning.
* **Comportamento Final:** Na fase de testes (com $\epsilon = 0$), o comportamento final de ambos é ótimo, resolvendo o ambiente com o número mínimo de passos.

![Curva de Aprendizado SARSA - Taxi](..\..Outputs/Assignment/curva_sarsa_taxi_v3.png)

## 2. Análise do Ambiente Cliff Walking

**Qual algoritmo tem os melhores resultados? A curva de aprendizado dos dois algoritmos é a mesma? O comportamento final do agente, depois de treinado, é ótimo? Qual agente tem um comportamento mais conservador e qual tem um comportamento mais otimista?**

É no `CliffWalking` que as diferenças teóricas se tornam evidentes na prática:
* **Conservador vs. Otimista:** O **Q-Learning** é **otimista**. Ele aprende a rota mais rápida colada no abismo (recompensa de **-13.0**), pois ignora as quedas acidentais causadas pela exploração. O **SARSA** é **conservador**. Ele atualiza seus valores considerando a política real (com o $\epsilon$), percebe o perigo de "tropeçar" no penhasco e aprende uma rota mais segura, afastada da borda (recompensa de **-15.0**).
* **Curva de Aprendizado:** As curvas são diferentes. A curva do SARSA é superior (menos negativa) durante o treinamento, pois ele aprende rapidamente a evitar o penhasco. O Q-Learning cai no abismo frequentemente durante o treino, jogando sua curva para baixo.
* **Comportamento Final:** O comportamento final de ambos é "ótimo" sob perspectivas diferentes. O Q-Learning encontra o menor caminho absoluto (ótimo teórico). O SARSA encontra o menor caminho seguro (ótimo prático sob penalidade de exploração).

![Curva de Aprendizado SARSA - Cliff Walking](..\..\Outputs/Assignment/curva_sarsa_cliffwalking_v1.png)

## 3. Diferença Geral: Q-Learning vs SARSA

A diferença elementar está na equação de atualização da Função de Valor (Equação de Bellman):
* **Q-Learning (Off-Policy):** Calcula o erro temporal com base no valor máximo possível do próximo estado: $\max_{A'}Q(s',A')$. Ele aprende a política ótima ignorando a política de exploração atual.
* **SARSA (On-Policy):** Calcula o erro temporal com base na ação $a'$ que o agente **realmente executará** no próximo estado: $Q(s',a')$. Ele avalia e melhora a política que está sendo seguida no momento.

Em suma, os agentes terão comportamentos e curvas de aprendizado distintas em ambientes que apresentam caminhos perigosos adjacentes a rotas ótimas (como o Cliff Walking), sendo o SARSA mais adequado quando a punição por erros durante a execução/treino for inaceitável.