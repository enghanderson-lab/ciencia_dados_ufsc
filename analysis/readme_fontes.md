# Fontes de dados — `analise_viamar.ipynb`

Todas as fontes usadas no notebook, com URL de origem, cobertura e como cada uma entra na
análise. Janela de referência do notebook: **2022–2025** (4 anos completos); todo comparativo de
variação/crescimento usa o mesmo ano de início e o mesmo ano de fim dos dois lados. Arquivos
ficam em `../dados`. Para as fontes usadas em `analise_preliminar/analise_preliminar.ipynb`
(que ainda cobre 2024–2026) e para os gaps de coleta em detalhe, ver também `dados/README.md`.

## 1. Acidentes — PRF, dados abertos

Origem: [Dados Abertos da PRF](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-acidentes) (planilhas hospedadas em Google Drive, extraídas em jul/2026).

| Arquivo | Granularidade | Cobertura |
|---|---|---|
| `datatran2022.csv` … `datatran2025.csv` | 1 linha = 1 acidente | Anos completos 2022–2025 |
| `acidentes2022_pessoas.csv` … `acidentes2025_pessoas.csv` | 1 linha = 1 pessoa/veículo envolvido | Anos completos 2022–2025, usado na seção 5 (tipo de veículo) |

`datatran2026.csv`, `acidentes2026.csv` e `acidentes2026_todas_causas_tipos.csv` (2026 parcial)
continuam em `dados/` para uso do `analise_preliminar.ipynb`, mas **não entram** em
`analise_viamar.ipynb` — o notebook restringe tudo a anos completos dentro de 2022–2025.

## 2. Volume de tráfego (VMD/VMDa) — DNIT PNCT

Origem: [DNIT — Contagem de Tráfego](https://servicos.dnit.gov.br/dadosabertos/dataset/contagem-de-trafego)

| Arquivo | Ano-base | Uso |
|---|---|---|
| `vmda2022_snv_202301b.xlsx` | Dez/2022 | Único ano de VMD dentro da janela 2022–2025; correlacionado com densidade de acidentes por trecho (seção 2) |
| `contagem_trafego_2020.csv` | Dez/2020 | Fora da janela — mantido só para a tabela de Nível de Serviço (NS), rotulada explicitamente como contexto pré-2022, não comparada como par |

**Gap confirmado em jul/2026:** o DNIT não publicou VMD/VMDa para o corredor após dezembro/2022
(checado no portal de dados abertos e no PNCT — `servicos.dnit.gov.br/dadospnct`). Não existe,
portanto, um segundo ponto dentro de 2022–2025 para medir crescimento de tráfego pareado com os
acidentes; a seção 2 usa o VMDa 2022 como snapshot único.

## 3. Frota de veículos — SENATRAN

| Arquivo | Origem | Cobertura | Formato |
|---|---|---|---|
| `frota-dezembro2022.xlsx` | [SENATRAN, dez/2022](https://www.gov.br/transportes/pt-br/assuntos/transito/arquivos-senatran/estatisticas/renavam/2022/dezembro/g_frota_por_uf_municipio_tipo_especie_eixos_dezembro_2022.xlsx) | Todos os municípios do Brasil, por tipo/espécie/eixos | "Longo" — 1 linha por UF/município/tipo/espécie/eixos, precisa agregar por município |
| `FrotaporMunicipioetipoDEZEMBRO2025.xlsx` | [SENATRAN, dez/2025](https://www.gov.br/transportes/pt-br/assuntos/transito/conteudo-Senatran/arquivos-renavam-2025/FrotaporMunicipioetipoDEZEMBRO2025.xlsx) | Todos os municípios, por tipo de veículo | "Largo" — 1 linha por município, coluna `TOTAL` já soma os tipos |
| `frota-dezembro2020.xls` | SENATRAN, dez/2020 | Idem 2025 | Não usado em `analise_viamar.ipynb` (fora da janela 2022–2025); mantido só para `analise_preliminar` |

**Nota de qualidade corrigida:** os dois arquivos grafam "Balneário Piçarras" de formas
diferentes — SENATRAN usa "Balneário **de** Piçarras", enquanto PRF/IBGE usam "Balneário
Piçarras" (sem "de"). Sem tratar esse alias, o município ficava **fora** dos totais de frota em
todas as versões anteriores do notebook (17 de 18 municípios, não 18). `analise_viamar.ipynb`
agora normaliza esse alias antes de somar (`MUNICIPIO_ALIASES` na célula de setup).

## 4. Pendularidade / Censo 2022 — IBGE

| Arquivo | Origem | Conteúdo |
|---|---|---|
| `populacao_corredor.json` | [IBGE — API SIDRA, tabela 4714](https://apisidra.ibge.gov.br/values/t/4714/n6/.../v/93/p/2022) | População residente 2022 (Censo) dos 18 municípios do corredor — snapshot único, usado como proxy de escala urbana/pendularidade (seção 4) |
| `municipios_sc.json` | [IBGE — localidades API](https://servicodados.ibge.gov.br/api/v1/localidades/estados/SC/municipios) | Códigos de município de SC, usado só para montar a consulta à API do SIDRA |

**Gap:** não há tabela SIDRA de deslocamento pendular por município do Censo 2022 disponível via
API pública (só PNAD Contínua em nível Brasil/UF/RM, ou Censo 2010). O argumento de pendularidade
na seção 4 usa como proxy o horário de pico dos próprios acidentes, não fluxo origem-destino.

## 5. Movimentação portuária — ANTAQ

**Gap:** portal ANTAQ (`estatistica.antaq.gov.br`) é um dashboard Qlik Sense sem endpoint de
download simples; `dados.gov.br/dataset/movimentacao-carga` retorna 401. Os números de tonelagem
por porto catarinense citados na seção 5 (São Francisco do Sul, Itapoá, Portonave, Imbituba,
Itajaí) vêm de notícias oficiais ANTAQ/SPAF-SC, **não** de série bruta — usados só como contexto,
não entram em nenhum cálculo.

## 6. Qualidade do pavimento — CNT

| Arquivo | Origem | Nível de detalhe |
|---|---|---|
| `cnt_pesquisa_rodovias_2025.pdf` | [CNT — Pesquisa de Rodovias 2025 (síntese)](https://repositorio.itl.org.br/jspui/bitstream/123456789/847/1/Pesquisa%20CNT%20de%20Rodovias%202025%20-%20S%C3%ADntese%20dos%20resultados%20-%20nacional,%20por%20regi%C3%A3o%20e%20por%20estados.pdf) | Apenas nível estadual (SC), não por trecho — snapshot único, usado como teto/contexto (seção 6) |

## Resumo dos gaps que seguem em aberto

1. VMD/tráfego "vivo" do corredor: sem ponto DNIT posterior a dez/2022 dentro da janela 2022–2025.
2. Pendularidade Censo 2022: sem tabela SIDRA municipal (mitigado com horário de pico dos acidentes).
3. Cargas portuárias: sem série ANTAQ auditável (contexto qualitativo apenas).
4. Pavimento: só nível estadual, não por trecho.
5. Correlação volume x densidade de acidentes por trecho é fraca (r ≈ 0,22) — o dado por si só não
   isola causa.
