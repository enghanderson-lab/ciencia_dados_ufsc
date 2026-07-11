# Dados — Diagnóstico BR-101 / ViaMar

Esta pasta reúne todos os dados brutos usados no diagnóstico original (`analise_preliminar/analise_preliminar.ipynb`)
e na extensão feita para testar a tese "saturação estrutural x sazonalidade turística"
(`analysis/analise_viamar.ipynb`). As duas análises estão consolidadas em
`relatorio_br101_viamar/relatorio.html` (seções 1–7 = diagnóstico original, seções 8–9 = extensão + veredito).

Convenção: `dados/` é a pasta de dados brutos do projeto (equivalente ao `data/raw` do prompt original).
Mantida em português/plana para não quebrar o notebook de diagnóstico já existente.

## 1. Acidentes — PRF, dados abertos

| Arquivo | Origem | Cobertura |
|---|---|---|
| `datatran2024.csv`, `datatran2025.csv`, `datatran2026.csv` | [PRF — Dados Abertos](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-acidentes) | Um registro por acidente, 2024–2026 (2026 parcial) |
| `acidentes2026.csv` | Idem (detalhe pessoa/veículo) | Um registro por pessoa envolvida, 2026 |
| `acidentes2026_todas_causas_tipos.csv` | Idem (schema estendido: múltiplas causas/tipos por acidente) | 2026, ~jan-dez (parcial), mesma fonte, schema alternativo (`cd_bat`/`rodovia`/`uf_acidente` em vez de `id`/`br`/`uf`) — mantido como redundância/checagem, não usado como fonte primária |

## 2. Volume de tráfego (VMD/VMDa) — DNIT PNCT

Fonte: [DNIT — Contagem de Tráfego](https://servicos.dnit.gov.br/dadosabertos/dataset/contagem-de-trafego)

| Arquivo | Ano-base | Estrutura | Observação |
|---|---|---|---|
| `vmda2022_snv_202301b.xlsx` | Dez/2022 | tem `vl_km_inic`/`vl_km_fina` por trecho → **permite join direto por km com os acidentes** | fonte principal do cruzamento volume × acidentes |
| `vmda2021_snv.xlsx` | Dez/2021 | schema diferente (`CODIGO_BR`, `EXTENSAO`, sem km_inic/km_fina) | usado só para tendência agregada de VMD, não para join por km |
| `contagem_trafego_2020.csv` | Dez/2020 | mesmo schema do 2022 (com km_inic/km_fina) e já traz `NS_C`/`NS_D` (Nível de Serviço) | também permite join por km |

## 3. Frota de veículos

| Arquivo | Origem | Cobertura |
|---|---|---|
| `FrotaporMunicipioetipoDEZEMBRO2025.xlsx` | [SENATRAN](https://www.gov.br/transportes/pt-br/assuntos/transito/conteudo-Senatran/arquivos-renavam-2025/FrotaporMunicipioetipoDEZEMBRO2025.xlsx) | Dez/2025, todos os municípios do Brasil, por tipo de veículo |
| `frota-dezembro2020.xls` | [SENATRAN/DENATRAN](https://www.gov.br/transportes/pt-br/assuntos/transito/arquivos-senatran/estatisticas/renavam/2020/dezembro/frota-reg-munic-tipo-modelo-07-dezembro2020.xls) | Dez/2020, mesma estrutura — par usado para medir crescimento de frota em 5 anos |

**Gap não coberto:** DETRAN-SC (`detran.sc.gov.br/estatisticas-veiculos-transparencia`) não expõe os arquivos de estatística
por link direto navegável por scraping (menu carregado via JS); os dois snapshots SENATRAN acima já cobrem o objetivo
("frota cresce mais ou menos que os acidentes") em nível de município, então não foi perseguido manualmente.

## 4. Estudos de tráfego / nível de serviço — ANTT/Arteris

| Arquivo | Origem | Conteúdo real |
|---|---|---|
| `Estudos de Tráfego.pdf` | [ANTT](https://antt.gov.br/documents/359170/710124/Estudos+de+Tr%C3%A1fego.pdf) | Estudo de 2019 para a **concessão do trecho sul** da BR-101/SC (km 244,7–465, Palhoça↔divisa RS), não o corredor Joinville–Palhoça. Útil como contexto qualitativo (rede viária, matriz de cargas, portos) mas não tem VMD do corredor de interesse. |
| `arteris_relatorio_mensal.pdf` | [ANTT](https://antt.gov.br/documents/359170/064ae541-9809-2408-2864-1fccb0b8e978) | Na verdade é um **relatório de obras de fevereiro/2015** do Contorno Rodoviário de Florianópolis (km 211–215), não um relatório mensal de VMD/nível de serviço atual. Confirma que o hotspot km 190–220 já era foco de obra de capacidade há 10 anos. Não usado quantitativamente. |

**Gap:** não foi encontrado relatório mensal atual (VMD/nível de serviço mês a mês da concessionária) via link
público direto — a ANTT publica isso por período em painéis de difícil acesso programático. O cruzamento
volume × acidentes do item 2 (DNIT) supre a mesma necessidade analítica.

## 5. Pendularidade / Censo 2022 — IBGE

| Arquivo | Origem | Conteúdo |
|---|---|---|
| `ibge_censo2022_deslocamentos.pdf` | [IBGE — release Censo 2022](https://agenciadenoticias.ibge.gov.br/media/com_mediaibge/arquivos/eb6833eb8d1aa7d4f77b9b38cd20fbdb.pdf) | Release nacional sobre deslocamento para trabalho/estudo no Censo 2022 (números nacionais/estaduais/RM) |
| `municipios_sc.json` | [IBGE — localidades API](https://servicodados.ibge.gov.br/api/v1/localidades/estados/SC/municipios) | Códigos de município (7 dígitos) de SC, usado só para mapear os 18 municípios do corredor para a API do SIDRA |
| `populacao_corredor.json` | [IBGE — API SIDRA, tabela 4714](https://apisidra.ibge.gov.br/values/t/4714/n6/.../v/93/p/2022) | População residente 2022 (Censo) dos 18 municípios do corredor — usado como proxy de escala urbana/pendularidade (ver item 5) |

**Gap importante:** a tabela SIDRA com deslocamento pendular **por município** (quem mora em Joinville e trabalha
em outro município do corredor, etc.) do Censo 2022 não está disponível via API pública do SIDRA no momento da
coleta — as tabelas de deslocamento indexadas no catálogo do IBGE (1977, 2143, 3422, 3604, 8109–8114...) são todas
da PNAD Contínua (nível Brasil/UF/RM, não município) ou do Censo 2010. O detalhamento municipal do Censo 2022
existe apenas via microdados (`basedosdados.org`, tabela `08a1546e...`, acesso BigQuery) — não baixado aqui por
exigir autenticação/billing de uma conta BigQuery. Usamos os números estaduais/nacionais do release como contexto,
e o **perfil horário de pico (7–8h/17–19h)** já presente nos dados de acidentes como proxy direto de pendularidade.

## 6. Movimentação portuária — ANTAQ

**Gap:** o portal ANTAQ (`estatistica.antaq.gov.br`, `anuario.antaq.gov.br`) é um dashboard Qlik Sense
interativo sem endpoint de download direto acessível por scraping simples. `dados.gov.br/dataset/movimentacao-carga`
retornou HTTP 401. Não foi baixada série histórica granular por porto. Usamos como contexto qualitativo os números
de tonelagem por porto catarinense veiculados em notícias oficiais recentes da ANTAQ/SPAF-SC (ver seção 8.4 de
`relatorio_br101_viamar/relatorio.html`), com a ressalva explícita de que não são dados brutos auditáveis.

## 7. Qualidade do pavimento — CNT

| Arquivo | Origem | Nível de detalhe |
|---|---|---|
| `cnt_pesquisa_rodovias_2025.pdf` | [CNT — Pesquisa de Rodovias 2025 (síntese)](https://repositorio.itl.org.br/jspui/bitstream/123456789/847/1/Pesquisa%20CNT%20de%20Rodovias%202025%20-%20S%C3%ADntese%20dos%20resultados%20-%20nacional,%20por%20regi%C3%A3o%20e%20por%20estados.pdf) | Apenas nível **estadual** (Santa Catarina: 16,5% "ótimo"), não por trecho/km. Não permite isolar a qualidade do pavimento especificamente no corredor Garuvá–Palhoça. |

## Resumo dos gaps (para leitura crítica do resultado)

1. Frota: sem série DETRAN-SC município a município (mitigado com par SENATRAN 2020/2025).
2. VMD/nível de serviço "vivo" da concessionária: sem relatório mensal atual (mitigado com VMD DNIT 2020/2021/2022).
3. Pendularidade Censo 2022: sem tabela SIDRA municipal (mitigado com perfil horário de pico dos próprios acidentes + contexto nacional).
4. Cargas portuárias: sem série ANTAQ auditável (usado só como contexto qualitativo, não entra nos cálculos).
5. Qualidade do pavimento: só nível estadual, não por trecho (usado como teto/contexto, não como variável por km).
