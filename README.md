# Análise de Complexidade Ciclomática - Sistema de Login

Este repositório contém a análise de fluxo de controle e complexidade ciclomática referente à classe `User` (funcionalidade de login). A análise tem como objetivo mapear os caminhos lógicos do código e identificar falhas potenciais através da técnica de teste de caixa branca.

## 1. Notação de Grafo de Fluxo

Abaixo está a representação visual do fluxo de controle do código analisado, abrangendo os métodos `conectarBD` e `verificarUsuario`.

![Grafo de Fluxo](Grafo%20de%20Fluxo.drawio.png)

### Dados do Grafo
* **Nós (N):** 11
* **Arestas (E):** 10
* **Componentes Conexos (P):** 2 (Referente aos métodos envolvidos)

---

## 2. Complexidade Ciclomática

A complexidade ciclomática $V(G)$ foi calculada utilizando a fórmula baseada na teoria dos grafos de McCabe:

$$V(G) = E - N + 2P$$

Substituindo pelos valores extraídos do grafo:
$$V(G) = 10 - 11 + 2(2)$$
$$V(G) = 3$$

Também é possível validar este valor através da contagem de nós de predicado (decisão) + 1:
* Nós de decisão no grafo (bifurcações): 2 (Nó 3 e Nó 7)
* $V(G) = 2 + 1 = 3$

**Resultado:** A complexidade ciclomática do código é **3**. Isso indica que são necessários, no mínimo, 3 casos de teste para cobrir todos os caminhos independentes do código.

---

## 3. Caminhos Básicos

Com base na complexidade ciclomática, identificamos os 3 caminhos independentes que percorrem o sistema:

1.  **Caminho 1 (Fluxo de Sucesso/Falha Completa):**
    * `1 - 2 - 3 - 4 - 6 - 7 - 8 - 9 - 10`
    * *Descrição:* Fluxo onde a conexão ocorre, a verificação lógica prossegue e entra no bloco `try/if`.

2.  **Caminho 2 (Falha na Verificação Lógica 1):**
    * `1 - 2 - 3 - 5 - 6 - 7 - 11`
    * *Descrição:* Fluxo alternativo na primeira decisão lógica.

3.  **Caminho 3 (Falha na Verificação Lógica 2):**
    * `1 - 2 - 3 - 4 - 6 - 7 - 11`
    * *Descrição:* Fluxo onde a primeira condição é atendida, mas a segunda decisão desvia para o encerramento.

---

## 4. Análise de Risco e Lógica

Durante a análise dos caminhos básicos, foi identificada uma **inconsistência lógica** no código original:

> **Observação Crítica:** O caminho teórico que passaria pelas arestas `5 - 6 - 7 - 8` é tecnicamente inalcançável ("Dead Code" ou lógica falha) devido à estrutura atual de tratamento de erros.
>
> Se a conexão com o banco falhar no método `conectarBD` (Nós 1-2), o método retorna `null` (ou uma conexão vazia) e o fluxo segue. Porém, ao tentar usar essa conexão nula no método seguinte (Nó 7 em diante), ocorrerá uma `NullPointerException` ou erro SQL antes que a lógica de negócio possa ser processada corretamente. Isso evidencia a necessidade de um tratamento de exceção mais robusto ou uma verificação de nulidade antes de executar a query.