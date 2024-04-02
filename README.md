#  Solução de Mineração de Conhecimento

Este repositório contém uma rede nacional de cafés ficticia, com o objetivo de facilitar a busca por insights sobre as experiências dos clientes. A solução utiliza o Azure AI Search para indexar e consultar avaliações de clientes, enriquecendo os dados com habilidades de IA para análises mais profundas.



### Criar Recursos do Azure

Antes de começar, é necessário criar os seguintes recursos na sua assinatura do Azure:

- Um recurso do [Azure AI Search](https://azure.microsoft.com/services/search/), que gerenciará a indexação e a consulta.
- Um recurso de [Serviços de IA do Azure](https://azure.microsoft.com/services/cognitive-services/), que fornece serviços de IA para habilidades que a solução de pesquisa pode usar para enriquecer os dados.
- Uma [Conta de Armazenamento do Azure](https://azure.microsoft.com/services/storage/), que armazenará documentos brutos e outras coleções de dados.

### Extração e Carregamento de Dados

Após criar os recursos necessários, é hora de extrair e carregar os dados para a solução. Siga estas etapas:

- Baixe as avaliações de café compactadas em [https://aka.ms/mslearn-coffee-reviews](https://aka.ms/mslearn-coffee-reviews) e extraia os arquivos para a pasta de avaliações.
- Crie um contêiner no armazenamento de blobs do Azure e faça o upload dos documentos de avaliações para este contêiner.

# Indexação de Documentos

Após armazenar os documentos, é possível utilizar o Azure AI Search para extrair insights dos mesmos. O portal do Azure oferece um assistente de importação de dados que facilita esse processo. Com este assistente, você pode criar automaticamente um índice e um indexador para fontes de dados suportadas. Siga as instruções abaixo para indexar seus documentos no Azure AI Search:

1. No portal do Azure, navegue até o recurso Azure AI Search. Na página "Visão geral", selecione "Importar dados".


2. Na página "Conectar-se aos seus dados", na lista "Fonte de Dados", selecione "Azure Blob Storage". Preencha os detalhes do armazenamento de dados com as seguintes configurações:
   - **Fonte de dados**: Armazenamento de Blobs do Azure.
   - **Nome da fonte de dados**: (Nome de sus preferencia).
   - **Dados a extrair**: Conteúdo e metadados.
   - **Modo de análise**: Padrão.
   - **Cadeia de conexão**: Selecione "Escolha uma conexão existente", escolha sua conta de armazenamento e selecione o contêiner de avaliações de café.
   - **Autenticação de identidade gerenciada**: Nenhuma.
   - **Nome do contêiner**: Esta configuração é preenchida automaticamente após escolher uma conexão existente.
   - **Pasta Blob**: Deixe em branco.
   - **Descrição**: Avaliações sobre rede ficticia de cafés.
   
   Após preencher os detalhes, selecione "Próximo: Adicionar habilidades cognitivas (opcional)".

3. Na seção "Anexar Serviços Cognitivos", selecione o seu recurso de serviços Azure AI.

4. Na seção "Adicionar enriquecimentos", siga estas etapas:
   - Altere o nome da qualificação para "coffee-skillset".
   - Marque a caixa de seleção "Habilitar OCR" e mesclar todo o texto no campo "merged_content".
   - Certifique-se de que o campo "Dados de origem" esteja configurado como "merged_content".
   - Altere o nível de granularidade de enriquecimento para "Páginas (blocos de 5.000 caracteres)".
   - Selecione os campos enriquecidos conforme especificado na tabela.

5. Em "Salvar enriquecimentos em um armazenamento de conhecimento", siga estas etapas:
   - Se aparecer um aviso solicitando uma cadeia de conexão de conta de armazenamento, selecione "Escolha uma conexão existente".
   - Escolha a conta de armazenamento que você criou anteriormente.
   - Clique em "+ Container" para criar um novo contêiner chamado "armazenamento de conhecimento" com o nível de privacidade definido como "Private" e selecione "Create".
   - Selecione o contêiner de armazenamento de conhecimento e clique em "Selecionar" na parte inferior da tela.
   - Selecione projeções de blob do Azure: "Documento".

6. Na próxima tela, "Personalizar índice de destino", altere o nome do índice para "coffee-index" e verifique as configurações dos campos de índice.

7. Por fim, selecione "Próximo: Criar um indexador". No painel de configuração do indexador, você pode personalizar o nome e outras opções avançadas, como a programação.

8. Após revisar todas as configurações, selecione "Enviar" para criar a fonte de dados, o conjunto de habilidades, o índice e o indexador. O indexador será executado automaticamente e realizará o pipeline de indexação.

9. Volte à página de recursos do Azure AI Search e verifique o status do indexador até que ele indique sucesso.

Com essas etapas concluídas, seus documentos estarão indexados e prontos para consulta no Azure AI Search. Utilize o Search Explorer para escrever e testar consultas. Este explorador é uma ferramenta incorporada no portal do Azure que oferece uma maneira fácil de validar a qualidade do seu índice de pesquisa. Alterne para a visualização JSON para revisar os resultados das consultas.
# Consulta do Índice

Após a configuração do Azure AI Search, você pode consultar o índice para extrair insights sobre as experiências dos clientes. Utilize o Search Explorer no portal do Azure para testar consultas e revisar os resultados.


## Exemplos 

#### Input:
```json
{

 "search": "sentiment:'negative'",
 "count": true
}
```

#### Output:

```json
{
  "@odata.context": "https://cafeteste2.search.windows.net/indexes('coffee-index')/$metadata#docs(*)",
  "@odata.count": 1,
  "value": [
    {
      "@search.score": 0.6931472,
      "content": "Review: Today I was truly disappointed with how long I had to wait for the pastries I ordered ahead of time. When I got my box, some of the pastries seemed stale. Terrible experience!  \nDate: October 23, 2018\nLocation: Chicago, Illinois \n\n",
      "metadata_storage_path": "aHR0cHM6Ly9ibG9xMS5ibG9iLmNvcmUud2luZG93cy5uZXQvYXZhbGlhY29lc2RvY2FmZS9yZXZpZXctOC5kb2N40",
      "locations": [
        "Chicago",
        "Illinois"
      ],
      "keyphrases": [
        "Terrible experience",
        "Review",
        "pastries",
        "time",
        "box",
        "Date",
        "October",
        "Location",
        "Chicago",
        "Illinois"
      ],
      "sentiment": "[\"negative\"]",
      "merged_content": "Review: Today I was truly disappointed with how long I had to wait for the pastries I ordered ahead of time. When I got my box, some of the pastries seemed stale. Terrible experience!  \nDate: October 23, 2018\nLocation: Chicago, Illinois \n\n",
      "text": [],
      "layoutText": [],
      "imageTags": [],
      "imageCaption": []
    }
  ]
}
```

# Insights
A Azure AI Search, utilizada para indexação e consulta de dados, é uma das ferramentas do Azure que facilita termos uma noção melhor de como a nossa empresa ou nosso ambiente, de um modo geral, é visto. Essa integração permite não apenas entender as preferências e opiniões dos clientes, mas também agir com base nessas informações para melhorar produtos, serviços e interações com os clientes. Por exemplo, ao utilizar os resultados da análise de dados em plataformas de CRM, as empresas podem personalizar suas abordagens de marketing e atendimento ao cliente para atender melhor às necessidades individuais dos clientes. Da mesma forma, ao integrar sistemas de recomendação em plataformas de comércio eletrônico com insights de análise de dados, é possível oferecer recomendações de produtos mais relevantes e aumentar as taxas de conversão.

### Possíveis Ferramentas Beneficiadas:

1. **Plataformas de Análise de Dados**: Como Tableau, Power BI e Google Data Studio.
2. **CRM (Gestão de Relacionamento com o Cliente)**: Integração para personalização das interações.
3. **Chatbots e Assistência Virtual**: Para respostas personalizadas e eficientes.
4. **Sistemas de Recomendação**: Melhoria das recomendações em plataformas de comércio eletrônico ou streaming.
5. **Melhoria de Produtos e Serviços**: Identificação de áreas de melhoria.

### Aprendizados Adquiridos:

- **Compreensão de Dados Reais**: Estrutura, qualidade e desafios dos dados.
- **Integração de Serviços do Azure**: Uso prático de diferentes serviços Azure.
- **Habilidades de IA e NLP**: Análise de texto e suas aplicações.
- **Configuração e Gerenciamento de Recursos na Nuvem**: Provisionamento e gerenciamento eficiente.
- **Desenvolvimento de Consultas e Análises**: Tradução de requisitos de negócios em consultas eficazes.

Essa abordagem permite aproveitar os benefícios de uma solução de mineração de conhecimento de forma eficaz, enquanto adquire valiosos aprendizados práticos.
