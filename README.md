# 🌍 Pipeline ETL com API REST — RestCountries

> Semana 2 do curso de Análise de Dados · UFSCar  
> Fontes de dados, APIs REST, ETL vs ELT e manipulação com pandas

---

## 📋 Sobre o Projeto

Notebook com pipeline ETL completo que consome a **RestCountries API v3.1**, transforma os dados JSON aninhados em um DataFrame estruturado e exporta os resultados em CSV e JSON.

O projeto percorre todo o fluxo de um pipeline real de dados:

```
Extract ──► Transform ──► Load
   │              │           │
API REST     Flatten JSON   .csv
requests     Tipagem        .json
             Nulos
             Features
```

---

## 🗂️ Estrutura do Repositório

```
.
├── semana2_api_etl.ipynb     ← notebook principal
├── countries_raw.json        ← dados brutos da API (gerado ao rodar)
├── countries_clean.csv       ← dados tratados, Excel-friendly (gerado ao rodar)
├── countries_clean.json      ← dados tratados em JSON (gerado ao rodar)
└── README.md
```

---

## ⚙️ Como Executar

### Opção A — Google Colab (recomendado)

1. Acesse [colab.research.google.com](https://colab.research.google.com)
2. Faça upload do arquivo `semana2_api_etl.ipynb`
3. Execute todas as células: `Runtime → Run all`
4. Nenhuma instalação adicional é necessária

### Opção B — Ambiente local

```bash
# 1. Clone o repositório
git clone https://github.com/aatsac/world-countries-api-eda.git
cd world-countries-api-eda

# 2. Crie e ative um ambiente virtual
python -m venv .venv
source .venv/bin/activate        # Linux/Mac
.venv\Scripts\activate           # Windows

# 3. Instale as dependências
pip install -r requirements.txt

# 4. Abra o notebook
jupyter notebook semana2_api_etl.ipynb
```

---

## 🔌 API Utilizada

**RestCountries v3.1** — [restcountries.com](https://restcountries.com)

| Propriedade | Valor |
|---|---|
| Autenticação | Nenhuma (sem chave) |
| Formato | JSON |
| Endpoint | `GET /v3.1/all` |
| Registros | ~250 países |

Campos extraídos: `name`, `capital`, `region`, `subregion`, `population`, `area`, `languages`, `currencies`, `timezones`, `independent`

---

## 🔄 Pipeline ETL

### 1. Extract
- Chamada HTTP com `requests.get()`
- Inspeção de `status_code`, tempo de resposta e tamanho
- Tratamento de erros com `raise_for_status()`
- Salvamento do JSON bruto antes de qualquer transformação

### 2. Transform
| Etapa | Descrição |
|---|---|
| Flatten | JSON aninhado → dicionário plano via `normalizar_pais()` |
| Tipagem | `pd.to_numeric()`, `.astype('Int64')`, `.astype(bool)` |
| Texto | `.str.strip()`, `.str.title()` |
| Nulos | Identificação por coluna + `.fillna('N/D')` |
| Features | `densidade_hab_km2`, `faixa_pop` |

### 3. Load
- `countries_clean.csv` — encoding `utf-8-sig` (compatível com Excel)
- `countries_clean.json` — `orient='records'`
- Round-trip check com `assert`

---

## 📊 Visualizações

| Gráfico | O que mostra |
|---|---|
| Barras + Pizza | Países por região e status de independência |
| Barras horizontais | Top 15 países mais populosos |
| Scatter (log-log) | Área vs população com países notáveis anotados |
| Barras | Diversidade linguística média por região |

---

## 🧪 Conceitos Praticados

- **API REST** — métodos HTTP, status codes, query parameters
- **`requests`** — `.get()`, `.json()`, `.raise_for_status()`, `timeout`
- **JSON aninhado** — flatten manual com `.get()` encadeado
- **pandas** — `pd.DataFrame`, `.astype()`, `.fillna()`, `.groupby()`, `.nlargest()`
- **ETL** — separação clara entre Extract, Transform e Load
- **ELT** — conceito de salvar o dado bruto antes de transformar

---

## 📚 Referências

- [RestCountries Docs](https://restcountries.com/#api-endpoints-v3)
- [Databricks — ETL vs ELT](https://www.databricks.com/br/discover/etl/vs-elt)
- [Requests — Documentação](https://docs.python-requests.org)
- [Tutorial API/Requests — GeeksforGeeks](https://www.geeksforgeeks.org/software-testing/what-is-an-api/)
- [Notebook de referência — Nesta UK](https://colab.research.google.com/github/nestauk/im-tutorials/blob/3-ysi-tutorial/notebooks/APIs/API_tutorial.ipynb)