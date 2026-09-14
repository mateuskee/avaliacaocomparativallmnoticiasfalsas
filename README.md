# Avaliação Comparativa de LLMs para Classificação de Notícias Falsas em Português do Brasil

Este repositório reúne o código e os materiais complementares utilizados no Trabalho de Conclusão de Curso **“Avaliação Comparativa de Grandes Modelos de Linguagem (LLMs) para Classificação de Notícias Falsas em Português do Brasil”**, desenvolvido no curso de Engenharia da Computação da Universidade Federal do Pará (UFPA).

## Objetivo

O trabalho avalia comparativamente diferentes Modelos de Linguagem de Grande Escala (LLMs) na classificação binária de notícias falsas em português brasileiro, considerando diferentes estratégias de prompt e dois corpora.

A tarefa utiliza a seguinte convenção:

- `0` = notícia verdadeira
- `1` = notícia falsa

A notícia falsa (`1`) é considerada a classe positiva nas métricas de classificação.

## Modelos avaliados

Foram avaliadas quatro configurações disponibilizadas por meio da API da DeepInfra:

- `meta-llama/Meta-Llama-3.1-8B-Instruct-Turbo`
- `meta-llama/Llama-3.3-70B-Instruct-Turbo`
- `Qwen/Qwen3-32B`
- `Qwen/Qwen3-235B-A22B-Instruct-2507`

O **Qwen3-32B** foi executado em modo *non-thinking* por meio da instrução `/no_think`.

## Conjuntos de dados

### Fake.br

O notebook obtém o corpus a partir do repositório público:

`https://github.com/roneysco/Fake.br-Corpus`

Foram utilizados os textos completos, totalizando:

- 3.600 notícias falsas
- 3.600 notícias verdadeiras
- 7.200 documentos

### FakeTrueBR

O notebook obtém o corpus a partir do repositório público:

`https://github.com/jpchav98/FakeTrue.Br`

Foram utilizados:

- 1.791 notícias falsas
- 1.791 notícias verdadeiras
- 3.582 documentos

Os corpora são mantidos em sua composição original. O código realiza verificações de integridade, padroniza os registros e remove apenas entradas sem texto quando necessário.

## Estratégias de prompt

O experimento utiliza quatro estratégias em regime *zero-shot*:

- **P1 — Instrução direta:** solicita diretamente a classificação binária.
- **P2 — Critérios linguísticos e textuais:** acrescenta critérios a serem considerados em conjunto.
- **P3 — Critérios com calibração:** adiciona orientações para evitar que características isoladas sejam interpretadas automaticamente como evidência de veracidade ou falsidade.
- **P4 — Estruturação em XML:** reorganiza as orientações do P3 em blocos estruturados.

Os textos completos dos quatro prompts estão disponíveis em [`prompts/prompts.md`](prompts/prompts.md).

## Configuração experimental

As principais condições utilizadas foram:

- regime: `zero-shot`
- temperatura: `0`
- máximo de tokens de saída: `16`
- workers concorrentes: `4`
- checkpoint: a cada `25` resultados
- máximo de tentativas para falhas temporárias da API: `5`
- sem busca externa
- sem RAG
- sem ferramentas adicionais durante a inferência

Cada notícia foi enviada individualmente ao modelo juntamente com uma das estratégias de prompt. O rótulo real não foi fornecido ao modelo durante a inferência.

O experimento completo corresponde a:

- 2 corpora
- 4 modelos
- 4 estratégias de prompt
- 10.782 textos
- **172.512 inferências**

## Tratamento das respostas

Embora os prompts solicitem apenas `0` ou `1`, o código preserva a resposta original do modelo e aplica um procedimento automático para extrair o rótulo previsto.

São consideradas interpretáveis as respostas que apresentam `0` ou `1` ao final da saída, admitindo espaços em branco e um ponto opcional após o rótulo.

Respostas sem rótulo interpretável:

- são preservadas nos registros;
- são contabilizadas separadamente;
- não participam do cálculo das métricas de classificação.

## Métricas

O desempenho é avaliado por meio de:

- Acurácia
- Precisão
- Recall
- F1-score

As métricas são calculadas separadamente para cada combinação de:

`corpus × modelo × estratégia de prompt`

## Estrutura do repositório

```text
tcc-fake-news-llms/
├── README.md
├── requirements.txt
├── .gitignore
├── tcc_fake_news_codigo_apendice_final.ipynb
└── prompts/
    └── prompts.md
```

## Ambiente de execução

O notebook foi desenvolvido para execução no **Google Colab** e utiliza o **Google Drive** para armazenamento persistente dos resultados.

Por padrão, os arquivos são armazenados em:

```text
/content/drive/MyDrive/tcc_fake_news_resultados
```

## Dependências

As principais bibliotecas Python estão listadas em [`requirements.txt`](requirements.txt).

No Google Colab, várias dessas dependências já estão instaladas. O notebook instala explicitamente a biblioteca `openai` quando necessário.

## Como executar

1. Abra `tcc_fake_news_codigo_apendice_final.ipynb` no Google Colab.
2. Execute as células em ordem.
3. Autorize a montagem do Google Drive.
4. O notebook fará o download dos corpora Fake.br e FakeTrueBR a partir dos respectivos repositórios públicos.
5. Quando solicitado, informe sua própria chave da API da DeepInfra.
6. Verifique as configurações experimentais antes de iniciar novas chamadas.
7. Para executar as inferências, altere:

```python
EXECUTAR_EXPERIMENTO = False
```

para:

```python
EXECUTAR_EXPERIMENTO = True
```

> **Atenção:** a execução completa envolve um grande número de chamadas à API e pode gerar custos e demandar tempo significativo de processamento.

O código possui mecanismo de checkpoint e retomada para evitar a repetição de inferências já concluídas.

## Segurança

Nenhuma chave de API deve ser armazenada no repositório.

O notebook solicita a chave da DeepInfra de forma interativa por meio de `getpass()`.

Antes de publicar alterações no GitHub, verifique se não existem:

- chaves de API;
- tokens de acesso;
- arquivos de credenciais;
- resultados contendo informações que não devam ser publicados.

## Reprodutibilidade

O código disponibilizado corresponde à versão consolidada do procedimento experimental utilizado no TCC.

Resultados obtidos por APIs de terceiros podem sofrer alterações futuras em razão de mudanças de infraestrutura, versões de modelos ou disponibilidade dos serviços. Por esse motivo, os resultados apresentados no trabalho devem ser interpretados considerando as configurações e versões efetivamente utilizadas durante o experimento.

## Autor

**Mateus do Carmo Matos**  
Engenharia da Computação  
Universidade Federal do Pará — UFPA

## Orientação

**Marcos Cesar da Rocha Seruffo** e 
**Adriano Madureira Dos Santos**

## Uso acadêmico

Este repositório é disponibilizado como material complementar ao Trabalho de Conclusão de Curso. Caso o código ou a metodologia sejam utilizados em outros trabalhos acadêmicos, recomenda-se citar o TCC correspondente.
