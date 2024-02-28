# Rekognition
### Overview
- Encontrar objetos, pessoas, textos, cenas em imagens e videos usando Machine Learning (ML)
- Análise facial e busca facial para realizar *user authentication*, *people counting*
- Cria um banco de dados de "rostos familiares" ou compare com celebridades
- *Use Cases*
	- Labeling
	- Content Moderation
	- Text Detection
	- Face Detection and Analysis (gender, age range, emotions, etc)
	- Face Search and Verification
	- Celebrity Recognition
	- Pathing (e.g. for sports game analysis)

### Content Moderation
- Detecta conteúdo que são inapropriados, não desejados ou ofensivos (em imagens e videos)
- Usado em mídias sociais, broadcast media, anúncios e situações de e-commerce que necessitam uma experiência segura
- Configure um limite mínimo de confiança para os itens marcados
- Marque conteúdo sensível para revisões manuais (por um humano) no Augmented AI (A2I)
	- *Help comply with regulations*

# Transcribe
### Overview
- Converte automaticamente discurso em texto
- Usa um *deep learning process* chamado *automaitc speech recognition (ASR)* para converter o discurso em texto rapidamente e de forma acurada
- Transcribe automaticamente remove Informações de Identificação Pessoal (*Personally Identifiable Information (PII)*) usando *Redaction*
- Suporta *Automatic Language Identification for Multi-Lingual Audio*
- *Use Cases*
	- Transcrever chamadas de clientes
	- Automatizar o processo de legendagem
	- Gerar metadados de *media assets* para criar um arquivo integralmente consultável

# Polly
### Overview
- Polly é o inverso do Transcribe, ele transforma texto em discurso (ou *lifelike speech*) usando *deep learning*
- Permite criar aplicações que falam

### Lexicon & SSML
1. Lexicon
   - Customiza a pronuncia das palavras com *Pronunciation Lexicons*
	   - Stylized words: V1ct0r => "Victor"
	   - Acronyms: AWS => "Amazon Web Services"
   - Realize o upload dos lexicons e utilize-os no *SynthesizeSpeech* operation
2. Speech Synthesis Markup Language (SSML)
   - SSML gera discurso a partir de texto ou documentos, com maior possibilidade de customização
	   - Dando ênfase em palavras ou frases específicas
	   - Usando pronunciação fonética
	   - Incluindo som de respiração, ou de sussurros
	   - utilizando o *Newscaster speaking style* (e.g. estilo de fala de televisão)

# Lex & Connect
### Amazon Lex
- *Automatic Speech Recognition (ASR)* para converter discurso em texto
- *Natural Language Understanding* para reconhecer intenção em textos ou chamadas
- Ajuda a construir chatbots, call center bots
### Amazon Connect
- Recebe chamadas, e cria *contact flows*, *cloud-based virtual contact center*
- Pode ser integrado com outros CRM systems ou AWS
- Sem pagamento antecipado, e 80% mais barato que outras *contact center solutions* tradicionais.

# Comprehend
### Overview
- Para *Natural Language Processing - NLP*
- Utiliza *machine learning* para encontrar insights e relacionamentos em textos
	- Lingua do texto
	- Extrai frases, lugares, pessoas, marcas ou eventos importantes
	- Entende se o texto é positivo ou negativo
	- Analisa o texto usando *tokenization* e partes do discurso
	- Organiza automaticamente uma coleção de arquivos de textos por tópico
- *Use Cases*
	- Analisar interações do cliente (emails) para achar o que os leva a uma experiência positiva ou negativa
	- Criar e agrupar artigos por tópicos que o Comprehend irá descobrir

### Comprehend Medical
- Comprehend Medical detecta e retorna informações úteis de textos clínicos não estruturados
	- Notas de médicos
	- Resumos de altas
	- Resultado de testes
	- Notas de caso
- Utiliza NLP para detectar *Protected Health Information (PHI)* - DetectPHI API
- Armazena os documentos no S3, e os analisa em tempo real com o Kinesis Firehose, ou utilize o Transcribe para transcrever as histórias dos pacientes em textos que podem ser analisados pelo Comprehend Medical

# SageMaker
### Overview
- Serviço completamente gerenciável para desenvolvedores ou cientistas de dados criarem seus próprios ML models.
	- Diferentemente dos outros serviços de ML, o SageMaker não é pre-pronto pela AWS, onde só é necessário inserir os dados. No SageMaker o processo de criação é completamente da responsabilidade do usuário
	- Label -> Build -> Train and tune -> Apply Model
- Difícil de realizar todo o processo em um local
- Necessário provisionar servidores

# Forecast
### Overview
- Utiliza ML para entregar previsões altamente acuradas
	- e.g. prever o futuro de vendas de um casaco de chuva
- 50% mais exato do que olhar os dados manualmente
- Reduz o tempo de previsão de meses para algumas horas

# Kendra
### Overview
- *Document Search Service*
- Extrai respostas de dentro de documentos (text, pdf, HTML, PowerPoint, MS Word, FAQs...)
- *Natural language search capabilities*
- Aprende com interações/feedbacks do usuário para promover melhores resultados (*Incremental Learning*)
- Habilidade de *manually fine-tune search results* (importância dos dados, *freshness*, custom, etc)

# Personalize
### Overview
- Util para criar aplicações com recomendações pessoais em tempo real
- *Personalized product recommendations/re-ranking, customized direct marketing*
- Se integra com websites existentes, aplicações, SMS, *email marketing systems*, e mais.
- Implemente em dias e não meses
- *Use Case*
	- Lojas de varejo, media e entretenimento

# Textract
### Overview
- Extrai automaticamente texto, escritos a mão, e dados de qualquer documento escaneado usando AI e ML
- Extrai dados de tabelas e formulários
- Lê e processa qualquer tipo de documento (PDFs, Imagens)
- *Use Case*
	- Financial Services (e.g. invoices, financial reports)
	- Healthcare (e.g. medical records, insurance claims)
	- Public Setor (e.g. tax forms, ID documents, passports)