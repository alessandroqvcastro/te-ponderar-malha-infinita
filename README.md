# TE Ponderar — Malha Infinita (Modo Laboratório)

Um experimento computacional mínimo para tornar a **Malha Q** observável em linguagem de laboratório.

Este repositório contém o simulador **TE Ponderar (v0.3)**, escrito em Python (notebook `.ipynb`), que implementa uma **ontologia operacional** baseada em:

- **Distinções** (*markers* / “Q” como conjuntos que particionam um universo discreto `N`)
- **Relações** (*edges* / “REL” conectando markers em um grafo)
- Um **princípio variacional** (*ganho informacional vs. custo descritivo*) que decide o próximo evento

> Importante: isto **não** é um “modelo cosmológico padrão” nem uma simulação física completa.
> É um **laboratório conceitual**: um mecanismo mínimo para observar como **distinções + relações + custo**
> podem produzir (ou impedir) crescimento estrutural, saturação e transições de regime.

---

## 1) Ideia central (em uma linha)

Em cada passo, o sistema escolhe o evento que maximiza:

**U = ΔI − C**

onde:

- **ΔI** é o ganho de informação/estrutura;
- **C** é o custo de descrever/instanciar a operação.

---

## 2) O que o simulador mede (I)

A métrica informacional usada nesta versão (v0.3) é, em essência:

**I = H + ρ·log2(R) + λ_rel·log2(1 + E) + (bônus leve por aresta)**

onde:

- **H** = entropia (em bits) da distribuição dos blocos induzidos pelos markers;
- **R** = número de blocos (partição efetiva do universo);
- **E** = número de relações (arestas) no grafo REL;
- **ρ (rho)** pesa a “pressão” por diversidade de blocos;
- **λ_rel** pesa o valor informacional da conectividade (REL);
- e há um bônus leve dependente do “tamanho” relativo das ligações (heurística controlada por `lam_rel_size`).

---

## 3) Quais são os “eventos” (candidatos)

O simulador testa candidatos e escolhe o melhor por utilidade **U**:

### A) SPLIT_RULE (lei determinística)
Cria um novo marker aplicando uma regra determinística ao maior bloco atual  
(ex.: `FIRST_HALF`, `EVERY_OTHER`, `MOD3_0` etc.).

- Tende a aumentar **H** e **R**
- Tem custo descritivo maior (**C** alto)

### B) SPLIT_RAND (divisão combinatória/aleatória)
Cria um marker escolhendo subconjuntos aleatórios do maior bloco.

- O custo cresce com `log2(C(n,k))` (combinatório)

### C) SINGLETON
Cria marker com um único elemento do maior bloco.

- É uma distinção extrema (e relativamente cara)

### D) REL (relação entre markers)
Adiciona uma aresta entre dois markers ainda não conectados.

- Custo baixo, crescendo lentamente com o número de relações existentes:
  `rel_base + rel_scale*log2(1+E)`
- Pode produzir ganho pequeno porém “barato” (ΔI modesto, mas **C** menor)

---

## 4) Macro-métricas de saturação (Malha Infinita)

Além de **H**, **R** e **I**, o notebook imprime métricas “macro” para acompanhar a transição de regime:

- **dens**: densidade do grafo REL (aprox. `E / Emax`)
- **<k>**: grau médio do grafo
- **gcc**: tamanho da maior componente conectada (coesão global)
- **Hnorm**: entropia normalizada (aprox. `H / log2(R)`)

Intuição: quando o inventário de ligações deixa de ser a unidade útil, métricas agregadas
começam a “descrever melhor” o estado do sistema.

---

## 5) Como rodar (Colab / Local)

### ✅ Rodar no Colab (recomendado)

Notebook principal:

- `TE_Ponderar_—_Modo_Laboratório_Malha_Infinita_v0_3.ipynb`

Link Colab:

- https://colab.research.google.com/drive/1DwqtXJGZ9TCu_7dSTt3JHwECyZq-1CM_?usp=sharing

Passos:

1. Abra o link.
2. Menu: **Runtime** → **Run all** (ou “Executar tudo”).
3. O notebook imprime o log no console e pode salvar logs em arquivo (`*.txt`) quando `log_path` é definido.

### Rodar localmente (opcional)

Pré-requisitos:

- Python 3.10+
- Jupyter

Comandos típicos:

```bash
pip install jupyter
jupyter notebook
```

Depois, abra o arquivo `.ipynb` e execute as células.

---

## 6) Regimes incluídos (A_strict, A_soft, B)

O notebook v0.3 foi pensado para executar **três regimes** que ilustram transições de economia ontológica:

- **A_strict**: barreira estrita (`acceptU_min = 0.0`)  
  Interpretação: o sistema só “investe” em eventos com utilidade não-negativa.

- **A_soft**: barreira suave (`acceptU_min = -0.05`)  
  Interpretação: o sistema permite pequenos “investimentos” locais (U ligeiramente negativo) para atravessar gargalos.

- **B_soft_HIGH_REL**: custo de REL 10× maior (`rel_base` e `rel_scale` multiplicados por 10)  
  Interpretação: testar como a malha muda quando **relações ficam caras**.

---

## 7) O que o laboratório viu (resumo narrativo dos logs)

**A_strict (U >= 0):**  
O sistema começa com um salto de distinção (`SPLIT_RULE`), em seguida cria a **primeira relação** (`REL`),
e continua conectando até saturar rapidamente. Ele para quando nenhum próximo passo tem utilidade ≥ 0.

**A_soft (U >= -0.05):**  
Ao permitir um pequeno investimento, o sistema cria mais um marcador e fecha a conectividade,
atingindo densidade alta (quase “grafo completo” no microcosmo) antes de parar.

**B (REL caro):**  
O laboratório mostra um comportamento qualitativamente distinto: relações deixam de ser a opção “barata”.
O sistema para cedo e os melhores próximos passos passam a competir entre “tentar relacionar” (mesmo caro)
e “forçar novos splits” (mais caros ainda). É um teste simples de robustez: alterar a economia altera o regime.

---

## 8) Reprodutibilidade

O simulador usa `seed` fixa por regime. Mantendo:

- o mesmo `seed`,
- os mesmos parâmetros (`N`, `rho`, custos, lambdas),
- e a mesma versão do notebook,

o log deve ser reprodutível.

---

## 9) Estrutura do repositório

- `*.ipynb` — notebook principal (simulador)
- `README.md` — este arquivo

Sugestão (opcional, mas recomendado):

- `logs/` — pasta para logs exportados (`A_strict.txt`, `A_soft.txt`, `B_soft.txt`)

---

## 10) Limites e escopo

Este simulador:

- não modela campos físicos, partículas, relatividade, mecânica quântica ou cosmologia observacional;
- não “prova” uma teoria física;
- não pretende substituir formalismos existentes;

Ele serve para uma coisa específica:

> tornar observável, com regras mínimas, a dinâmica “distinção + relação + custo”
> e como isso gera (ou bloqueia) crescimento e saturação em um microcosmo discreto.

---

## 11) Citação (rascunho)

Se você for referenciar este repositório em texto acadêmico, um formato simples (provisório) é:

> Castro, A. Q. V. “TE Ponderar — Malha Infinita (Modo Laboratório)”, GitHub, v0.3, 2025.

---

## 12) Licença

Licença ainda **não definida** neste repositório (por padrão: direitos reservados).
Se você quiser abrir para colaboração pública, recomendo escolher e incluir um arquivo `LICENSE`.

