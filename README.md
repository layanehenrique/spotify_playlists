# Curadoria editorial de playlists no Spotify: análise de valor e performance

> Um diagnóstico orientado a dados sobre como a curadoria editorial humana de playlists influencia o sucesso de uma música no Spotify.

![Status](https://img.shields.io/badge/Status-concluído-1DB954)
![Ferramentas](https://img.shields.io/badge/Ferramentas-Google%20Sheets%20%7C%20Looker%20Studio-1DB954)

**🔗 [Acessar o dashboard interativo](https://datastudio.google.com/reporting/cb713889-17cd-494d-944a-75b61d4a4f06/page/81E5F)**

---

## Sobre o projeto

O caso trata de um questionamento da diretoria que afirma que o investimento no time de curadoria editorial do Spotify é alto, mas que os resultados de streams por playlist variam muito de um lançamento para outro. Alguns sucessos viram exemplo em apresentações internas, mas ninguém conseguia responder **com dados** se isso é sorte, tendência de mercado, ou se a curadoria realmente gera esse retorno.

Esse projeto nasceu de uma thread de e-mail entre a VP de Produto, o Head de Curadoria Editorial e a Head de Analytics: a diretoria pediu uma posição fundamentada em dados, para embasar a revisão de orçamento do trimestre seguinte.

---

## Objetivo

Analisar a efetividade do investimento em curadoria editorial humana (playlists) e entender se ela realmente gera retorno para o Spotify. Para  isso, investiguei se a presença de faixas em playlists gera aumento real em volume de streams ou se reflete apenas viés de seleção.

---

## Perguntas de negócio

Priorizadas pela VP de Produto:

| Prioridade | Pergunta |
|---|---|
| 1 | A curadoria gera valor? Músicas presentes em mais playlists do Spotify têm, de fato, mais streams? |
| 2 | Existem sinais precoces de sucesso? Músicas que entram nos charts do Spotify têm mais streams que as que não entram? |
| 3 | Existem padrões (gênero, país, nº de artistas, época de lançamento, multiplataforma) a considerar na estratégia de curadoria? |

---

## Dados

**Bases utilizadas:**
- `track_in_spotify_ativa_BR` — desempenho dentro do Spotify (playlists, charts, streams, gênero, país, data de lançamento)
- `track_in_competition_ativa_BR` — presença em playlists e charts do Apple Music, Deezer e Shazam

**Chave de cruzamento:** `track_id` (join tipo LEFT JOIN via PROCV/VLOOKUP)

**Volume:** ~950 registros, quase 1.000 músicas do catálogo

**Limitações de origem:**
- Sem dado de investimento editorial por faixa
- Sem histórico de série temporal de streams (dia a dia)

---

## Ferramentas e tecnologias

- **Google Sheets** — limpeza, tratamento e exploração dos dados
- **Looker Studio** — construção do dashboard final
- **Funções aplicadas:** `PROCV/VLOOKUP`, `CONT.SE/COUNTIF`, `COUNTBLANK`, `SE/IFS`, `ÚNICO/UNIQUE`, `FILTRO/FILTER`, `MED/MEDIAN`, `CORREL`, `MÁX/MÍN/MÉDIA`, formatação condicional, `CASE WHEN` (Looker Studio)
- **Claude (IA)** — apoio na leitura de requisitos, estruturação da análise e criação de entregáveis

---

## Processamento e limpeza de dados

1. **Cruzamento das bases** via PROCV, usando `track_id` como chave
2. **Tratamento de nulos** — nulos em `main_music_genre`/`main_country` (1 cada), `in_spotify_charts` (4) e `in_shazam_charts` (~50, ~5% da base) mantidos como dado ausente genuíno, não convertidos em zero
3. **Tratamento de duplicatas** — múltiplos `track_id` para a mesma música (remasters, re-releases, múltiplos álbuns), sinalizado como risco de inflar/diluir métricas
4. **Outliers em variáveis categóricas** — padronização de inconsistências (ex.: "PR" → "Puerto Rico", "disco-pop" → "disco pop")
5. **Outliers em variáveis numéricas** — 1 linha inválida (erro de importação) e 1 valor negativo em `streams` removidos; mediana adotada como medida central por conta da distribuição assimétrica de `streams`
6. **Análise exploratória** — campos calculados (`faixa_playlists`, `qtd_plataformas_chart`, `status_chart_spotify`), gráfico de dispersão, tabela dinâmica, correlação de Pearson

---

## Principais resultados

| Pergunta | Resultado |
|---|---|
| Mais playlists → mais streams? | **Sim.** Correlação de Pearson = **0,79** (forte). Mediana de streams cresce ~8x da menor para a maior faixa de playlist (199 mi → 830 mi → 1,69 bi) |
| Playlists garantem entrar em chart? | **Não.** Correlação fraca (0,158), outros fatores pesam mais para o chart |
| Entrar em chart aumenta streams? | **Sim.** Em média, **+77,84%** mais streams |
| Multiplataforma → mais streams? | **Sim**, mas chart não é pré-requisito. 46 músicas fora de qualquer chart somam +100 mi de streams |
| Gênero e país influenciam? | **Sim.** Pop, reggaeton, R&B e K-pop dominam; EUA, Reino Unido, Coreia do Sul, Porto Rico e Colômbia lideram. Provável reflexo de comportamento de consumo |
| Presença no Shazam se relaciona com Spotify? | Relação moderada (Pearson = **0,61**) |
| Época de lançamento influencia? | Ano reflete tempo acumulado, não sucesso real; meses de pico: jan/ago/set/out |

**Conclusão central:** existe uma correlação forte e consistente entre curadoria e sucesso, mas os dados **não permitem confirmar causalidade**. Pode haver reforço mútuo entre curadoria e sucesso orgânico.

---

## Apresentação

15 slides estruturado em narrativa (gancho → contexto → jornada → descoberta → recomendação → riscos), com roteiro de fala cronometrado para 15 minutos.

---

## Limitações

- Não distingue causa de consequência (curadoria gera sucesso, ou reage a ele?)
- Streams são valores acumulados totais — músicas mais antigas têm vantagem natural na comparação
- ~5% da base sem dado de presença no Shazam, sem padrão de causa identificado
- Diagnóstico inicial, não definitivo

## Próximos passos

Recomenda-se priorizar, junto ao time de Data Engineering, a coleta de:
1. Histórico de streams ao longo do tempo
2. Data de entrada em playlist
3. Picos sazonais de streams

Isso permitiria uma análise de "antes x depois" para isolar causalidade entre curadoria e sucesso.

---

## Estrutura do repositório

```
├── dados/
│   ├── track_in_spotify_ativa_BR.csv
│   └── track_in_competition_ativa_BR.csv
├── docs/
│   ├── ficha_tecnica.docx
│   └── apresentacao.pptx
├── dashboard/
│   └── link_looker_studio.md
└── README.md
```
---

## Autoria

Projeto desenvolvido por **Layane**, como parte do programa de formação em análise de dados (Laboratória).

---

- **Referência externa:** [Music streaming stats — Exploding Topics](https://explodingtopics.com/blog/music-streaming-stats)
