# DOCUMENTAÇÃO TÉCNICA EXECUTIVA
## SISTEMA RAG INPI - Consulta Inteligente de Marcas

---

## 1. RESUMO EXECUTIVO

**Objetivo**: Sistema RAG para consulta automatizada de 572 revistas INPI (11 anos de dados históricos)

**Funcionalidades Core**:
- Verificação instantânea de disponibilidade de marcas
- Análise de conflitos por classe Nice
- Estatísticas de mercado e tendências
- Motivos de indeferimento históricos

**ROI Esperado**: Redução de 80% no tempo de pesquisa manual (8h → 1.5h por consulta)

---

## 2. ARQUITETURA TÉCNICA

### 2.1 Pipeline de Dados (Local)
```
XML INPI → Parser Customizado → Chunks por Marca → Embeddings → Supabase
```

**Componentes**:
- **Parser XML**: Extração estruturada por marca individual
- **Chunking Strategy**: 1 marca = 1 chunk (contexto completo preservado)
- **Embeddings**: OpenAI text-embedding-3-large (1536 dimensões)
- **Volume**: ~100.000 chunks (572 revistas processadas)

### 2.2 Armazenamento (Supabase)
```sql
-- Schema Principal
TABLE inpi_marcas {
  numero_processo TEXT UNIQUE,
  marca_nome TEXT,
  requerente TEXT,
  classes_nice TEXT[],
  status TEXT,
  chunk_content TEXT,        -- Contexto completo
  embedding VECTOR(1536),    -- Busca semântica
  revista_numero TEXT,
  data_deposito DATE
}

-- Índices Otimizados
- Busca exata: GIN(marca_nome)
- Busca semântica: ivfflat(embedding)
- Filtragem: classe_nice, status, data
```

### 2.3 Sistema Multi-Agente (Agno Framework)
```python
# Arquitetura de Agentes
MarcaSearchAgent:     Claude Sonnet 4  → Análise complexa de conflitos
AnalyticsAgent:       GPT-4o-mini     → Estatísticas quantitativas  
TeamCoordinator:      Claude Sonnet 4  → Router inteligente
```

**Retrieval Híbrido**:
1. Busca exata PostgreSQL (nomes similares)
2. Busca semântica pgVector (conceitos relacionados)
3. Filtragem metadata (classe, status, período)

### 2.4 API e Deploy
- **Backend**: FastAPI + uvicorn
- **Infraestrutura**: Digital Ocean VPS (4GB RAM, 2 vCPU)
- **Monitoramento**: Logs estruturados + métricas performance
- **Segurança**: Rate limiting + autenticação API key

---

## 3. ETAPAS DE DESENVOLVIMENTO

### Fase 1: Base Técnica
- Setup Supabase + schema otimizado
- Desenvolvimento parser XML customizado
- Testes chunking estratégia
- Pipeline embeddings + upload
- Processamento das 572 revistas
- Validação qualidade dados

### Fase 2: Sistema Inteligente
- Implementação agentes Agno
- Configuração retrieval híbrido
- Otimização prompts especializados
- Testes qualidade respostas
- Fine-tuning parâmetros busca
- Validação casos de uso reais

### Fase 3: Deploy e Produção
- API FastAPI + documentação
- Deploy Digital Ocean + monitoramento
- Testes carga + performance

## 4. PRÓXIMOS PASSOS

### Imediatos
1. **Setup ambiente desenvolvimento**: Supabase + Digital Ocean
2. **Aquisição APIs**: OpenAI + Anthropic créditos
3. **Desenvolvimento parser**: XML → chunks otimizados

### Desenvolvimento Core
1. **Processamento dados**: 572 revistas → embeddings
2. **Setup Supabase**: Schema + índices performance
3. **Implementação agentes**: Agno + Claude integration

### Finalização
1. **API FastAPI**: Endpoints + documentação
2. **Deploy produção**: VPS + monitoramento
3. **Testes finais**: Performance + validação


---
**DATA**: 25/07/2025