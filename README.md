📘 README — Fluxo de Atendimento Telegram

Projeto: Marmitaria — Engenheiro de IA (Teste Técnico)

Versão do fluxo: v0.7
________________________________________

🔎 Visão Geral

Este fluxo implementa um agente inteligente de atendimento para uma Marmitaria via Telegram, seguindo todos os requisitos presentes no PDF do Teste Técnico para a vaga de Engenheiro de IA.

O fluxo foi construído no n8n, integrando:

-	Telegram Trigger;
-	Extração e unificação de parâmetros;
-	IA com ferramentas (tools);
-	Consulta e gravação em Google Sheets;
-	Raciocínio estruturado (Tool Think e Engenharia Avançada de Prompt);
-	Humanização do texto com envio por parágrafos;
-	Log avançado de performance do fluxo;

O agente é totalmente autônomo, capaz de:
- enviar cardápio;
- iniciar pedidos;
- atualizar pedidos;
- cancelar pedidos;
- confirmar pedidos;
- consultar pedidos;
- registrar tudo em planilhas;
- decidir quando e qual tool usar;
- seguir regras rígidas de fluxo;
- humanizar as respostas em parágrafos;

________________________________________
🎯 Objetivo do Fluxo

O objetivo principal é:
Criar um fluxo completo de atendimento automatizado para uma Marmitaria, onde a IA conduz toda a conversa, chama ferramentas n8n conforme necessário e registra pedidos de forma organizada em planilhas externas.

O sistema agirá como um atendente real, garantindo:
-	Consistência;
-	Clareza;
-	Estrutura dos pedidos;
-	Controle de status;
-	Organização de histórico;
________________________________________
🏛 Arquitetura Geral do Fluxo

O fluxo é dividido em quatro blocos grandes, representados por sticky notes:
________________________________________
✔ 1. Entrada de Mensagens + Preparação dos Dados
(Telegram Trigger → Switch Texto/Voz → Download Voz → Transcrição → PARÂMETROS → Unificação)

Função:
-	Captura mensagens do Telegram;
-	Reconhece se é texto ou áudio;
-	Transcreve áudio;
-	Normaliza e unifica:
o	session_id
o	nome
o	mensagem
o	voz
o	mime_type
o	timestamp
o	cardápio
-	Prepara o JSON usado por todos os blocos seguintes;
________________________________________


✔ 2. Agente IA + Memória + Ações (Tools)
(OpenAI → Memory → Tools → AGENTE IA)

Este é o coração do fluxo.

O Agente IA possui:
-	System Message altamente detalhado;
-	User Message com lógica de interação;
-	Think (raciocínio oculto);
  
-	Acesso a tools:
- Enviar Menu (Enviado via tool Telegram);
- Enviar Cardápio (Enviado via tool Telegram);
- Criar Pedido (Agente cria pedido na planilha do Google Sheets);
- Consultar Pedido (Agente consulta pedido na planilha do Google Sheets);
- Atualizar/Cancelar Pedido (Agente atualiza pedido na planilha do Google Sheets);
- Calculadora (Auxílio para calcular valores do pedido);
- Memory Buffer (Contexto das últimas 10 mensagens enviadas pelo cliente);

Aqui acontece:
-	interpretação da intenção do usuário;
-	decisão de usar ou não tool;
-	geração da resposta estruturada;
-	validação de pedidos;
________________________________________
✔ 3. Saída de Mensagens (Humanização)
(AGENTE JSON → Structured Parser → SplitOut → Loop → Envio Telegram)

Objetivo:
Humanizar o envio, evitando blocos gigantes de texto.

Processo:
1.	IA gera texto completo;
2.	AGENTE JSON divide o texto em frases curtas;
3.	Structured Parser transforma em lista JSON;
4.	SplitOut quebra em itens;
5.	Loop envia um por um com atraso de 1s;
6.	Resultado final: atendimento natural e humano;
________________________________________
✔ 4. Looping de Mensagens + Telegram + Captação de Sentimentos Mensagens + Log de Performance + Planilha de Dados + Fallback

Responsável por:
1.	- O looping irá enviar cada parágrafo por vez conforme divididos pelo agente JSON;
2.	- Timer de 1s para humanizar o envio de cada parágrafo;
3.	- Enviar mensagem via Telegram;
4.	- IA que analisa o sentimento das mensagens;
5.	- Coleta de dados para log de performance (tempo de resposta, sentimento mensagens, data e hora, entre outros);
6.	- Gravação de log para planilha no Google Sheets;
7.	- Saída de fallback;
________________________________________
🎯 Componentes Principais

1. Telegram Trigger
   
Entrada inicial.

Aciona o fluxo sempre que o usuário envia:
•	Texto;
•	Áudio;
________________________________________
2. SET PARÂMETROS
   
Organiza tudo que o agente precisa:
- Session;
-	Nome;
-	Mensagem;
-	Voz;
-	Cardápio;
-	Timestamp;
-	Identificação da conversa;
________________________________________

3. Switch para Voz ou Texto
   
Define:
-	Se o fluxo deve baixar áudio;
-	Ou seguir com texto normal;
________________________________________
4. Unificação (Unifica Parâmetro)
   
Junta transcrição, texto e IDs em um único JSON limpo.
________________________________________
5. Memory Buffer
   
Mantém as últimas 10 mensagens para dar contexto ao agente.
________________________________________
6. AGENTE IA
   
O mais importante.

Possui:
-	Regras;
-	Cardápio;
-	Fluxo;
-	Ferramentas;
-	Validações;
-	Lógica de negócio;
-	Decisões automáticas do fluxo;

Mantém coerência entre etapas:
-	Pedido selecionado;
-	Extras;
-	Valor total;
-	Nome do cliente;
-	Status do pedido;
________________________________________

7. Tools
   
- Enviar Menu Inicial;
- Enviar Cardápio;
- Consultar Pedido;
- Criar Novo Pedido;
- Atualizar/Cancelar Pedido;
- Calculadora;
________________________________________
8. Agente JSON
   
O Agente JSON foi criado para separar a resposta do Agente IA em parágrafos,
permitindo um envio humanizado e natural via Telegram.
________________________________________
10. SplitOut + Loop
    
Envia cada parágrafo separadamente (humanização).
________________________________________
10. Log de Performance
    
Escreve no Sheets:
-	tempo de resposta;
-	sentimento;
-	input_length;
-	saída (quantidade de parágrafos);
  
________________________________________
📊 Planilhas (Google Sheets)

📌 Planilha 1 — Pedidos

Campos:
-	pedido_id;
-	chatId;
-	nome_cliente;
-	itens;
-	extras;
-	valor_total;
-	status_pedido;
-	criado_em;
-	observações;
  
📌 Planilha 2 — Log de Performance

Campos:
-	session_id;
-	user_id;
-	mime_type;
-	input_length;
-	sentimento;
-	tempo_resposta;
-	criado_em;
-	dia;
-	hora;
________________________________________
📐 Regras da IA (Resumo Técnico)

1.	Sempre enviar menu inicial na primeira interação
2.	Sempre enviar cardápio quando solicitado
3.	Nunca inventar pratos
4.	Confirmar tudo antes de registrar pedido
5.	Gerar sempre JSON final para criação ou edição
6.	Nunca alterar número do pedido
7.	Cancelamento muda apenas status para “cancelado”
8.	IA deve chamar THINK antes de agir
9.	Ao usar tool, não repetir texto da própria tool

________________________________________

📊 Dashboard Operacional — Lovable

Foi criado um dashboard público para visualização dos dados gerados pela automação.

Link: https://marmitaria.lovable.app/

O dashboard apresenta:

- Total de pedidos por status;
- Pedidos por dia;
- Horário de pico;
- Quantidade de mensagens processadas;
- Análise de sentimentos;
- Conversas por usuário;
- Tempo de resposta da IA;
- Evolução da operação em tempo real;

Objetivo:

Fornecer uma visão executiva da operação da marmitaria, permitindo:

- tomada de decisões;
- análise de crescimento;
- acompanhamento de volume;
- monitoramento de performance da IA;
