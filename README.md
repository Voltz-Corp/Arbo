# 🌳 Arbo - Plataforma de Monitoramento e Engajamento em Arborização Urbana

> Conectando cidadãos e gestão pública para um Recife mais verde.

O **Arbo** é uma solução digital integrada (Dashboard + App) desenvolvida para enfrentar os desafios da arborização urbana na cidade do Recife. A plataforma combina análise de dados geoespaciais, inteligência artificial e ciência cidadã para monitorar a cobertura vegetal, identificar ilhas de calor e gerenciar riscos de queda de árvores.

## 📑 Índice

1. [Sobre o Projeto](#-sobre-o-projeto)
2. [Funcionalidades Chave](#-funcionalidades-chave)
3. [Arquitetura e Tecnologias](#-arquitetura-e-tecnologias)
4. [Ciência de Dados e Pipeline](#-ciência-de-dados-e-pipeline)
5. [Instalação e Execução](#-instalação-e-execução)
6. [Estrutura do Repositório](#-estrutura-do-repositório)
7. [Equipe](#-equipe)

## 🎯 Sobre o Projeto

A arborização urbana é vital para a qualidade de vida, especialmente em cidades tropicais como Recife. O **Arbo** atua em duas frentes:

1. **Para o Gestor Público:** Um dashboard analítico que consolida dados do censo arbóreo, permitindo visualizar déficits de cobertura, saúde das espécies e eficiência das equipes de manutenção.
2. **Para o Cidadão:** Uma ferramenta de engajamento que permite reportar problemas (podas, riscos), visualizar a "nota verde" do seu bairro e receber insights educativos sobre a flora local.

**Diferenciais:**

* Uso de **IA Generativa (Gemini)** para gerar insights automáticos sobre os dados.
* Tratamento avançado de dados do Censo Arbóreo (Imputação via KNN).
* Visualização de dados georreferenciados de alta performance.

## 🚀 Funcionalidades Chave

### 📊 Dashboard de Gestão

* **KPIs em Tempo Real:** Monitoramento de chamados abertos, riscos iminentes e tempo médio de atendimento.
* **Mapa de Calor (Heatmap):** Visualização da densidade arbórea e clusters de incidentes por bairro/RPA.
* **Análise Biométrica:** Gráficos sobre porte, altura e diâmetro das árvores da cidade.

### 📱 Engajamento Cidadão

* **Reporte de Incidentes:** Interface intuitiva para registrar solicitações de poda, denúncias de corte ou risco de queda.
* **Geolocalização:** Marcação precisa de ocorrências no mapa da cidade.

### 🤖 Arbo IA

* **Insights Automatizados:** O sistema analisa os dados tabulares e gera resumos textuais inteligentes (ex: "O bairro Boa Viagem apresenta um déficit de 15% na cobertura em relação à média da cidade").

## 🛠 Arquitetura e Tecnologias

O projeto segue uma arquitetura moderna baseada em microsserviços e contêineres.

### **Frontend (Web/Mobile Responsive)**

* **Framework:** React (Vite) + TypeScript
* **UI/UX:** Tailwind CSS, Shadcn UI
* **Mapas:** Leaflet + React Leaflet
* **Visualização de Dados:** Recharts

### **Backend (API)**

* **Runtime:** Node.js (Express)
* **Banco de Dados:** PostgreSQL (via Supabase)
* **IA:** Integração com Google Gemini API
* **Serviços:** Processamento de GeoJSON, Gestão de Chamados

### **Data Science & Engenharia**

* **Linguagem:** Python 3.10+
* **Bibliotecas:** Pandas, GeoPandas, Scikit-learn (KNN Imputer/Classifier)
* **Formato de Dados:** GeoJSON, H5, CSV Otimizado

### **Infraestrutura**

* **Containerização:** Docker & Docker Compose

## 📈 Ciência de Dados e Pipeline

A inteligência do Arbo reside no tratamento rigoroso dos dados brutos do censo. O fluxo de dados (`ETL`) é executado da seguinte forma:

1. **Ingestão:** Dados brutos do `censo_arboreo.geojson` (Open Data Recife).
2. **Limpeza e Imputação (Python):**
   * Remoção de colunas legadas.
   * **Tratamento de Missing Values:** Uso do algoritmo **KNN Imputer** para estimar altura e copa de árvores com dados faltantes (zeros), baseando-se em vizinhos similares.
   * **Classificação de Espécies:** Uso de **KNN Classifier** para inferir espécies não identificadas com base na biometria.
3. **Otimização:**
   * Exportação de CSVs agregados (`bairro_stats.csv`) para leitura rápida pelo Frontend.
   * Amostragem estatística para renderização performática do Heatmap (`heatmap_data.csv`).

## 💻 Instalação e Execução

### Pré-requisitos

* Docker e Docker Compose instalados.
* Chave de API do Google Gemini (para funcionalidades de IA).

### Passo a Passo

1. **Clone o repositório:**

```bash
git clone https://github.com/voltz-corp/arbo.git
cd arbo
```

2. **Configuração de Ambiente:**

Crie os arquivos `.env` baseados nos exemplos:

```bash
cp .env.example .env
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
```

*Edite o arquivo `backend/.env` e insira sua `GEMINI_API_KEY`.*

3. **Processamento de Dados (Primeira Execução):**

Antes de subir a aplicação, processe os dados brutos para gerar os CSVs otimizados:

```bash
cd data
pip install -r requirements.txt
python export_to_csv.py
cd ..
```

4. **Executar com Docker (Recomendado):**

Utilize o Makefile para subir todo o ambiente:

```bash
make up
```

Ou via Docker Compose diretamente:

```bash
docker-compose up --build
```

5. **Acessar a Aplicação:**

* **Frontend:** http://localhost:5173
* **Backend API:** http://localhost:3000

## 📂 Estrutura do Repositório

```text
arbo/
├── backend/                # API Node.js Express
│   ├── models/             # Modelos de dados (Incident, etc.)
│   ├── gemini-service.js   # Integração com IA
│   └── index.js            # Entrypoint da API
├── data/                   # Pipeline de Ciência de Dados
│   ├── censo_arboreo.geojson # Dados Brutos
│   ├── data-process.ipynb    # Notebook de Análise e Limpeza (EDA)
│   ├── export_to_csv.py      # Script de transformação para Prod
│   └── *.csv                 # Dados processados para o Frontend
├── frontend/               # Aplicação React
│   ├── src/
│   │   ├── components/     # Componentes UI (Mapas, Charts, KPI)
│   │   ├── hooks/          # Hooks customizados (use-incidents, use-ai)
│   │   ├── pages/          # Rotas da aplicação
│   │   └── lib/            # Utilitários (Leaflet, Data parsers)
├── docker-compose.yml      # Orquestração dos containers
└── Makefile                # Atalhos de comando
```

## 🤝 Contribuição

Contribuições são bem-vindas\! Por favor, siga os passos:

1. Faça um Fork do projeto.
2. Crie uma Branch para sua Feature (`git checkout -b feature/IncrivelFeature`).
3. Faça o Commit (`git commit -m 'Add: IncrivelFeature'`).
4. Faça o Push (`git push origin feature/IncrivelFeature`).
5. Abra um Pull Request.

## 📄 Licença

Este projeto está sob a licença GNU General Public License v2.0. Veja o arquivo [LICENSE](/LICENSE.md) para mais detalhes.

## 👥 Equipe

Desenvolvido por [Voltz Corp - Grupo 4](https://github.com/Voltz-Corp) 🚀
Soluções tecnológicas para cidades inteligentes.
