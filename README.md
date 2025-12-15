# TE Ponderar — Malha Infinita (Modo Laboratório)

**Um experimento computacional mínimo para tornar a Malha Q observável em linguagem de laboratório.**  
Este repositório contém o simulador **TE Ponderar (v0.3)**, escrito em Python (notebook `.ipynb`), que implementa uma ontologia operacional baseada em:

- **Distinções** (markers / “Q” como conjuntos que particionam um universo discreto `N`)
- **Relações** (edges / “REL” conectando markers em um grafo)
- Um **princípio variacional** (ganho informacional vs. custo descritivo) que decide o próximo evento.

> Importante: isto **não** é um “modelo cosmológico padrão” nem uma simulação física completa.  
> É um **laboratório conceitual**: um mecanismo mínimo para observar como *distinções + relações + custo* podem produzir (ou impedir) crescimento estrutural, saturação e transições de regime.

---

## 1) Ideia central (em uma linha)

Em cada passo, o sistema escolhe o evento que maximiza:

**U = ΔI − C**

onde:
- `ΔI` é o ganho de informação/estrutura,
- `C` é o custo de descrever/instanciar a operação.

---

## 2) O que o simulador mede (I)

A métrica informacional usada nesta versão (v0.3) é, em essência:

**I = H + ρ·log2(R) + λ_rel·log2(1 + E) + (bônus leve por aresta)**

onde:
- `H` = entropia (em bits) da distribuição dos blocos induzidos pelos markers,
- `R` = número de blocos (partição efetiva do universo),
- `E` = número de relações (arestas) no grafo REL,
- `ρ` (rho) pesa a “pressão” por diversidade de blocos,
- `λ_rel` pesa o valor informacional da conectividade (REL),
- e há um bônus leve dependente do “tamanho” relativo das ligações (heurística controlada por `lam_rel_size`).

---

## 3) Quais são os “eventos” (candidatos)

O simulador testa candidatos e escolhe o melhor por utilidade `U`:

### A) SPLIT_RULE (Lei determinística)
Cria um novo marker aplicando uma regra determinística ao **maior bloco atual**  
(ex.: FIRST_HALF, EVERY_OTHER, MOD3_0 etc.).  
- **Tende a aumentar H e R**, mas tem **custo descritivo maior** (C alto).

### B) SPLIT_RAND (divisão combinatória/aleatória)
Cria marker escolhendo subconjuntos aleatórios do maior bloco.  
- Custo cresce com `log2(C(n,k))` (combinatório).

### C) SINGLETON
Cria marker com um único elemento do maior bloco.  
- Uma distinção extrema (e cara, relativamente).

### D) REL (relação entre markers)
Adiciona uma aresta entre dois markers ainda não conectados.  
- **Custo baixo** (cresce lentamente com E: `rel_base + rel_scale*log2(1+E)`),
- pode produzir ganho pequeno porém “barato” (ΔI modesto, mas C menor).

---

## 4) Macro-métricas de saturação (Malha Infinita)

Além de `H, R, I`, o notebook imprime métricas “macro” para acompanhar a transição de regime:

- **dens**: densidade do grafo REL (`E / Emax`)
- **<k>**: grau médio do grafo
- **gcc**: tamanho da maior componente conectada (coesão global)
- **Hnorm**: entropia normalizada (aprox. `H / log2(R)`)

A intuição por trás disso é simples:  
quando o inventário de ligações deixa de ser a unidade útil, métricas agregadas começam a “descrever melhor” o estado do sistema.

---

## 5) Como rodar (Colab / Local)

### ✅ Rodar no Colab (recomendado)
Notebook principal:
- **TE Ponderar — Modo Laboratório Malha Infinita (v0.3).ipynb**  
Link Colab:
- https://colab.research.google.com/drive/1DwqtXJGZ9TCu_7dSTt3JHwECyZq-1CM_?usp=sharing

Passos:
1. Abra o link.
2. `Runtime > Run all` (Executar tudo).
3. O notebook gera logs no ambiente (e pode salvá-los em arquivo, se configurado).

### Rodar localmente (opcional)
Pré-requisitos:
- Python 3.10+
- Jupyter

Comandos típicos:
```bash
pip install jupyter
jupyter notebook
