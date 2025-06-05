# LattesScraper

Um sistema automatizado para extrair e processar dados de currículos Lattes utilizando web scraping, inteligência artificial e processamento de dados estruturados.

## 📋 Descrição

O LattesScraper é uma ferramenta desenvolvida para automatizar a coleta e processamento de informações acadêmicas da Plataforma Lattes do CNPq. O sistema realiza scraping dos currículos, extrai informações relevantes usando IA, e organiza os dados em formatos estruturados (JSON e Excel) para análise posterior.

### Principais Funcionalidades

- **Web Scraping Automatizado**: Coleta dados de currículos Lattes de forma massiva
- **Contorno de CAPTCHA**: Utiliza API de browser para resolver CAPTCHAs automaticamente
- **Extração via IA**: Processa conteúdo HTML usando modelos de linguagem para extrair informações estruturadas
- **Processamento Concorrente**: Suporte a múltiplas requisições simultâneas com controle de rate limiting
- **Múltiplos Formatos de Saída**: Gera dados em JSON e planilhas Excel organizadas
- **Tratamento de Erros**: Sistema robusto de logging e reprocessamento de falhas

## 🔧 Tecnologias Utilizadas

- **Python 3.13+**: Linguagem principal do projeto
- **Playwright**: Automação de browser para web scraping
- **BeautifulSoup4**: Parsing e manipulação de HTML
- **OpenAI/Instructor**: Extração estruturada via modelos de linguagem
- **Polars**: Processamento eficiente de dados
- **Pydantic**: Validação e serialização de dados
- **XlsxWriter**: Geração de planilhas Excel
- **Tqdm**: Barras de progresso para operações assíncronas

## 📁 Estrutura do Projeto

```
LattesScraper/
├── .venv/                    # Ambiente virtual Python
├── resumes/                  # Arquivos HTML dos currículos coletados
├── jsons/                    # Dados extraídos em formato JSON bruto
├── enriched_jsons/           # Dados processados e enriquecidos via IA
├── excel/                    # Planilhas Excel organizadas por pesquisador
├── failed_requests/          # Logs de requisições com falha
├── scrape.ipynb             # Notebook principal para web scraping
├── parse.ipynb              # Notebook para processamento e extração via IA
├── resumes.txt              # Lista de URLs dos currículos Lattes
├── failed_extractions.txt   # URLs que falharam durante o processo
├── pyproject.toml           # Configuração do projeto e dependências
└── README.md                # Este arquivo
```

## 🚀 Instalação e Configuração

### Pré-requisitos

- Python 3.13 ou superior
- UV (gerenciador de pacotes Python) - recomendado
- Conta no OpenRouter ou provedor de IA compatível
- API de Browser (BrightData recomendado) para contorno de CAPTCHA

### Instalação

1. **Clone o repositório**:

   ```bash
   git clone <url-do-repositorio>
   cd LattesScraper
   ```

2. **Configure o ambiente virtual e instale dependências**:

   ```bash
   uv venv
   source .venv/bin/activate  # Linux/Mac
   # ou .venv\Scripts\activate  # Windows
   uv sync
   ```

3. **Configure as variáveis de ambiente**:

   ```bash
   # Crie um arquivo .env com:
   CDP_URL=sua_url_de_browser_api_aqui
   OPENROUTER_API_KEY=sua_chave_openrouter_aqui
   ```

### Configuração de APIs Externas

#### BrightData Browser API

- Registre-se em [BrightData](https://brightdata.com)
- Configure um Browser API endpoint
- Use a URL CDP fornecida na variável `CDP_URL`

#### OpenRouter API

- Registre-se em [OpenRouter](https://openrouter.ai)
- Obtenha sua chave de API
- Configure na variável `OPENROUTER_API_KEY`

## 📖 Como Usar

### 1. Preparação dos Dados

Crie o arquivo `resumes.txt` com as URLs dos currículos Lattes (uma por linha):

```
http://lattes.cnpq.br/8894044025019842
http://lattes.cnpq.br/6290785760473426
http://lattes.cnpq.br/3201859120142095
```

### 2. Execução do Web Scraping

Execute o notebook `scrape.ipynb` para coletar os dados:

```python
# O notebook irá:
# 1. Ler as URLs do arquivo resumes.txt
# 2. Conectar com a API de browser
# 3. Navegar e resolver CAPTCHAs automaticamente
# 4. Salvar os HTMLs na pasta resumes/
# 5. Registrar falhas no arquivo failed_extractions.txt
```

### 3. Processamento e Extração

Execute o notebook `parse.ipynb` para extrair informações estruturadas:

```python
# O notebook irá:
# 1. Processar arquivos HTML da pasta resumes/
# 2. Usar IA para extrair publicações e bancas acadêmicas
# 3. Salvar dados brutos em jsons/
# 4. Enriquecer dados e salvar em enriched_jsons/
# 5. Gerar planilhas Excel individuais em excel/
```

## 📊 Estrutura dos Dados Extraídos

### Publicações (`Publicacao`)

```python
{
    "ano": int,              # Ano da publicação
    "titulo": str,           # Título da publicação
    "autores": str,          # Lista de autores (separados por vírgula)
    "periodico": str | None, # Nome do periódico
    "editora": str | None,   # Editora ou veículo de publicação
    "cidade": str | None,    # Cidade de publicação
    "extra": str | None      # Informações adicionais
}
```

### Bancas Acadêmicas (`Banca`)

```python
{
    "ano": int,              # Ano da banca
    "titulo": str,           # Título do trabalho avaliado
    "membros": str,          # Lista de membros (separados por vírgula)
    "candidato": str | None, # Nome do candidato
    "instituicao": str | None # Instituição de origem
}
```

## 🔧 Configurações Avançadas

### Controle de Concorrência

No arquivo `scrape.ipynb`, ajuste o semáforo para controlar requisições simultâneas:

```python
semaphore = Semaphore(5)  # Máximo 5 requisições simultâneas
```

### Modelo de IA

No arquivo `parse.ipynb`, configure o modelo desejado:

```python
default_model = "mistralai/mistral-medium-3"  # Ou outro modelo compatível
```

### Timeout e Retry

Configure timeouts e tentativas no código:

```python
await page.goto(url, timeout=2 * 60_000)  # 2 minutos de timeout
```

## 🐛 Solução de Problemas

### Erros Comuns

1. **Erro de CAPTCHA**: Verifique se a API de browser está configurada corretamente
2. **Rate Limiting**: Reduza o número de requisições simultâneas no semáforo
3. **Timeout**: Aumente os valores de timeout para conexões lentas
4. **Falhas de IA**: Verifique se a chave da API OpenRouter está válida

### Logs e Monitoramento

- URLs com falha são registradas em `failed_extractions.txt`
- Progresso é exibido via barras do tqdm
- Logs detalhados são impressos durante a execução

## 📈 Reprocessamento de Falhas

Para reprocessar URLs que falharam:

1. Verifique o arquivo `failed_extractions.txt`
2. Copie as URLs com falha para `resumes.txt`
3. Execute novamente os notebooks
