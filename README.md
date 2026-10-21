# Mini CPU de 16 bits

Projeto da disciplina **Prática em Sistemas Digitais (SSC0108)**, do Instituto de Ciências Matemáticas e de Computação (ICMC) da Universidade de São Paulo (USP), São Carlos, SP.

**Docente:** Denis Fernando Wolf

**Autores:**
- Henrique Mohr
- Pedro Henrique Costa Teixeira
- Henrique Rossi Posso

---

## Sobre o projeto

O objetivo principal do projeto é projetar, implementar e validar o funcionamento de uma **Mini CPU de 16 bits** (`WIDTH=16`) com suporte a uma memória de programa de **64 posições** (`DEPTH=64`), capaz de executar sequencialmente um conjunto de instruções para processamento aritmético, lógico e controle de fluxo.

## Funcionalidades

### Processamento aritmético e lógico
- Operações aritméticas básicas de adição, subtração, multiplicação e divisão entre registradores internos e valores imediatos/constantes.
- Operações lógicas bit a bit (`AND`, `OR`, `XOR`) e determinação de valor máximo (`MAX`).

### Gerenciamento de registradores internos
- Leitura, escrita e atualização do banco de registradores gerais da CPU (registradores **A**, **B** e **C**).
- Zeramento individual de registradores via instrução aritmética (ex.: `A = A - A`).

### Controle de fluxo e tomada de decisão
- Desvios condicionais e incondicionais: `JUMP IF EQUAL`, `JUMP IF ZERO` e `JUMP INCONDICIONAL`.
- Módulo de comparação (`COMPARA`) para avaliação de condições relativas entre operandos (identificação de igualdade ou magnitude para acionamento de saídas/LEDs).
- Rotinas estruturadas de repetição (loops).

### Sinalização e reinicialização
- Decodificador de instruções que evita falsos acionamentos por sobreposição de padrões de bits (ex.: diferenciação entre um endereço de `JUMP` e o código de `Master Reset`).
- Instrução de **Master Reset**, que redefine o estado dos registradores gerais (A=0, B=0, C=0) e reinicia o ciclo de busca e execução a partir do endereço de memória 0.

---

## Palavra de instrução (bits 15 a 0)

```
 B15 B14 | B13 B12 | B11 B10 | B9 B8 | B7 B6 | B5 B4 | B3 B2 B1 B0
 operação| origem 1| origem 2| destino| jump  | especial| constante
```

### B15–B14: operação aritmética

| Bits | Operação |
|------|----------|
| `00` | Soma |
| `01` | Subtração |
| `10` | Multiplicação |
| `11` | Divisão |

> **Atenção:** na divisão, a **origem 2** é sempre o **dividendo** e a **origem 1** é o **divisor**.

### B13–B12: origem 1 · B11–B10: origem 2

| Bits | Fonte |
|------|-------|
| `00` | Registrador A |
| `01` | Registrador B |
| `10` | Registrador C |
| `11` | Constante |

### B9–B8: registrador de destino

| Bits | Destino |
|------|---------|
| `00` | Registrador A |
| `01` | Registrador B |
| `10` | Registrador C |
| `11` | Inválido (usado quando não é preciso alterar nenhum registrador) |

### B7–B6: operação de jump

| Bits | Operação |
|------|----------|
| `00` | Inválido |
| `01` | JUMP INCONDICIONAL |
| `10` | JUMP IF ZERO |
| `11` | JUMP IF EQUAL |

> **Observação:** os jumps têm preferência máxima sobre as outras operações. Mesmo que haja um `Master Reset`, o jump é executado, além das outras operações.

### B5–B4: Master Reset, Comparador e Operação Nova

| Bits | Operação |
|------|----------|
| `00` | Inválido |
| `01` | Master Reset |
| `10` | Comparar |
| `11` | Operação Nova |

Na **Operação Nova**, a calculadora lógica é ativada e os bits B15–B14 determinam qual operação lógica é feita:

| B15–B14 | Operação |
|---------|----------|
| `00` | AND |
| `01` | OR |
| `10` | XOR |
| `11` | MAX (pega o maior dos números dos registradores e grava no registrador de destino) |

### B3–B0: constantes fixas

---

## Operação MOV

Não há uma instrução MOV própria. A movimentação é feita com a instrução **SOMA**, usando a constante `0000` como origem 2.

A constante B3–B0 é compartilhada pelas duas origens, então carregar um valor imediato exige que a outra origem seja um registrador com valor zero.

---

## Conclusão

O desenvolvimento e a validação do projeto comprovam a arquitetura proposta para o processamento de dados e o controle de rotinas: operações aritméticas fundamentais (com destaque para o alinhamento e mapeamento dos operandos de divisão), operações lógicas (AND, OR, XOR e MAX), controle de fluxo com desvios condicionais e incondicionais, laços de repetição e Master Reset.

## Referências

- BROWN, S.; VRANESIC, Z. *Fundamentals of Digital Logic with VHDL Design*, 2nd Edition, McGraw Hill, 2004, 939pg.
- BROWN, S.; VRANESIC, Z. *Fundamentals of Digital Logic with VHDL Design*, 3th Edition, McGraw Hill, 2014, 864pg.
- IDOETA, I.V.; CAPUANO, F.G. *Elementos de Eletrônica Digital*, Livros Érica, 2007.
