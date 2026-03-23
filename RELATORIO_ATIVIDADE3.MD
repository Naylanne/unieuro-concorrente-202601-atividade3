# Relatório da atividade 3 
**Disciplina: Programação Concorrente e Distribuída**
**Aluno(s): Naylanne Lissa Gomes Cunha**
**Turma: SIN04M1**
**Professor: Rafael Marconi Ramos**
**Data: 20/03/2026**

---

# 1. Descrição do Problema

* Problema implementado: Processamento de arquivos de texto em uma pasta, realizando a contagem de linhas, palavras, caracteres e palavras-chave específicas (erro, warning, info).

* Algoritmo utilizado: Um algoritmo de varredura sequencial sobre cada arquivo, aplicando funções de leitura e contagem. Na versão paralela, utilizou-se multiprocessing com Pool.map para distribuir os arquivos entre múltiplos processos.

* Tamanho da entrada utilizada nos testes: Foram processados 1000 arquivos, totalizando aproximadamente 10 milhões de linhas, 200 milhões de palavras e 1,36 bilhão de caracteres.

* Objetivo da paralelização: Reduzir o tempo de execução do processamento de grandes volumes de arquivos de texto, explorando o paralelismo entre múltiplos núcleos do processador.

* O algoritmo é O(n), onde n representa o número total de linhas (ou palavras/caracteres) nos arquivos. Cada linha é lida e processada uma vez, sem operações aninhadas de maior custo.

---

# 2. Ambiente Experimental

* Software: Visual Studio Code (VSCode).

* Linguagem: Python 3.14.2

* Modelo: Produtor-Consumidor com buffer limitado.

* Hardware: Máquina com múltiplos núcleos.

| Item                        |   Descrição   |
| --------------------------- |   ---------   |
| Processador                 |    i5-12500   |
| Número de núcleos           |       6       |
| Memória RAM                 |     16gb      |
| Sistema Operacional         |  Windows 11   |
| Linguagem utilizada         |    Python     |
| Biblioteca de paralelização |multiprocessing|
| Compilador / Versão         | VScode 1.112.0|

---

# 3. Metodologia de Testes

* Utilizou-se a função time.time() para capturar o instante inicial e final da execução, calculando a diferença como tempo total.

* Foi realizada uma execução para cada configuração de paralelismo (2, 4, 8 e 12 processos), para a entrada de 1000 arquivos, totalizando 10 milhões de linhas, 200 milhões de palavras e 1,36 bilhão de caracteres.

### Configurações testadas

Os experimentos foram realizados nas seguintes configurações:

* 1 thread/processo (versão serial)
* 2 threads/processos
* 4 threads/processos
* 8 threads/processos
* 12 threads/processos

### Procedimento experimental

* A máquina foi dedicada, pois durante os testes não havia outros programas pesados em execução, garantindo que o processador estivesse focado na tarefa.

* Carga do sistema: mínima, apenas serviços essenciais do sistema operacional.

* Arquivos de teste: pasta log2, contendo 1000 arquivos de log.

---

# 4. Resultados Experimentais


| Nº Threads/Processos | Tempo de Execução (s) |
| -------------------- | --------------------- |
| 1                    |          115          |
| 2                    |          56           |
| 4                    |          31           |
| 8                    |          21           |
| 12                   |          19           |

---

# 5. Cálculo de Speedup e Eficiência

## Fórmulas Utilizadas

### Speedup

```
Speedup(p) = T(1) / T(p)
```

Onde:

* **T(1)** = tempo da execução serial
* **T(p)** = tempo com p threads/processos

### Eficiência

```
Eficiência(p) = Speedup(p) / p
```

Onde:

* **p** = número de threads ou processos

---

# 6. Tabela de Resultados

Preencha a tabela abaixo utilizando os tempos medidos.

| Threads/Processos | Tempo (s) | Speedup | Eficiência |
| ----------------- | --------- | ------- | ---------- |
| 1                 |    115    |   1.0   |    1.0     |
| 2                 |    56     |   2.1   |    1.03    |
| 4                 |    31     |   3.7   |    0.93    |
| 8                 |    21     |   5.5   |    0.68    |
| 12                |    19     |   6.1   |    0.50    |

---

# 7. Gráfico de Tempo de Execução

* Eixo X: número de threads/processos
* Eixo Y: tempo de execução (segundos)

![Gráfico Tempo Execução](C:\Users\nayla\OneDrive\Documentos\Unieuro\Semestre4\PROGCD\Atividade3\tempo_execucao.png)

---

# 8. Gráfico de Speedup

* Eixo X: número de threads/processos
* Eixo Y: speedup

![Gráfico Speedup](C:\Users\nayla\OneDrive\Documentos\Unieuro\Semestre4\PROGCD\Atividade3\speedup.png)

---

# 9. Gráfico de Eficiência

Construa um gráfico mostrando a **eficiência da paralelização**.

## Orientações

* Eixo X: número de threads/processos
* Eixo Y: eficiência
* Valores entre 0 e 1

Inserir o gráfico abaixo:

![Gráfico Eficiência](C:\Users\nayla\OneDrive\Documentos\Unieuro\Semestre4\PROGCD\Atividade3\eficiencia.png)

---

# 10. Análise dos Resultados

* O speedup foi próximo do ideal apenas até 4 threads, a partir daí, a curva se distancia significativamente da linha linear, atingindo apenas $6,1\times$ com 12 threads.
* A aplicação apresenta uma escalabilidade sublinear, continua ganhando desempenho com mais threads, mas o esforço para adicionar novas threads não traz um retorno proporcional. Entre 8 e 12 threads há indícios de que a aplicação está próxima do seu limite de saturação.
* A máquina utilizada possui 6 núcleos, sendo menor que o número de threads. Enquanto com 2 threads a eficiência foi de 103%, com 4 threads ela caiu para 93%, e com 12 threads despencou para 50%, indicando múltiplos processos competindo pelos mesmos recursos de memória e cache do processador o que reduz a eficiência.
* Houve um overhead claro. O custo de gerenciar 12 threads (criação, troca de contexto e destruição) começou a rivalizar com o tempo de execução do próprio processamento.
* Se as threads precisam atualizar uma estrutura de dados, como um contador global de arquivos processados ou uma lista de resultados, elas precisam de locks/mutexes.
* Ao tentar adquirir o mesmo lock ao mesmo tempo, as 12 threads ficam bloqueadas, gerando espera e reduzindo a eficiência.
* Se o "Avaliador de Arquivos" lê arquivos do disco, o limite de velocidade do SSD/HD pode ter sido atingido. Adicionar mais threads não faz o disco ler mais rápido; elas apenas ficam na fila de espera do sistema de arquivos.
* Caso diferentes threads tentem escrever em variáveis que residem na mesma linha de cache, o processador precisa invalidar e atualizar o cache constantemente entre os núcleos, degradando a performance.
* Com 12 threads ativas, o Sistema Operacional precisa alternar constantemente qual thread está usando a CPU. Cada troca de contexto exige salvar e carregar registradores, o que consome ciclos de CPU que poderiam estar sendo usados para avaliar arquivos.

---

# 11. Conclusão

* O paralelismo trouxe ganho significativo de desempenho, uma vez que o tempo de execução caiu de ~115 segundos (serial) para ~56 segundos com 2 processos, representando um ganho expressivo, portanto o melhor equilíbrio entre tempo e eficiência foi obtido com 2 processos, aproveitando todos os núcleos físicos da máquina sem sobrecarga excessiva e obtendo uma eficiência de 103%.
* O programa escala muito bem até 4 threads acompanha de perto a linha ideal, mas depois disso a escalabilidade começa a sofrer reduzindo a eficiência drasticamente, pois ao aumentar de 8 para 12 threads o tempo só caiu de 21s para 19s (uma melhora de apenas ~10%). Isso indica que o sistema está atingindo um gargalo de contenção ou que a parte serial do código está começando a dominar o tempo de execução.
* Para melhorar a escalabilidade e evitar a queda de eficiência em contagens maiores de threads, poderia ser realizado:
- Implementação de I/O assíncrono para reduzir gargalos de leitura/escrita em disco.
- Balanceamento de Carga (Load Balancing), usar uma fila dinâmica de tarefas (Task Queue) costuma ser mais eficiente do que dividir os arquivos estaticamente.
- Redução de Regiões Críticas, acrescentando algum Lock ou Mutex global (como um contador de progresso ou log de resultados) que force as threads a esperarem umas pelas outras.
- Ajuste de granularidade, pois se o trabalho de cada thread for muito pequeno, o custo de criar e gerenciar a thread pode ser maior que o ganho. Agrupar pequenos arquivos para serem processados em lotes por uma única thread pode melhorar a eficiência.

---
