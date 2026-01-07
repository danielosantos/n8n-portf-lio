## Visão Geral

Este projeto implementa uma **arquitetura de suporte automatizado inteligente**, construída em **n8n**, que combina:

* Atendimento via **WhatsApp**
* **Agente de IA com RAG**
* **Consulta transacional em tempo real na Hotmart**
* **Memória de conversa com Redis**
* **Controle de intervenção humana**
* Tools de Consulta **MCP na Hotmart**

O sistema foi projetado para **resolver demandas de suporte digital** com **clareza, escala e segurança**, indo além de automações simples e adotando princípios de **arquitetura orientada a responsabilidades**.

---

## Contexto de Negócio

### Quem usa

* Alunas e seguidoras de produtos digitais (cursos, mentorias e assinaturas)
* Equipe de suporte (nível 1 e nível 2)

### Cenário

* Alto volume de mensagens no WhatsApp
* Dúvidas recorrentes sobre:

  * Acesso
  * Conteúdo
  * Pagamentos
  * Status de compra
* Períodos de pico e recesso do suporte humano

### Dor resolvida

* Sobrecarga humana
* Respostas inconsistentes
* Falta de visibilidade sobre compras
* Baixa escalabilidade do atendimento manual

---

## Identificação de Componentes (Arquitetura)

### 🔔 Triggers

* **Webhook (WhatsApp / Evolution API)**
  Entrada principal de mensagens do usuário
* **Execute Workflow Trigger**
  Workflow MCP Hotmart é acionado por outros fluxos

---

### 🧠 Processamento

* Normalização de mensagens (texto, áudio, imagem, documento)
* Agrupamento de mensagens em janela temporal
* Decisão de uso de ferramentas (RAG vs Hotmart)
* Orquestração da IA (Agente)
* Controle de bloqueio para intervenção humana

---

### 🛠️ Ferramentas

* **IA (OpenAI / GPT-4o-mini)**
* **RAG Híbrido com SUPABASE (Base de Conhecimento interna)**
* **Ferramenta MCP Hotmart** (consulta de compras)
* **Transcrição de áudio**
* **Análise de imagem**
* **Extração de texto de PDF**

---

### 🧠 Memória

* **Redis**

  * Histórico de conversa (Chat Memory)
  * Buffer de mensagens
  * Controle de bloqueio (TTL)
  * Identificação de sessão por telefone

---

### 🌐 Integrações Externas

* WhatsApp (Evolution API)
* OpenAI
* Hotmart API
* Redis
* Google Drive (base de documentos)

---

## 3.2 Separação de Responsabilidades

| Função                             | Responsável                                    |
| ---------------------------------- | ---------------------------------------------- |
| **O que decide?**                  | Agente de IA (LangChain) com regras explícitas |
| **O que executa?**                 | Workflows n8n (orquestração)                   |
| **O que armazena?**                | Redis (estado, memória, bloqueios)             |
| **O que responde?**                | IA → WhatsApp (HTTP Request)                   |
| **O que consulta dados críticos?** | MCP Hotmart                                    |
| **O que valida contexto oficial?** | RAG                                            |

👉 Essa separação garante **controle, previsibilidade e auditabilidade**.

---

## 4.3 Arquitetura

### Diagrama (ASCII)

```
[WhatsApp]
    |
    v
[Webhook n8n]
    |
    v
[Normalização + Filtros]
    |
    v
[Redis Buffer + Memória]
    |
    v
[Agente IA]
    |         |
    |         +--> [RAG]
    |
    +--> [MCP Hotmart]
    |
    v
[Resposta WhatsApp]
```

### Componentes-chave

* **Workflow Agente de Suporte**: orquestra toda a conversa
* **Workflow MCP Hotmart**: microserviço de consulta transacional
* **Redis**: estado e memória desacoplados
* **IA**: decisão, não execução

---

## 4.4 Fluxo de Operação

1. **Entrada**

   * Usuária envia mensagem via WhatsApp

2. **Decisão**

   * Sistema identifica tipo de mensagem
   * Verifica bloqueio humano
   * IA decide:

     * Consultar RAG?
     * Consultar Hotmart?
     * Pedir mais dados?

3. **Execução**

   * Consulta APIs necessárias
   * Processa resposta contextual
   * Mantém histórico no Redis

4. **Resposta**

   * Mensagem humanizada enviada ao WhatsApp
   * Em casos críticos → encaminha para suporte humano

---

## 4.5 Exemplo de Uso

**Usuária:**

> “Paguei o curso, mas não recebi acesso. Tá certo isso?”

**Comportamento do sistema:**

1. IA identifica tema financeiro
2. Solicita e-mail da usuária
3. Aciona MCP Hotmart
4. Retorna status real da compra
5. Explica procedimento correto
6. Se necessário, encaminha ao suporte nível 2

---

## 4.6 Benefícios

### ⏱️ Tempo

* Redução estimada de **60–80%** no tempo de resposta

### 📈 Escala

* Atendimento simultâneo ilimitado

### 🧠 Clareza

* Respostas padronizadas e oficiais

### 🛠️ Redução de esforço humano

* Suporte humano focado apenas em exceções críticas

---

## 4.7 Configuração

### Necessário configurar

* Credenciais:
  * OpenAI
  * Redis
  * Hotmart
  * WhatsApp (Evolution API)
* URLs e Webhooks
* Base de Conhecimento (RAG)

### Onde configurar

* Nodes `Credenciais`
* Nodes HTTP Request
* Redis credentials
* Agent Prompt

### O que **não** está incluso

* Criação da base RAG
* Infraestrutura Redis
* Conta Hotmart / WhatsApp
* Monitoramento externo (logs / APM)

---

## 🧠 Conclusão Técnica

Este projeto **não é apenas automação**.
Ele representa uma **arquitetura de suporte inteligente**, com:

* Decisão desacoplada
* Ferramentas especializadas
* Memória persistente
* Governança de IA
* Escalabilidade real

Um **case sólido de n8n como plataforma de orquestração arquitetural**, não apenas de fluxos.