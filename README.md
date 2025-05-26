# 🧠 Guia Profissional: Azure Cognitive Search — Utilizando AI Search para Indexação e Consulta de Dados

## 📌 Introdução

O **Azure Cognitive Search** é um serviço de busca baseado em inteligência artificial que permite indexar, enriquecer e consultar grandes volumes de dados estruturados e não estruturados. Com ele, é possível construir experiências de busca modernas, relevantes e escaláveis em websites, aplicativos corporativos e soluções personalizadas.

Este guia profissional cobre as principais etapas e melhores práticas para utilização do Azure Cognitive Search, desde a ingestão de dados até a configuração de modelos cognitivos e consulta avançada.

---

## 🧭 Visão Geral da Arquitetura

Uma solução completa com Azure Cognitive Search normalmente envolve:

```
[Origem de Dados] --> [Indexer] --> [Index] --> [Consulta via Search SDK/API]
                                    |
                               [Skillset de AI]
```

Componentes-chave:

- **Datasource**: conexão com origem de dados (Blob Storage, Cosmos DB, SQL, etc.)
- **Indexer**: responsável por buscar, extrair e indexar os dados
- **Index**: estrutura de busca que armazena os dados processados
- **Skillset**: conjunto de habilidades cognitivas aplicadas aos dados (OCR, NLP, tradução)
- **Search API / SDK**: camada de consulta

---

## 📥 Etapa 1: Conectando a Origem de Dados

Configure o datasource para apontar para seu repositório de dados:

Tipos suportados:
- Azure Blob Storage
- Azure SQL Database
- Cosmos DB
- Data Lake Storage Gen2
- Documentos locais via API

Exemplo (Blob):

```json
{
  "name": "blob-datasource",
  "type": "azureblob",
  "credentials": { "connectionString": "..." },
  "container": { "name": "documentos" }
}
```

---

## 🧠 Etapa 2: Habilitando AI com Skillsets

Skillsets aplicam enriquecimento cognitivo:

Exemplos de habilidades (skills):
- OCR (extração de texto de imagens)
- Reconhecimento de entidades (nomes, locais)
- Análise de sentimento
- Tradução automática
- Detecção de idioma

Exemplo de skillset:

```json
{
  "name": "skillset-documento",
  "skills": [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "inputs": [{ "name": "text", "source": "/document/content" }],
      "outputs": [{ "name": "organizations", "targetName": "orgs" }]
    }
  ]
}
```

---

## 🏗️ Etapa 3: Criando o Index

O índice define a estrutura dos campos pesquisáveis:

```json
{
  "name": "index-documentos",
  "fields": [
    { "name": "id", "type": "Edm.String", "key": true },
    { "name": "titulo", "type": "Edm.String", "searchable": true },
    { "name": "conteudo", "type": "Edm.String", "searchable": true, "analyzer": "pt.microsoft" }
  ]
}
```

- `searchable`: habilita busca textual
- `filterable`: permite aplicar filtros
- `sortable`: ordenação
- `facetable`: agrega por valores distintos
- `retrievable`: define se o campo será retornado

---

## ⚙️ Etapa 4: Configurando o Indexador

O indexador conecta datasource → skillset → index.

```json
{
  "name": "indexador-documentos",
  "dataSourceName": "blob-datasource",
  "targetIndexName": "index-documentos",
  "skillsetName": "skillset-documento",
  "schedule": { "interval": "PT1H" }
}
```

- Pode ser agendado ou acionado manualmente
- Suporta delta de indexação (novos ou alterados)

---

## 🔍 Etapa 5: Consultando com a API de Busca

As consultas podem ser simples ou avançadas via:

- REST API
- SDK (.NET, Java, Python, JS)
- Azure Portal (explorador de índice)

Exemplo de consulta simples:

```http
GET https://<search-name>.search.windows.net/indexes/index-documentos/docs?search=azure
```

Com filtros e ordenações:

```http
GET /docs?search=IA&$filter=categoria eq 'TI'&$orderby=ano desc
```

---

## 🧰 Boas Práticas Profissionais

- ✅ Use **analyzers linguísticos** conforme o idioma do conteúdo
- ✅ Proteja com **chaves de API e RBAC**
- ✅ Separe campos para consulta e exibição
- ✅ Habilite **autocomplete e sugestões** com campos `suggesters`
- ✅ Use **soft delete** em dados indexados sensíveis

---

## 📈 Monitoramento e Performance

Utilize:

- **Azure Monitor + Logs** para rastrear atividades
- **Search Traffic Analytics** para entender uso
- **Throttling Alerts** para prevenir sobrecarga

---

## 📚 Recursos Complementares

- [Documentação Oficial](https://learn.microsoft.com/azure/search/)
- [REST API Reference](https://learn.microsoft.com/rest/api/searchservice/)
- [Azure AI Studio (pré-visualização)](https://ai.azure.com/)
- [Exemplo com Python SDK](https://learn.microsoft.com/azure/search/search-get-started-python)

---

## ✅ Conclusão

O Azure Cognitive Search fornece uma plataforma poderosa e flexível para transformar dados em experiências de busca enriquecidas com inteligência artificial. Combinando indexação automática, habilidades cognitivas e APIs modernas, é possível construir soluções robustas para diferentes domínios — desde buscadores corporativos até bots cognitivos.
