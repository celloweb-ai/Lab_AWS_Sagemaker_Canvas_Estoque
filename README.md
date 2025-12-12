# Previsão de Estoque Inteligente na AWS com SageMaker Canvas

## 📋 Descrição do Projeto

Este projeto implementa um modelo de Machine Learning no-code utilizando o Amazon SageMaker Canvas para previsão inteligente de estoque. O objetivo é criar um sistema capaz de prever a demanda de produtos, otimizando o gerenciamento de estoque e reduzindo custos operacionais.

## 🎯 Objetivos

- Desenvolver um modelo de previsão de estoque usando SageMaker Canvas
- Aplicar conceitos práticos de Machine Learning sem necessidade de código
- Documentar todo o processo de criação e análise do modelo
- Fortalecer o portfólio com projeto prático de IA na AWS

## 🚀 Tecnologias Utilizadas

- **Amazon SageMaker Canvas**: Plataforma no-code para criação de modelos de ML
- **AWS S3**: Armazenamento dos datasets
- **Machine Learning**: Algoritmos de previsão de séries temporais

## 📊 Dataset

O dataset utilizado contém informações históricas de estoque com as seguintes características:

- **ID_PRODUTO**: Identificador único do produto
- **DATA_EVENTO**: Data do registro
- **QUANTIDADE_ESTOQUE**: Quantidade disponível em estoque
- **QUANTIDADE_VENDIDA**: Quantidade vendida no período
- **PRECO**: Preço unitário do produto
- **PRECO_PROMOCIONAL**: Preço em promoção (se aplicável)
- **FLAG_PROMOCAO**: Indicador de promoção (0 ou 1)
- **DATA_RENOVACAO_ESTOQUE**: Data prevista para renovação

### Estrutura dos Dados

O dataset está localizado na pasta `datasets/` e contém 1000 registros simulados representando o comportamento de vendas e estoque ao longo do tempo.

## 🔧 Passo a Passo da Implementação

### 1. Preparação do Ambiente AWS

#### 1.1 Criar Conta na AWS
- Acesse [aws.amazon.com](https://aws.amazon.com)
- Siga o [guia de criação de conta](https://github.com/digitalinnovationone/aws-cloud-quickstart)
- Configure billing alerts para monitorar custos

#### 1.2 Acessar o SageMaker Canvas
- No console AWS, busque por "SageMaker"
- No menu lateral, selecione "Canvas"
- Clique em "Open Canvas" para iniciar

### 2. Importação e Preparação dos Dados

#### 2.1 Upload do Dataset
1. No SageMaker Canvas, clique em "Import data"
2. Selecione "Upload from local" ou "Import from S3"
3. Faça upload do arquivo `dataset-1000-com-preco-promocional-e-renovacao-estoque.csv`
4. Aguarde a validação e preview dos dados

#### 2.2 Análise Exploratória
- Verifique a qualidade dos dados
- Identifique valores ausentes ou inconsistentes
- Analise a distribuição das variáveis
- Observe padrões sazonais e tendências

### 3. Construção do Modelo

#### 3.1 Configuração do Modelo
1. Selecione o tipo de modelo: **Time Series Forecasting**
2. Defina a variável target: `QUANTIDADE_ESTOQUE`
3. Configure o horizonte de previsão (ex: 30 dias)
4. Selecione a coluna de data: `DATA_EVENTO`
5. Adicione features relevantes:
   - QUANTIDADE_VENDIDA
   - PRECO
   - FLAG_PROMOCAO
   - PRECO_PROMOCIONAL

#### 3.2 Configurações Avançadas
- **Item ID**: ID_PRODUTO (para previsões por produto)
- **Holiday Schedule**: Adicione feriados brasileiros se aplicável
- **Backtest Window**: Configure janela de validação

### 4. Treinamento do Modelo

#### 4.1 Quick Build vs Standard Build
- **Quick Build**: ~2-15 minutos, boa precisão inicial
- **Standard Build**: ~2-4 horas, máxima precisão

💡 **Recomendação**: Inicie com Quick Build para validação rápida, depois execute Standard Build para produção.

#### 4.2 Acompanhamento
- Monitore o progresso do treinamento
- Aguarde a conclusão (não feche o navegador)
- Verifique notificações de conclusão

### 5. Análise dos Resultados

#### 5.1 Métricas de Performance
Após o treinamento, analise as seguintes métricas:

- **MAPE (Mean Absolute Percentage Error)**: Erro percentual médio
  - < 10%: Excelente
  - 10-20%: Bom
  - 20-50%: Aceitável
  - > 50%: Necessita melhorias

- **WAPE (Weighted Absolute Percentage Error)**: Erro ponderado
- **RMSE (Root Mean Square Error)**: Raiz do erro quadrático médio
- **MAE (Mean Absolute Error)**: Erro absoluto médio

#### 5.2 Feature Importance
Identifique as variáveis mais importantes:
- Verifique o impacto de cada feature na previsão
- Remova features com baixa importância se necessário
- Considere adicionar novas features relevantes

#### 5.3 Visualizações
- Gráficos de previsão vs real
- Análise de resíduos
- Tendências e sazonalidade
- Intervalos de confiança

### 6. Previsões

#### 6.1 Gerar Previsões
1. Clique em "Predict"
2. Selecione o horizonte temporal
3. Escolha os produtos para previsão
4. Execute as previsões

#### 6.2 Exportar Resultados
- Baixe as previsões em formato CSV
- Exporte gráficos e visualizações
- Documente insights e recomendações

#### 6.3 Análise de Cenários
- Teste diferentes cenários (promoções, sazonalidade)
- Compare previsões com diferentes configurações
- Valide resultados com especialistas de negócio

## 📈 Resultados Obtidos

### Métricas do Modelo

```
🎯 Métricas de Performance:
- MAPE: XX.XX%
- WAPE: XX.XX%
- RMSE: XXX.XX
- MAE: XXX.XX
- Acurácia: XX.XX%
```

### Features Mais Importantes

1. **QUANTIDADE_VENDIDA** (XX%): Principal indicador de demanda
2. **FLAG_PROMOCAO** (XX%): Impacto significativo nas vendas
3. **PRECO** (XX%): Elasticidade de demanda
4. **DATA_RENOVACAO_ESTOQUE** (XX%): Padrão de reposição

### Insights e Conclusões

#### Descobertas Principais
- [Descreva padrões identificados no estoque]
- [Análise de sazonalidade]
- [Impacto de promoções]
- [Comportamento por produto]

#### Recomendações
1. **Gestão de Estoque**:
   - Otimizar níveis de estoque mínimo
   - Ajustar frequência de reposição
   - Prever necessidades para períodos de alta demanda

2. **Estratégias Comerciais**:
   - Planejar promoções baseadas em previsões
   - Identificar produtos com maior potencial
   - Reduzir rupturas de estoque

3. **Melhorias Futuras**:
   - Incluir dados externos (clima, eventos)
   - Adicionar mais histórico de dados
   - Refinar features do modelo

## 💡 Aprendizados

### Técnicos
- Utilização do SageMaker Canvas para ML no-code
- Configuração de modelos de previsão de séries temporais
- Análise e interpretação de métricas de ML
- Importância da qualidade dos dados

### Negócio
- Impacto de promoções na demanda
- Padrões de comportamento de compra
- Otimização de custos de estoque
- Tomada de decisão baseada em dados

## 🔄 Próximos Passos

- [ ] Implementar modelo em produção
- [ ] Configurar pipeline automatizado
- [ ] Integrar com sistema de ERP
- [ ] Criar dashboard de monitoramento
- [ ] Estabelecer processo de retreinamento periódico
- [ ] Expandir para outras categorias de produtos

## 📚 Recursos Adicionais

### Documentação Oficial
- [Amazon SageMaker Canvas](https://aws.amazon.com/sagemaker/canvas/)
- [Guia de Introdução ao SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html)
- [SageMaker Resources](https://aws.amazon.com/sagemaker/resources/)

### Tutoriais e Cursos
- [AWS SageMaker Examples](https://github.com/aws/amazon-sagemaker-examples)
- [Criação de Modelos de Linguagem na AWS](https://explore.skillbuilder.aws/learn/course/internal/view/elearning/18522/criacao-de-modelos-de-linguagem-na-aws-portugues-building-language-models-on-aws-portuguese)
- [AWS Cloud Quickstart](https://github.com/digitalinnovationone/aws-cloud-quickstart)

### Comunidade
- [Artigos DIO](https://web.dio.me/articles)
- [Stack Overflow - AWS Tag](https://stackoverflow.com/questions/tagged/amazon-web-services)
- [AWS Community](https://aws.amazon.com/developer/community/)

## 🤝 Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para:

1. Fazer fork do projeto
2. Criar uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona MinhaFeature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abrir um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.

## ✨ Autor

**Marcus Vasconcellos**
- LinkedIn: [linkedin.com/in/marcusvasconcellos](https://www.linkedin.com/in/marcusvasconcellos)
- GitHub: [@celloweb-ai](https://github.com/celloweb-ai)
- Email: marcus@vasconcellos.net.br

---

## 🏆 Desafio DIO

Projeto desenvolvido como parte do bootcamp da [Digital Innovation One](https://www.dio.me/)

**Bootcamp**: AWS Machine Learning
**Desafio**: Previsão de Estoque Inteligente na AWS com SageMaker Canvas

---

⭐ Se este projeto foi útil para você, considere dar uma estrela!

#AWS #MachineLearning #SageMaker #IA #DIO #DataScience