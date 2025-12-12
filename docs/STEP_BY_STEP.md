# Guia Passo a Passo - SageMaker Canvas

## 📋 Índice

1. [Preparação do Ambiente](#1-preparação-do-ambiente)
2. [Acesso ao SageMaker Canvas](#2-acesso-ao-sagemaker-canvas)
3. [Importação dos Dados](#3-importação-dos-dados)
4. [Construção do Modelo](#4-construção-do-modelo)
5. [Treinamento](#5-treinamento)
6. [Análise de Resultados](#6-análise-de-resultados)
7. [Geração de Previsões](#7-geração-de-previsões)
8. [Exportação e Documentação](#8-exportação-e-documentação)

---

## 1. Preparação do Ambiente

### 1.1 Criação da Conta AWS

**Tempo estimado**: 15-30 minutos

1. Acesse [aws.amazon.com](https://aws.amazon.com)
2. Clique em "Criar uma conta da AWS"
3. Preencha os dados:
   - Email
   - Senha
   - Nome da conta AWS
4. Adicione informações de contato
5. Configure método de pagamento (cartão de crédito)
6. Verifique identidade (SMS ou chamada)
7. Selecione o plano de suporte (Basic - gratuito)

### 1.2 Configuração de Billing Alerts

**⚠️ IMPORTANTE**: Configure alertas de custo para evitar surpresas!

1. No console AWS, acesse **CloudWatch**
2. No menu lateral, selecione **Billing** → **Billing alerts**
3. Clique em **Create alarm**
4. Configure:
   - Métrica: `EstimatedCharges`
   - Condição: Maior que USD $10 (ou valor desejado)
   - Ação: Enviar notificação por email
5. Confirme o email de notificação

### 1.3 Configuração de IAM

1. Acesse **IAM** (Identity and Access Management)
2. Crie um usuário para o SageMaker:
   - Nome: `sagemaker-user`
   - Tipo de acesso: Console da AWS
3. Anexe políticas:
   - `AmazonSageMakerFullAccess`
   - `AmazonS3FullAccess`
4. Anote as credenciais de acesso

---

## 2. Acesso ao SageMaker Canvas

### 2.1 Navegando até o SageMaker

1. No console AWS, busque por "**SageMaker**" na barra de pesquisa
2. Selecione a região (recomendado: **us-east-1** - N. Virginia)
3. No menu lateral esquerdo, localize a seção **Canvas**
4. Clique em **Open Canvas**

### 2.2 Primeira Configuração

**Na primeira vez que acessar:**

1. O Canvas irá solicitar permissões
2. Clique em **Set up Canvas**
3. Aguarde a criação do ambiente (2-3 minutos)
4. Você verá a interface principal do Canvas

### 2.3 Interface do Canvas

Conheça os principais elementos:

- **Datasets**: Gerenciamento de dados
- **Models**: Seus modelos de ML
- **Predictions**: Previsões realizadas
- **Settings**: Configurações da conta

---

## 3. Importação dos Dados

### 3.1 Upload do Dataset

1. Na tela inicial, clique em **Import data**
2. Selecione **Tabular**
3. Escolha o método de importação:

   **Opção A: Upload Local**
   - Clique em **Local upload**
   - Selecione o arquivo `dataset-1000-com-preco-promocional-e-renovacao-estoque.csv`
   - Aguarde o upload (pode levar 1-2 minutos)

   **Opção B: S3 (Recomendado para datasets grandes)**
   - Primeiro, faça upload para S3:
     - Acesse o console S3
     - Crie um bucket: `sagemaker-estoque-[seu-nome]`
     - Faça upload do CSV
   - No Canvas, selecione **Import from S3**
   - Navegue até o arquivo no S3

### 3.2 Validação dos Dados

Após o upload:

1. O Canvas exibirá um **preview dos dados**
2. Verifique:
   - ✓ Todas as colunas foram reconhecidas
   - ✓ Tipos de dados estão corretos
   - ✓ Não há erros de parsing
3. Revise estatísticas básicas:
   - Número de linhas
   - Valores ausentes
   - Distribuição das colunas

### 3.3 Preparação dos Dados

**Verificações importantes:**

```
✓ DATA_EVENTO está no formato correto (YYYY-MM-DD)
✓ QUANTIDADE_ESTOQUE e QUANTIDADE_VENDIDA são numéricos
✓ FLAG_PROMOCAO é binário (0 ou 1)
✓ Não há valores negativos em quantidades
✓ PRECO_PROMOCIONAL é 0 quando FLAG_PROMOCAO = 0
```

**Se houver problemas:**

1. Clique em **Data wrangler** (se disponível)
2. Ou corrija o arquivo CSV e faça novo upload

---

## 4. Construção do Modelo

### 4.1 Criar Novo Modelo

1. Na aba **Models**, clique em **New model**
2. Nomeie o modelo: `Previsao-Estoque-v1`
3. Selecione o dataset importado
4. Clique em **Create**

### 4.2 Configuração do Modelo

#### Tipo de Modelo

1. O Canvas analisará automaticamente os dados
2. Confirme o tipo de modelo: **Time Series Forecasting**
3. Se não detectar automaticamente, selecione manualmente

#### Configuração de Colunas

**Passo 1: Target Column (Variável Alvo)**
- Selecione: `QUANTIDADE_ESTOQUE`
- Esta é a variável que queremos prever

**Passo 2: Time Column (Coluna de Tempo)**
- Selecione: `DATA_EVENTO`
- Verifique o formato: YYYY-MM-DD

**Passo 3: Item Identifier (Identificador de Item)**
- Selecione: `ID_PRODUTO`
- Permite previsões individuais por produto

**Passo 4: Features (Características)**
Inclua as seguintes colunas:
- ✓ `QUANTIDADE_VENDIDA`
- ✓ `PRECO`
- ✓ `PRECO_PROMOCIONAL`
- ✓ `FLAG_PROMOCAO`
- ✓ `DATA_RENOVACAO_ESTOQUE`

### 4.3 Configurações Avançadas

#### Forecast Horizon (Horizonte de Previsão)
- Configure: **30 dias** (ajuste conforme necessidade)
- Máximo recomendado: 1/3 do período histórico

#### Forecast Frequency (Frequência)
- Selecione: **Daily** (diária)
- Outras opções: Weekly, Monthly

#### Holiday Schedule (Opcional)
1. Clique em **Add holiday schedule**
2. Selecione país: **Brazil**
3. Inclui feriados nacionais automaticamente

#### Backtest Windows
- Mantenha padrão: **Automatic**
- Para controle manual: configure 2-3 janelas de teste

### 4.4 Revisão Final

Antes de treinar, verifique:

```
✓ Target: QUANTIDADE_ESTOQUE
✓ Time: DATA_EVENTO
✓ Item ID: ID_PRODUTO
✓ Horizon: 30 dias
✓ Frequency: Daily
✓ Features: 5 selecionadas
✓ Holiday schedule: Brazil (opcional)
```

---

## 5. Treinamento

### 5.1 Escolher Tipo de Build

**Quick Build** (Recomendado para início)
- ⏱️ Tempo: 2-15 minutos
- 🎯 Precisão: Boa (80-90% do Standard)
- 💰 Custo: Menor
- ✅ Use para: Validação inicial, testes rápidos

**Standard Build** (Recomendado para produção)
- ⏱️ Tempo: 2-4 horas
- 🎯 Precisão: Máxima
- 💰 Custo: Maior
- ✅ Use para: Modelo final, produção

### 5.2 Iniciar Treinamento

1. Clique em **Quick build** ou **Standard build**
2. Confirme as configurações
3. Clique em **Start building**

### 5.3 Acompanhamento

**Durante o treinamento:**

1. Você verá uma barra de progresso
2. Etapas do processo:
   - 📊 Analyzing data (Analisando dados)
   - 🔧 Feature engineering (Engenharia de features)
   - 🎓 Training models (Treinando modelos)
   - ✅ Evaluating (Avaliando)

3. **⚠️ IMPORTANTE**: 
   - Você pode fechar o navegador
   - O treinamento continuará em background
   - Receberá notificação por email quando concluir

### 5.4 Custos Estimados

**Quick Build:**
- Dataset 1000 linhas: ~$0.50 - $1.00

**Standard Build:**
- Dataset 1000 linhas: ~$2.00 - $5.00

💡 **Dica**: Use Quick Build primeiro para validar configurações

---

## 6. Análise de Resultados

### 6.1 Visualizar Métricas

Após conclusão do treinamento:

1. Clique no modelo treinado
2. Navegue até a aba **Analyze**
3. Você verá as métricas principais

### 6.2 Entender as Métricas

#### MAPE (Mean Absolute Percentage Error)
**O que é**: Erro percentual médio das previsões

**Como interpretar**:
- < 10%: ⭐⭐⭐⭐⭐ Excelente
- 10-20%: ⭐⭐⭐⭐ Bom
- 20-50%: ⭐⭐⭐ Aceitável
- > 50%: ⭐⭐ Necessita melhorias

**Exemplo**: MAPE = 15% significa que em média as previsões erram 15%

#### WAPE (Weighted Absolute Percentage Error)
**O que é**: MAPE ponderado pelo volume

**Vantagem**: Dá mais peso a itens com maior volume de vendas

#### RMSE (Root Mean Square Error)
**O que é**: Raiz do erro quadrático médio em unidades absolutas

**Como usar**: Compare RMSE entre diferentes versões do modelo

#### MASE (Mean Absolute Scaled Error)
**O que é**: Erro escalonado comparado com método naive

**Como interpretar**:
- < 1: Modelo é melhor que método naive
- = 1: Modelo equivale a método naive
- > 1: Modelo é pior que método naive

### 6.3 Feature Importance

**Analise a importância das features:**

1. No Canvas, veja o gráfico **Feature importance**
2. Identifique as top 3-5 features
3. Exemplo de interpretação:

```
1. QUANTIDADE_VENDIDA (35%)
   → Vendas passadas são o melhor preditor
   
2. FLAG_PROMOCAO (25%)
   → Promoções impactam significativamente
   
3. DATA_RENOVACAO_ESTOQUE (20%)
   → Padrão de reposição é importante
   
4. PRECO (12%)
   → Elasticidade de demanda moderada
   
5. PRECO_PROMOCIONAL (8%)
   → Impacto menor que a flag de promoção
```

### 6.4 Visualizações

#### Gráfico de Previsão vs Real
- Analise se as previsões seguem a tendência real
- Identifique onde o modelo erra mais

#### Análise de Resíduos
- Resíduos devem ser aleatórios
- Padrões nos resíduos indicam problemas

#### Intervalos de Confiança
- Bandas de incerteza (10%, 50%, 90%)
- Quanto maior a banda, maior a incerteza

### 6.5 Quando Re-treinar

**Re-treine o modelo se:**
- ❌ MAPE > 50%
- ❌ Gráficos mostram padrões não capturados
- ❌ Feature importance não faz sentido de negócio
- ❌ Modelo pior que baseline simples

**Estratégias de melhoria:**
1. Adicionar mais dados históricos
2. Incluir features externas (clima, eventos)
3. Ajustar horizonte de previsão
4. Remover outliers
5. Tratar dados ausentes

---

## 7. Geração de Previsões

### 7.1 Criar Previsão

1. No modelo treinado, clique em **Predict**
2. Configure a previsão:

   **Forecast start date**: Data inicial da previsão
   - Exemplo: 2024-02-01
   
   **Forecast horizon**: Dias para prever
   - Use o configurado no treinamento (ex: 30 dias)
   
   **Select items**: Produtos para prever
   - Todos os produtos, ou
   - Selecione produtos específicos

3. Clique em **Generate predictions**

### 7.2 Visualizar Previsões

**Após geração:**

1. Veja gráficos interativos:
   - Linha temporal de previsões
   - Comparação entre produtos
   - Intervalos de confiança

2. Filtre por:
   - Produto específico
   - Período
   - Nível de confiança

### 7.3 Interpretar Resultados

#### Análise Individual por Produto

```
PROD001:
- Previsão para 01/02: 145 unidades (±15)
- Tendência: Decrescente
- Risco de ruptura: Baixo
- Ação recomendada: Manter estoque atual

PROD003:
- Previsão para 01/02: 85 unidades (±20)
- Tendência: Estável
- Risco de ruptura: Médio
- Ação recomendada: Reabastecer antes de 15/02
```

#### Análise Agregada

- **Total previsto**: Soma das previsões
- **Produtos críticos**: Com risco de ruptura
- **Oportunidades**: Produtos com demanda crescente

### 7.4 Análise de Cenários

**Teste diferentes cenários:**

1. **Cenário Base**: Sem promoções
2. **Cenário com Promoção**: FLAG_PROMOCAO = 1
3. **Cenário Sazonal**: Período de alta demanda

**Como fazer:**
1. Prepare datasets com diferentes cenários
2. Faça batch predictions
3. Compare resultados

---

## 8. Exportação e Documentação

### 8.1 Exportar Previsões

1. Na tela de previsões, clique em **Download**
2. Selecione formato:
   - **CSV**: Para análise em Excel/Python
   - **JSON**: Para integração com APIs
3. Salve o arquivo localmente

### 8.2 Exportar Gráficos

1. Use a função de screenshot do navegador, ou
2. Use ferramentas do Canvas para exportar visualizações
3. Salve em `results/`

### 8.3 Documentar no README

Atualize o README.md com:

```markdown
## Resultados Obtidos

### Métricas do Modelo
- MAPE: XX.XX%
- WAPE: XX.XX%
- RMSE: XXX.XX

### Features Mais Importantes
1. Feature X (XX%)
2. Feature Y (XX%)

### Insights
- [Insight 1]
- [Insight 2]

### Previsões
[Incluir gráficos]
```

### 8.4 Compartilhar Modelo

**Opções de compartilhamento:**

1. **Via Canvas**:
   - Settings → Share model
   - Adicione emails de colaboradores

2. **Deploy para Endpoint** (Avançado):
   - Permite integração via API
   - Custos adicionais aplicam

3. **Exportar para SageMaker Studio**:
   - Para cientistas de dados fazerem fine-tuning

---

## 🔍 Troubleshooting

### Problemas Comuns

#### 1. "Dataset não carrega"
**Solução**:
- Verifique formato CSV (UTF-8)
- Remova caracteres especiais
- Use vírgula como delimitador

#### 2. "Tipo de modelo não reconhecido"
**Solução**:
- Verifique se DATA_EVENTO está no formato correto
- Garanta que há pelo menos 50 registros históricos

#### 3. "Treinamento falhou"
**Solução**:
- Verifique logs no CloudWatch
- Confirme que não há valores ausentes na target
- Reduza número de features

#### 4. "MAPE muito alto"
**Solução**:
- Adicione mais dados históricos
- Remova outliers
- Adicione features relevantes
- Use Standard Build ao invés de Quick Build

---

## ✅ Checklist Final

Antes de finalizar:

- [ ] Dataset importado e validado
- [ ] Modelo treinado com sucesso
- [ ] Métricas analisadas (MAPE < 50%)
- [ ] Previsões geradas
- [ ] Resultados exportados
- [ ] README.md atualizado
- [ ] Gráficos salvos em `results/`
- [ ] Insights documentados
- [ ] Código commitado no GitHub
- [ ] Repositório compartilhado na DIO

---

## 📚 Próximos Passos

1. **Otimização**:
   - Teste diferentes configurações
   - Adicione mais features
   - Experimente outras técnicas

2. **Produção**:
   - Configure pipeline automatizado
   - Implemente monitoramento
   - Estabeleça processo de retreinamento

3. **Expansão**:
   - Aplique em outros produtos
   - Integre com sistemas existentes
   - Crie dashboards de visualização

---

**🎉 Parabéns!** Você completou o desafio de Previsão de Estoque com SageMaker Canvas!