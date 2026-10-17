# Conecta Company - Atendimento via Chat com Amazon Connect e Lex V2

Este projeto implementa uma solução serverless de contact center em nuvem utilizando **Amazon Connect** e **Amazon Lex V2**. O sistema permite que clientes iniciem interações via chat em um site estático, naveguem por um menu de autoatendimento baseado em Processamento de Linguagem Natural (NLP) e sejam roteados para agentes humanos em filas específicas.

**🌐 Live Demo:** [Acessar o Frontend Hospedado no S3](http://conecta-joao.santos-site.s3-website-us-east-1.amazonaws.com)

## 🚀 Tecnologias e Serviços AWS Utilizados

*   **Amazon Connect**: Configuração de instâncias (`conecta-company-joao-coelho`), criação de filas dedicadas por departamento (Matrículas, Cursos, Suporte Técnico, Financeiro, Comercial), routing profiles e operação no painel Contact Control Panel (CCP).
*   **Amazon Lex V2**: Construção de bot conversacional com 7 intents customizadas (ex: `IntentMatriculas`, `IntentSuporteTecnico`, `IntentFinanceiro`). Implementação de submenus orientados a opções dinâmicas.
*   **Amazon S3**: Static Website Hosting para o frontend.
*   **HTML/JS**: Integração assíncrona do Communication Widget do Connect.

## 🏗️ Arquitetura e Roteamento (Contact Flow)

O Contact Flow foi desenhado para maximizar a retenção no autoatendimento. A arquitetura de decisão baseia-se no bloco `Get customer input` integrado ao Lex para interpretar as intenções do usuário antes de alocar recursos de atendimento humano.

![Arquitetura do Projeto](./docs/arquitetura.svg)

### Fluxo de Decisão (Contact Flow)
O roteamento valida parâmetros dinâmicos e transfere o contato para as filas correspondentes. 

![Diagrama do Contact Flow](./docs/ContactFlowMenu.png)

### Tratamento de Submenus (NLP)
O bot Lex foi configurado para interpretar linguagem natural e jargões corporativos e acadêmicos. Por exemplo, no submenu de Matrículas, o sistema reconhece sinônimos como "grade curricular", "o que vou aprender" e "carga horária" para acionar os fluxos de forma fluida, mitigando falhas por dependência exclusiva de inputs numéricos.

![Intents do Lex V2](./docs/BotAlexIntents.png)

## 📊 Métricas e Evidências de Funcionamento

O diretório `/evidencias` contém os artefatos visuais que comprovam a operação fim a fim da arquitetura. A bateria de testes comprovou a robustez do bot Lex e a transferência de contexto para o painel CCP.

1.  **Testes de Roteamento e Interação (`/evidencias/vídeos/Customer - Test chat.mp4`)**:
    *   **Fluxo de Matrículas e Cursos:** Demonstração da navegação bem-sucedida pelo menu principal e submenus. O bot processa inputs numéricos e palavras-chave (como "conclusão"), encaminhando o cliente para a fila correta. O agente ("Admin") aceita o chat no Contact Control Panel e o atende com base no histórico da conversa previamente carregado na tela.
    *   **Tratamento de Exceções (Timeout e Fallback):** Capacidade de tratar opções inválidas. Após entradas não reconhecidas, o sistema retorna uma mensagem de erro ("Opção inválida, repetição n") e, ao exceder o limite, informa a falha ("Não entendi sua solicitação") e redireciona o usuário novamente para as opções principais.
    *   **Atendimento Direto (Suporte Técnico):** A simulação da opção "3" atesta que o roteamento direto para uma fila contorna os submenus, com o agente aceitando prontamente a chamada.

2.  **Contact Search & Inspeção de Contatos**: 
    *   Múltiplos contatos foram registrados e concluídos com sucesso (Contact Status: *Completed*).
    *   A inspeção detalhada de contatos individuais (`DetalhesContato.png`) atesta o registro correto do canal (Chat), fila de destino (ex: `BasicQueue`) e motivo de desconexão (*Customer disconnect*).
    
3.  **Historical Metrics (Relatórios de Desempenho)**:
    *   **Análise de Filas (`Queue.png`)**: O *Service Level* atingiu **95.00%** para atendimentos em até 60 e 120 segundos na fila principal (`BasicQueue`). O Tempo Médio de Interação do Agente (*Average agent interaction time*) foi de aproximadamente **29m37s**, refletindo o volume de testes no CCP.
    *   **Análise de Agentes (`Agents.png`)**: O relatório de performance atesta a operação e disponibilidade. A conta `agente01` operou com **100% de Answer Rate** e Occupancy total, validando a eficácia do roteamento.

## 📁 Estrutura do Repositório

```text
/
├── frontend/
│   ├── index.html 
│   └── styles.css
├── evidencias/
│   ├── screenshots/
│   │   ├── site_widget_minimizado.png
│   │   ├── SiteComChatWidgetVisível.png
│   │   ├── conversa_agente_cliente.png
│   │   ├── ConversaMatriculas.png
│   │   ├── ListaContatos.png
│   │   ├── DetalhesContato.png
│   │   ├── Agents.png
│   │   └── Queue.png
│   └── vídeos/
│       ├── demo.mp4
│       └── Customer - Test chat.mp4
├── docs/
│   ├── arquitetura.svg 
│   ├── fluxo-negocio-modulo2.png 
│   ├── contact-flow.svg 
│   ├── ContactFlowMenu.jpg 
│   └── BotAlexIntents.png 
└── README.md
