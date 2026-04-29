## Module: Telegram Bot Builder
---
name: telegram-bot-builder
description: "Expert in building Telegram bots that solve real problems - from simple automation to complex AI-powered bots. Covers bot architecture, the Telegram Bot API, user experience, monetization strategie..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Telegram Bot Builder

**Role**: Telegram Bot Architect

You build bots that people actually use daily. You understand that bots
should feel like helpful assistants, not clunky interfaces. You know
the Telegram ecosystem deeply - what's possible, what's popular, and
what makes money. You design conversations that feel natural.

## Capabilities

- Telegram Bot API
- Bot architecture
- Command design
- Inline keyboards
- Bot monetization
- User onboarding
- Bot analytics
- Webhook management

## Patterns

### Bot Architecture

Structure for maintainable Telegram bots

**When to use**: When starting a new bot project

```python
## Bot Architecture

### Stack Options
| Language | Library | Best For |
|----------|---------|----------|
| Node.js | telegraf | Most projects |
| Node.js | grammY | TypeScript, modern |
| Python | python-telegram-bot | Quick prototypes |
| Python | aiogram | Async, scalable |

### Basic Telegraf Setup
```javascript
import { Telegraf } from 'telegraf';

const bot = new Telegraf(process.env.BOT_TOKEN);

// Command handlers
bot.start((ctx) => ctx.reply('Welcome!'));
bot.help((ctx) => ctx.reply('How can I help?'));

// Text handler
bot.on('text', (ctx) => {
  ctx.reply(`You said: ${ctx.message.text}`);
});

// Launch
bot.launch();

// Graceful shutdown
process.once('SIGINT', () => bot.stop('SIGINT'));
process.once('SIGTERM', () => bot.stop('SIGTERM'));
```

### Project Structure
```
telegram-bot/
├── src/
│   ├── bot.js           # Bot initialization
│   ├── commands/        # Command handlers
│   │   ├── start.js
│   │   ├── help.js
│   │   └── settings.js
│   ├── handlers/        # Message handlers
│   ├── keyboards/       # Inline keyboards
│   ├── middleware/      # Auth, logging
│   └── services/        # Business logic
├── .env
└── package.json
```
```

### Inline Keyboards

Interactive button interfaces

**When to use**: When building interactive bot flows

```python
## Inline Keyboards

### Basic Keyboard
```javascript
import { Markup } from 'telegraf';

bot.command('menu', (ctx) => {
  ctx.reply('Choose an option:', Markup.inlineKeyboard([
    [Markup.button.callback('Option 1', 'opt_1')],
    [Markup.button.callback('Option 2', 'opt_2')],
    [
      Markup.button.callback('Yes', 'yes'),
      Markup.button.callback('No', 'no'),
    ],
  ]));
});

// Handle button clicks
bot.action('opt_1', (ctx) => {
  ctx.answerCbQuery('You chose Option 1');
  ctx.editMessageText('You selected Option 1');
});
```

### Keyboard Patterns
| Pattern | Use Case |
|---------|----------|
| Single column | Simple menus |
| Multi column | Yes/No, pagination |
| Grid | Category selection |
| URL buttons | Links, payments |

### Pagination
```javascript
function getPaginatedKeyboard(items, page, perPage = 5) {
  const start = page * perPage;
  const pageItems = items.slice(start, start + perPage);

  const buttons = pageItems.map(item =>
    [Markup.button.callback(item.name, `item_${item.id}`)]
  );

  const nav = [];
  if (page > 0) nav.push(Markup.button.callback('◀️', `page_${page-1}`));
  if (start + perPage < items.length) nav.push(Markup.button.callback('▶️', `page_${page+1}`));

  return Markup.inlineKeyboard([...buttons, nav]);
}
```
```

### Bot Monetization

Making money from Telegram bots

**When to use**: When planning bot revenue

```javascript
## Bot Monetization

### Revenue Models
| Model | Example | Complexity |
|-------|---------|------------|
| Freemium | Free basic, paid premium | Medium |
| Subscription | Monthly access | Medium |
| Per-use | Pay per action | Low |
| Ads | Sponsored messages | Low |
| Affiliate | Product recommendations | Low |

### Telegram Payments
```javascript
// Create invoice
bot.command('buy', (ctx) => {
  ctx.replyWithInvoice({
    title: 'Premium Access',
    description: 'Unlock all features',
    payload: 'premium_monthly',
    provider_token: process.env.PAYMENT_TOKEN,
    currency: 'USD',
    prices: [{ label: 'Premium', amount: 999 }], // $9.99
  });
});

// Handle successful payment
bot.on('successful_payment', (ctx) => {
  const payment = ctx.message.successful_payment;
  // Activate premium for user
  await activatePremium(ctx.from.id);
  ctx.reply('🎉 Premium activated!');
});
```

### Freemium Strategy
```
Free tier:
- 10 uses per day
- Basic features
- Ads shown

Premium ($5/month):
- Unlimited uses
- Advanced features
- No ads
- Priority support
```

### Usage Limits
```javascript
async function checkUsage(userId) {
  const usage = await getUsage(userId);
  const isPremium = await checkPremium(userId);

  if (!isPremium && usage >= 10) {
    return { allowed: false, message: 'Daily limit reached. Upgrade?' };
  }
  return { allowed: true };
}
```
```

## Anti-Patterns

### ❌ Blocking Operations

**Why bad**: Telegram has timeout limits.
Users think bot is dead.
Poor experience.
Requests pile up.

**Instead**: Acknowledge immediately.
Process in background.
Send update when done.
Use typing indicator.

### ❌ No Error Handling

**Why bad**: Users get no response.
Bot appears broken.
Debugging nightmare.
Lost trust.

**Instead**: Global error handler.
Graceful error messages.
Log errors for debugging.
Rate limiting.

### ❌ Spammy Bot

**Why bad**: Users block the bot.
Telegram may ban.
Annoying experience.
Low retention.

**Instead**: Respect user attention.
Consolidate messages.
Allow notification control.
Quality over quantity.

## Related Skills

Works well with: `telegram-mini-app`, `backend`, `ai-wrapper-product`, `workflow-automation`

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: amazon-alexa
description: Integracao completa com Amazon Alexa para criar skills de voz inteligentes, transformar Alexa em assistente com AI assistant como cerebro (projeto Auri) e integrar com AWS ecosystem (Lambda, DynamoDB,...
risk: safe
source: community
date_added: '2026-03-06'
author: renat
tags:
- voice
- alexa
- aws
- smart-home
- iot
tools:
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
---

# AMAZON ALEXA — Voz Inteligente com AI assistant

## Overview

Integracao completa com Amazon Alexa para criar skills de voz inteligentes, transformar Alexa em assistente com AI assistant como cerebro (projeto Auri) e integrar com AWS ecosystem (Lambda, DynamoDB, Polly, Transcribe, Lex, Smart Home).

## When to Use This Skill

- When you need specialized assistance with this domain

## Do Not Use This Skill When

- The task is unrelated to amazon alexa
- A simpler, more specific tool can handle the request
- The user needs general-purpose assistance without domain expertise

## How It Works

> Voce e o especialista em Alexa e AWS Voice. Missao: transformar
> qualquer dispositivo Alexa em assistente ultra-inteligente usando
> AI assistant como LLM backend, com voz neural, memoria persistente e
> controle de Smart Home. Projeto-chave: AURI.

---

## 1. Visao Geral Do Ecossistema

```
[Alexa Device] → [Alexa Cloud] → [AWS Lambda] → [LLM API]
    Fala          Transcricao      Logica          Inteligencia
      ↑               ↑               ↑                ↑
   Usuario         Intent        Handler          Anthropic
                               + DynamoDB
                               + Polly TTS
                               + APL Visual
```

## Componentes Da Arquitetura Auri

| Componente | Servico AWS | Funcao |
|-----------|-------------|--------|
| Voz → Texto | Alexa ASR nativo | Reconhecimento de fala |
| NLU | ASK Interaction Model + Lex V2 | Extrair intent e slots |
| Backend | AWS Lambda (Python/Node.js) | Logica e orquestracao |
| LLM | LLM API (Anthropic) | Inteligencia e respostas |
| Persistencia | Amazon DynamoDB | Historico e preferencias |
| Texto → Voz | Amazon Polly (neural) | Fala natural da Auri |
| Interface Visual | APL (Alexa Presentation Language) | Telas em Echo Show |
| Smart Home | Alexa Smart Home API | Controle de dispositivos |
| Automacao | Alexa Routines API | Rotinas inteligentes |

---

## 2.1 Pre-Requisitos

```bash

## Ask Cli

npm install -g ask-cli
ask configure

## Aws Cli

pip install awscli
aws configure
```

## Criar Skill Com Template

ask new \
  --template hello-world \
  --skill-name auri \
  --language pt-BR

## └── .Ask/Ask-Resources.Json

```

## 2.3 Configurar Invocation Name

No arquivo `models/pt-BR.json`:
```json
{
  "interactionModel": {
    "languageModel": {
      "invocationName": "auri"
    }
  }
}
```

---

## 3.1 Intents Essenciais Para Auri

```json
{
  "interactionModel": {
    "languageModel": {
      "invocationName": "auri",
      "intents": [
        {"name": "AMAZON.HelpIntent"},
        {"name": "AMAZON.StopIntent"},
        {"name": "AMAZON.CancelIntent"},
        {"name": "AMAZON.FallbackIntent"},
        {
          "name": "ChatIntent",
          "slots": [{"name": "query", "type": "AMAZON.SearchQuery"}],
          "samples": [
            "{query}",
            "me ajuda com {query}",
            "quero saber sobre {query}",
            "o que voce sabe sobre {query}",
            "explique {query}",
            "pesquise {query}"
          ]
        },
        {
          "name": "SmartHomeIntent",
          "slots": [
            {"name": "device", "type": "AMAZON.Room"},
            {"name": "action", "type": "ActionType"}
          ],
          "samples": [
            "{action} a {device}",
            "controla {device}",
            "acende {device}",
            "apaga {device}"
          ]
        },
        {
          "name": "RoutineIntent",
          "slots": [{"name": "routine", "type": "RoutineType"}],
          "samples": [
            "ativa rotina {routine}",
            "executa {routine}",
            "modo {routine}"
          ]
        }
      ],
      "types": [
        {
          "name": "ActionType",
          "values": [
            {"name": {"value": "liga", "synonyms": ["acende", "ativa", "liga"]}},
            {"name": {"value": "desliga", "synonyms": ["apaga", "desativa", "desliga"]}}
          ]
        },
        {
          "name": "RoutineType",
          "values": [
            {"name": {"value": "bom dia", "synonyms": ["acordar", "manhã"]}},
            {"name": {"value": "boa noite", "synonyms": ["dormir", "descansar"]}},
            {"name": {"value": "trabalho", "synonyms": ["trabalhar", "foco"]}},
            {"name": {"value": "sair", "synonyms": ["saindo", "goodbye"]}}
          ]
        }
      ]
    }
  }
}
```

---

## 4.1 Handler Principal Python

```python
import os
import time
import anthropic
import boto3
from ask_sdk_core.skill_builder import SkillBuilder
from ask_sdk_core.handler_input import HandlerInput
from ask_sdk_core.utils import is_intent_name, is_request_type
from ask_sdk_model import Response
from ask_sdk_dynamodb_persistence_adapter import DynamoDbPersistenceAdapter

## ============================================================

@sb.request_handler(can_handle_func=is_request_type("LaunchRequest"))
def launch_handler(handler_input: HandlerInput) -> Response:
    attrs = handler_input.attributes_manager.persistent_attributes
    name = attrs.get("name", "")
    greeting = f"Oi{', ' + name if name else ''}! Eu sou a Auri. Como posso ajudar?"
    return (handler_input.response_builder
            .speak(greeting).ask("Em que posso ajudar?").response)


@sb.request_handler(can_handle_func=is_intent_name("ChatIntent"))
def chat_handler(handler_input: HandlerInput) -> Response:
    try:
        # Obter query
        slots = handler_input.request_envelope.request.intent.slots
        query = slots["query"].value if slots.get("query") else None
        if not query:
            return (handler_input.response_builder
                    .speak("Pode repetir? Nao entendi bem.").ask("Pode repetir?").response)

        # Carregar historico
        attrs = handler_input.attributes_manager.persistent_attributes
        history = attrs.get("history", [])

        # Montar mensagens para AI assistant
        messages = history[-MAX_HISTORY:]
        messages.append({"role": "user", "content": query})

        # Chamar AI assistant
        client = anthropic.Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
        response = client.messages.create(
            model=MODEL_NAME,
            max_tokens=512,
            system=AURI_SYSTEM_PROMPT,
            messages=messages
        )
        reply = response.content[0].text

        # Truncar para nao exceder timeout
        if len(reply) > MAX_RESPONSE_CHARS:
            reply = reply[:MAX_RESPONSE_CHARS] + "... Quer que eu continue?"

        # Salvar historico
        history.append({"role": "user", "content": query})
        history.append({"role": "assistant", "content": reply})
        attrs["history"] = history[-50:]  # Manter ultimas 50
        handler_input.attributes_manager.persistent_attributes = attrs
        handler_input.attributes_manager.save_persist

## 4.2 Variaveis De Ambiente Lambda

```
ANTHROPIC_API_KEY=sk-...  (armazenar em Secrets Manager)
DYNAMODB_TABLE=auri-users
AWS_REGION=us-east-1
```

## 4.3 Requirements.Txt

```
ask-sdk-core>=1.19.0
ask-sdk-dynamodb-persistence-adapter>=1.19.0
anthropic>=0.40.0
boto3>=1.34.0
```

---

## 5.1 Criar Tabela

```bash
aws dynamodb create-table \
  --table-name auri-users \
  --attribute-definitions AttributeName=userId,AttributeType=S \
  --key-schema AttributeName=userId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region us-east-1
```

## 5.2 Schema Do Usuario

```json
{
  "userId": "amzn1.ask.account.XXXXX",
  "name": "Joao",
  "history": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ],
  "preferences": {
    "language": "pt-BR",
    "voice": "Vitoria",
    "personality": "assistente profissional"
  },
  "smartHome": {
    "devices": {},
    "routines": {}
  },
  "updatedAt": 1740960000,
  "ttl": 1748736000
}
```

## 5.3 Ttl Automatico (Expirar Dados Antigos)

```python
import time

## Adicionar Ttl De 180 Dias Ao Salvar

attrs["ttl"] = int(time.time()) + (180 * 24 * 3600)
```

---

## 6.1 Vozes Disponiveis (Portugues)

| Voice | Idioma | Tipo | Recomendado |
|-------|--------|------|-------------|
| `Vitoria` | pt-BR | Neural | ✅ Auri PT-BR |
| `Camila` | pt-BR | Neural | Alternativa |
| `Ricardo` | pt-BR | Standard | Masculino |
| `Ines` | pt-PT | Neural | Portugal |

## 6.2 Integrar Polly Na Resposta

```python
import boto3
import base64

def synthesize_polly(text: str, voice_id: str = "Vitoria") -> str:
    """Retorna URL de audio Polly para usar em Alexa."""
    client = boto3.client("polly", region_name="us-east-1")
    response = client.synthesize_speech(
        Text=text,
        OutputFormat="mp3",
        VoiceId=voice_id,
        Engine="neural"
    )
    # Salvar em S3 e retornar URL
    # (necessario para usar audio customizado no Alexa)
    return upload_to_s3(response["AudioStream"].read())

def speak_with_polly(handler_input, text, voice_id="Vitoria"):
    """Retornar resposta usando voz Polly customizada via SSML."""
    audio_url = synthesize_polly(text, voice_id)
    ssml = f'<speak><audio src="{audio_url}"/></speak>'
    return handler_input.response_builder.speak(ssml)
```

## 6.3 Ssml Para Controle De Voz

```xml
<speak>
  <prosody rate="90%" pitch="+5%">
    Oi! Eu sou a Auri.
  </prosody>
  <break time="0.5s"/>
  <emphasis level="moderate">Como posso ajudar?</emphasis>
</speak>
```

---

## 7.1 Template De Chat

```json
{
  "type": "APL",
  "version": "2023.3",
  "theme": "dark",
  "mainTemplate": {
    "parameters": ["payload"],
    "items": [{
      "type": "Container",
      "width": "100%",
      "height": "100%",
      "backgroundColor": "#1a1a2e",
      "items": [
        {
          "type": "Text",
          "text": "AURI",
          "fontSize": "32px",
          "color": "#e94560",
          "textAlign": "center",
          "paddingTop": "20px"
        },
        {
          "type": "Text",
          "text": "${payload.lastResponse}",
          "fontSize": "24px",
          "color": "#ffffff",
          "padding": "20px",
          "maxLines": 8,
          "grow": 1
        },
        {
          "type": "Text",
          "text": "Diga algo para continuar...",
          "fontSize": "18px",
          "color": "#888888",
          "textAlign": "center",
          "paddingBottom": "20px"
        }
      ]
    }]
  }
}
```

## 7.2 Adicionar Apl Na Resposta

```python
@sb.request_handler(can_handle_func=is_intent_name("ChatIntent"))
def chat_with_apl(handler_input: HandlerInput) -> Response:
    # ... obter reply do AI assistant ...

    # Verificar se device suporta APL
    supported = handler_input.request_envelope.context.system.device.supported_interfaces
    has_apl = getattr(supported, "alexa_presentation_apl", None) is not None

    if has_apl:
        apl_directive = {
            "type": "Alexa.Presentation.APL.RenderDocument",
            "token": "auri-chat",
            "document": CHAT_APL_DOCUMENT,
            "datasources": {"payload": {"lastResponse": reply}}
        }
        handler_input.response_builder.add_directive(apl_directive)

    return handler_input.response_builder.speak(reply).ask("Mais alguma coisa?").response
```

---

## 8.1 Ativar Smart Home Skill

No `skill.json`, adicionar:
```json
{
  "apis": {
    "smartHome": {
      "endpoint": {
        "uri": "arn:aws:lambda:us-east-1:123456789:function:auri-smart-home"
      }
    }
  }
}
```

## 8.2 Handler De Smart Home

```python
def handle_smart_home_directive(event, context):
    namespace = event["directive"]["header"]["namespace"]
    name = event["directive"]["header"]["name"]
    endpoint_id = event["directive"]["endpoint"]["endpointId"]

    if namespace == "Alexa.PowerController":
        state = "ON" if name == "TurnOn" else "OFF"
        # Chamar sua API de smart home
        control_device(endpoint_id, {"power": state})
        return build_smart_home_response(endpoint_id, "powerState", state)

    elif namespace == "Alexa.BrightnessController":
        brightness = event["directive"]["payload"]["brightness"]
        control_device(endpoint_id, {"brightness": brightness})
        return build_smart_home_response(endpoint_id, "brightness", brightness)
```

## 8.3 Discovery De Dispositivos

```python
def handle_discovery(event, context):
    return {
        "event": {
            "header": {
                "namespace": "Alexa.Discovery",
                "name": "Discover.Response",
                "payloadVersion": "3"
            },
            "payload": {
                "endpoints": [
                    {
                        "endpointId": "light-sala-001",
                        "friendlyName": "Luz da Sala",
                        "displayCategories": ["LIGHT"],
                        "capabilities": [
                            {
                                "type": "AlexaInterface",
                                "interface": "Alexa.PowerController",
                                "version": "3"
                            },
                            {
                                "type": "AlexaInterface",
                                "interface": "Alexa.BrightnessController",
                                "version": "3"
                            }
                        ]
                    }
                ]
            }
        }
    }
```

---

## Deploy Completo (Skill + Lambda)

cd auri/
ask deploy

## Verificar Status

ask status

## Testar No Simulador

ask dialog --locale pt-BR

## Teste Especifico De Intent

ask simulate \
  --text "abrir auri" \
  --locale pt-BR \
  --skill-id amzn1.ask.skill.YOUR-SKILL-ID
```

## Criar Lambda Manualmente

aws lambda create-function \
  --function-name auri-skill \
  --runtime python3.11 \
  --role arn:aws:iam::ACCOUNT:role/auri-lambda-role \
  --handler lambda_function.handler \
  --timeout 8 \
  --memory-size 512 \
  --zip-file fileb://function.zip

## Adicionar Trigger Alexa

aws lambda add-permission \
  --function-name auri-skill \
  --statement-id alexa-skill-trigger \
  --action lambda:InvokeFunction \
  --principal alexa-appkit.amazon.com \
  --event-source-token amzn1.ask.skill.YOUR-SKILL-ID
```

## Usar Secrets Manager

aws secretsmanager create-secret \
  --name auri/anthropic-key \
  --secret-string '{"ANTHROPIC_API_KEY": "sk-..."}'

## Lambda Acessa Via Sdk:

import boto3, json
def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response['SecretString'])
```

---

## Fase 1 — Setup (Dia 1)

```
[ ] Conta Amazon Developer criada
[ ] Conta AWS configurada (free tier)
[ ] ASK CLI instalado e configurado
[ ] IAM Role criada com permissoes: Lambda, DynamoDB, Polly, Logs
[ ] Anthropic API key armazenada em Secrets Manager
```

## Fase 2 — Skill Base (Dia 2-3)

```
[ ] ask new --template hello-world --skill-name auri
[ ] Interaction model definido (pt-BR.json)
[ ] LaunchRequest handler funcionando
[ ] ChatIntent handler com AI assistant integrado
[ ] ask deploy funcionando
[ ] Teste basico no ASK simulator
```

## Fase 3 — Persistencia (Dia 4)

```
[ ] DynamoDB table criada
[ ] Persistencia de historico funcionando
[ ] TTL configurado
[ ] Preferencias do usuario salvas
```

## Fase 4 — Polly + Apl (Dia 5-6)

```
[ ] Polly integrado com voz Vitoria (neural)
[ ] APL template de chat criado
[ ] APL renderizando em Echo Show simulator
```

## Fase 5 — Smart Home (Opcional)

```
[ ] Smart Home skill habilitada
[ ] Discovery de dispositivos funcionando
[ ] PowerController implementado
[ ] Teste com device real
```

## Fase 6 — Publicacao

```
[ ] Teste completo de todas funcionalidades
[ ] Performance OK (< 8s timeout)
[ ] Certificacao Amazon submetida
[ ] Publicado na Alexa Skills Store
```

---

## 11. Comandos Rapidos

| Acao | Comando |
|------|---------|
| Criar skill | `ask new --template hello-world` |
| Deploy | `ask deploy` |
| Simular | `ask simulate --text "abre a auri"` |
| Dialog interativo | `ask dialog --locale pt-BR` |
| Ver logs | `ask smapi get-skill-simulation` |
| Validar modelo | `ask validate --locales pt-BR` |
| Exportar skill | `ask smapi export-package --skill-id ID` |
| Listar skills | `ask list skills` |

---

## 12. Referencias

- Boilerplate Python completo: `assets/boilerplate/lambda_function.py`
- Interaction model PT-BR: `assets/interaction-models/pt-BR.json`
- APL chat template: `assets/apl-templates/chat-interface.json`
- Smart Home examples: `references/smart-home-api.md`
- ASK SDK Python docs: https://github.com/alexa/alexa-skills-kit-sdk-for-python
- AI assistant + Alexa guide: vendor AI assistant integration guide

## Best Practices

- Provide clear, specific context about your project and requirements
- Review all suggestions before applying them to production code
- Combine with other complementary skills for comprehensive analysis

## Common Pitfalls

- Using this skill for tasks outside its domain expertise
- Applying recommendations without understanding your specific context
- Not providing enough project context for accurate analysis

---

## Imported Reference

---
name: automate-whatsapp
description: Automate Whatsapp
---

404: Not Found

---

## Imported Reference

---
name: integrate-whatsapp
description: Integrate Whatsapp
---

404: Not Found

---

## Imported Reference

---
name: observe-whatsapp
description: Observe Whatsapp
---

404: Not Found

---

## Imported Reference

---
name: slack-bot-builder
description: "Build Slack apps using the Bolt framework across Python, JavaScript, and Java. Covers Block Kit for rich UIs, interactive components, slash commands, event handling, OAuth installation flows, and W..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Slack Bot Builder

## Patterns

### Bolt App Foundation Pattern

The Bolt framework is Slack's recommended approach for building apps.
It handles authentication, event routing, request verification, and
HTTP request processing so you can focus on app logic.

Key benefits:
- Event handling in a few lines of code
- Security checks and payload validation built-in
- Organized, consistent patterns
- Works for experiments and production

Available in: Python, JavaScript (Node.js), Java


**When to use**: ['Starting any new Slack app', 'Migrating from legacy Slack APIs', 'Building production Slack integrations']

```python
# Python Bolt App
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
import os

# Initialize with tokens from environment
app = App(
    token=os.environ["SLACK_BOT_TOKEN"],
    signing_secret=os.environ["SLACK_SIGNING_SECRET"]
)

# Handle messages containing "hello"
@app.message("hello")
def handle_hello(message, say):
    """Respond to messages containing 'hello'."""
    user = message["user"]
    say(f"Hey there <@{user}>!")

# Handle slash command
@app.command("/ticket")
def handle_ticket_command(ack, body, client):
    """Handle /ticket slash command."""
    # Acknowledge immediately (within 3 seconds)
    ack()

    # Open a modal for ticket creation
    client.views_open(
        trigger_id=body["trigger_id"],
        view={
            "type": "modal",
            "callback_id": "ticket_modal",
            "title": {"type": "plain_text", "text": "Create Ticket"},
            "submit": {"type": "plain_text", "text": "Submit"},
            "blocks": [
                {
                    "type": "input",
                    "block_id": "title_block",
                    "element": {
                        "type": "plain_text_input",
                        "action_id": "title_input"
                    },
                    "label": {"type": "plain_text", "text": "Title"}
                },
                {
                    "type": "input",
                    "block_id": "desc_block",
                    "element": {
                        "type": "plain_text_input",
                        "multiline": True,
                        "action_id": "desc_input"
                    },
                    "label": {"type": "plain_text", "text": "Description"}
                },
                {
                    "type": "input",
                    "block_id": "priority_block",
                    "element": {
                        "type": "static_select",
                        "action_id": "priority_select",
   
```

### Block Kit UI Pattern

Block Kit is Slack's UI framework for building rich, interactive messages.
Compose messages using blocks (sections, actions, inputs) and elements
(buttons, menus, text inputs).

Limits:
- Up to 50 blocks per message
- Up to 100 blocks in modals/Home tabs
- Block text limited to 3000 characters

Use Block Kit Builder to prototype: https://app.slack.com/block-kit-builder


**When to use**: ['Building rich message layouts', 'Adding interactive components to messages', 'Creating forms in modals', 'Building Home tab experiences']

```python
from slack_bolt import App
import os

app = App(token=os.environ["SLACK_BOT_TOKEN"])

def build_notification_blocks(incident: dict) -> list:
    """Build Block Kit blocks for incident notification."""
    severity_emoji = {
        "critical": ":red_circle:",
        "high": ":large_orange_circle:",
        "medium": ":large_yellow_circle:",
        "low": ":white_circle:"
    }

    return [
        # Header
        {
            "type": "header",
            "text": {
                "type": "plain_text",
                "text": f"{severity_emoji.get(incident['severity'], '')} Incident Alert"
            }
        },
        # Details section
        {
            "type": "section",
            "fields": [
                {
                    "type": "mrkdwn",
                    "text": f"*Incident:*\n{incident['title']}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Severity:*\n{incident['severity'].upper()}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Service:*\n{incident['service']}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Reported:*\n<!date^{incident['timestamp']}^{date_short} {time}|{incident['timestamp']}>"
                }
            ]
        },
        # Description
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": f"*Description:*\n{incident['description'][:2000]}"
            }
        },
        # Divider
        {"type": "divider"},
        # Action buttons
        {
            "type": "actions",
            "block_id": f"incident_actions_{incident['id']}",
            "elements": [
                {
                    "type": "button",
                    "text": {"type": "plain_text", "text": "Acknowledge"},
                    "style": "primary",
                    "action_id": "acknowle
```

### OAuth Installation Pattern

Enable users to install your app in their workspaces via OAuth 2.0.
Bolt handles most of the OAuth flow, but you need to configure it
and store tokens securely.

Key OAuth concepts:
- Scopes define permissions (request minimum needed)
- Tokens are workspace-specific
- Installation data must be stored persistently
- Users can add scopes later (additive)

70% of users abandon installation when confronted with excessive
permission requests - request only what you need!


**When to use**: ['Distributing app to multiple workspaces', 'Building public Slack apps', 'Enterprise-grade integrations']

```python
from slack_bolt import App
from slack_bolt.oauth.oauth_settings import OAuthSettings
from slack_sdk.oauth.installation_store import FileInstallationStore
from slack_sdk.oauth.state_store import FileOAuthStateStore
import os

# For production, use database-backed stores
# For example: PostgreSQL, MongoDB, Redis

class DatabaseInstallationStore:
    """Store installation data in your database."""

    async def save(self, installation):
        """Save installation when user completes OAuth."""
        await db.installations.upsert({
            "team_id": installation.team_id,
            "enterprise_id": installation.enterprise_id,
            "bot_token": encrypt(installation.bot_token),
            "bot_user_id": installation.bot_user_id,
            "bot_scopes": installation.bot_scopes,
            "user_id": installation.user_id,
            "installed_at": installation.installed_at
        })

    async def find_installation(self, *, enterprise_id, team_id, user_id=None, is_enterprise_install=False):
        """Find installation for a workspace."""
        record = await db.installations.find_one({
            "team_id": team_id,
            "enterprise_id": enterprise_id
        })

        if record:
            return Installation(
                bot_token=decrypt(record["bot_token"]),
                # ... other fields
            )
        return None

# Initialize OAuth-enabled app
app = App(
    signing_secret=os.environ["SLACK_SIGNING_SECRET"],
    oauth_settings=OAuthSettings(
        client_id=os.environ["SLACK_CLIENT_ID"],
        client_secret=os.environ["SLACK_CLIENT_SECRET"],
        scopes=[
            "channels:history",
            "channels:read",
            "chat:write",
            "commands",
            "users:read"
        ],
        user_scopes=[],  # User token scopes if needed
        installation_store=DatabaseInstallationStore(),
        state_store=FileOAuthStateStore(expiration_seconds=600)
    )
)

# OAuth routes are handled a
```

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | critical | ## Acknowledge immediately, process later |
| Issue | critical | ## Proper state validation |
| Issue | critical | ## Never hardcode or log tokens |
| Issue | high | ## Request minimum required scopes |
| Issue | medium | ## Know and respect the limits |
| Issue | high | ## Socket Mode: Only for development |
| Issue | critical | ## Bolt handles this automatically |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: slack-gif-creator
description: "Knowledge and utilities for creating animated GIFs optimized for Slack. Provides constraints, validation tools, and animation concepts. Use when users request animated GIFs for Slack like \"..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# Slack GIF Creator

A toolkit providing utilities and knowledge for creating animated GIFs optimized for Slack.

## Slack Requirements

**Dimensions:**
- Emoji GIFs: 128x128 (recommended)
- Message GIFs: 480x480

**Parameters:**
- FPS: 10-30 (lower is smaller file size)
- Colors: 48-128 (fewer = smaller file size)
- Duration: Keep under 3 seconds for emoji GIFs

## Core Workflow

```python
from core.gif_builder import GIFBuilder
from PIL import Image, ImageDraw

# 1. Create builder
builder = GIFBuilder(width=128, height=128, fps=10)

# 2. Generate frames
for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))
    draw = ImageDraw.Draw(frame)

    # Draw your animation using PIL primitives
    # (circles, polygons, lines, etc.)

    builder.add_frame(frame)

# 3. Save with optimization
builder.save('output.gif', num_colors=48, optimize_for_emoji=True)
```

## Drawing Graphics

### Working with User-Uploaded Images
If a user uploads an image, consider whether they want to:
- **Use it directly** (e.g., "animate this", "split this into frames")
- **Use it as inspiration** (e.g., "make something like this")

Load and work with images using PIL:
```python
from PIL import Image

uploaded = Image.open('file.png')
# Use directly, or just as reference for colors/style
```

### Drawing from Scratch
When drawing graphics from scratch, use PIL ImageDraw primitives:

```python
from PIL import ImageDraw

draw = ImageDraw.Draw(frame)

# Circles/ovals
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# Stars, triangles, any polygon
points = [(x1, y1), (x2, y2), (x3, y3), ...]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b), width=3)

# Lines
draw.line([(x1, y1), (x2, y2)], fill=(r, g, b), width=5)

# Rectangles
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)
```

**Don't use:** Emoji fonts (unreliable across platforms) or assume pre-packaged graphics exist in this skill.

### Making Graphics Look Good

Graphics should look polished and creative, not basic. Here's how:

**Use thicker lines** - Always set `width=2` or higher for outlines and lines. Thin lines (width=1) look choppy and amateurish.

**Add visual depth**:
- Use gradients for backgrounds (`create_gradient_background`)
- Layer multiple shapes for complexity (e.g., a star with a smaller star inside)

**Make shapes more interesting**:
- Don't just draw a plain circle - add highlights, rings, or patterns
- Stars can have glows (draw larger, semi-transparent versions behind)
- Combine multiple shapes (stars + sparkles, circles + rings)

**Pay attention to colors**:
- Use vibrant, complementary colors
- Add contrast (dark outlines on light shapes, light outlines on dark shapes)
- Consider the overall composition

**For complex shapes** (hearts, snowflakes, etc.):
- Use combinations of polygons and ellipses
- Calculate points carefully for symmetry
- Add details (a heart can have a highlight curve, snowflakes have intricate branches)

Be creative and detailed! A good Slack GIF should look polished, not like placeholder graphics.

## Available Utilities

### GIFBuilder (`core.gif_builder`)
Assembles frames and optimizes for Slack:
```python
builder = GIFBuilder(width=128, height=128, fps=10)
builder.add_frame(frame)  # Add PIL Image
builder.add_frames(frames)  # Add list of frames
builder.save('out.gif', num_colors=48, optimize_for_emoji=True, remove_duplicates=True)
```

### Validators (`core.validators`)
Check if GIF meets Slack requirements:
```python
from core.validators import validate_gif, is_slack_ready

# Detailed validation
passes, info = validate_gif('my.gif', is_emoji=True, verbose=True)

# Quick check
if is_slack_ready('my.gif'):
    print("Ready!")
```

### Easing Functions (`core.easing`)
Smooth motion instead of linear:
```python
from core.easing import interpolate

# Progress from 0.0 to 1.0
t = i / (num_frames - 1)

# Apply easing
y = interpolate(start=0, end=400, t=t, easing='ease_out')

# Available: linear, ease_in, ease_out, ease_in_out,
#           bounce_out, elastic_out, back_out
```

### Frame Helpers (`core.frame_composer`)
Convenience functions for common needs:
```python
from core.frame_composer import (
    create_blank_frame,         # Solid color background
    create_gradient_background,  # Vertical gradient
    draw_circle,                # Helper for circles
    draw_text,                  # Simple text rendering
    draw_star                   # 5-pointed star
)
```

## Animation Concepts

### Shake/Vibrate
Offset object position with oscillation:
- Use `math.sin()` or `math.cos()` with frame index
- Add small random variations for natural feel
- Apply to x and/or y position

### Pulse/Heartbeat
Scale object size rhythmically:
- Use `math.sin(t * frequency * 2 * math.pi)` for smooth pulse
- For heartbeat: two quick pulses then pause (adjust sine wave)
- Scale between 0.8 and 1.2 of base size

### Bounce
Object falls and bounces:
- Use `interpolate()` with `easing='bounce_out'` for landing
- Use `easing='ease_in'` for falling (accelerating)
- Apply gravity by increasing y velocity each frame

### Spin/Rotate
Rotate object around center:
- PIL: `image.rotate(angle, resample=Image.BICUBIC)`
- For wobble: use sine wave for angle instead of linear

### Fade In/Out
Gradually appear or disappear:
- Create RGBA image, adjust alpha channel
- Or use `Image.blend(image1, image2, alpha)`
- Fade in: alpha from 0 to 1
- Fade out: alpha from 1 to 0

### Slide
Move object from off-screen to position:
- Start position: outside frame bounds
- End position: target location
- Use `interpolate()` with `easing='ease_out'` for smooth stop
- For overshoot: use `easing='back_out'`

### Zoom
Scale and position for zoom effect:
- Zoom in: scale from 0.1 to 2.0, crop center
- Zoom out: scale from 2.0 to 1.0
- Can add motion blur for drama (PIL filter)

### Explode/Particle Burst
Create particles radiating outward:
- Generate particles with random angles and velocities
- Update each particle: `x += vx`, `y += vy`
- Add gravity: `vy += gravity_constant`
- Fade out particles over time (reduce alpha)

## Optimization Strategies

Only when asked to make the file size smaller, implement a few of the following methods:

1. **Fewer frames** - Lower FPS (10 instead of 20) or shorter duration
2. **Fewer colors** - `num_colors=48` instead of 128
3. **Smaller dimensions** - 128x128 instead of 480x480
4. **Remove duplicates** - `remove_duplicates=True` in save()
5. **Emoji mode** - `optimize_for_emoji=True` auto-optimizes

```python
# Maximum optimization for emoji
builder.save(
    'emoji.gif',
    num_colors=48,
    optimize_for_emoji=True,
    remove_duplicates=True
)
```

## Philosophy

This skill provides:
- **Knowledge**: Slack's requirements and animation concepts
- **Utilities**: GIFBuilder, validators, easing functions
- **Flexibility**: Create the animation logic using PIL primitives

It does NOT provide:
- Rigid animation templates or pre-made functions
- Emoji font rendering (unreliable across platforms)
- A library of pre-packaged graphics built into the skill

**Note on user uploads**: This skill doesn't include pre-built graphics, but if a user uploads an image, use PIL to load and work with it - interpret based on their request whether they want it used directly or just as inspiration.

Be creative! Combine concepts (bouncing + rotating, pulsing + sliding, etc.) and use PIL's full capabilities.

## Dependencies

```bash
pip install pillow imageio numpy
```

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: telegram
description: Integracao completa com Telegram Bot API. Setup com BotFather, mensagens, webhooks, inline keyboards, grupos, canais. Boilerplates Node.js e Python.
risk: critical
source: community
date_added: '2026-03-06'
author: renat
tags:
- messaging
- telegram
- bots
- webhooks
tools:
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
---

# Telegram Bot API - Integracao Profissional

## Overview

Integracao completa com Telegram Bot API. Setup com BotFather, mensagens, webhooks, inline keyboards, grupos, canais. Boilerplates Node.js e Python.

## When to Use This Skill

- When the user mentions "telegram" or related topics
- When the user mentions "bot telegram" or related topics
- When the user mentions "telegram bot" or related topics
- When the user mentions "api telegram" or related topics
- When the user mentions "chatbot telegram" or related topics
- When the user mentions "mensagem telegram" or related topics

## Do Not Use This Skill When

- The task is unrelated to telegram
- A simpler, more specific tool can handle the request
- The user needs general-purpose assistance without domain expertise

## How It Works

Skill para implementar bots profissionais no Telegram usando a Bot API oficial. Suporta Node.js/TypeScript e Python.

## Overview

A Telegram Bot API permite criar bots que interagem com usuarios via mensagens, comandos, inline keyboards, pagamentos e muito mais. Bots sao criados pelo @BotFather e autenticados via token unico.

**Base URL:** `https://api.telegram.org/bot<TOKEN>/METHOD_NAME`
**Metodos HTTP:** GET e POST
**Formatos de parametros:** query string, application/x-www-form-urlencoded, application/json, multipart/form-data (uploads)
**Limite de arquivos:** 50MB download, 20MB upload (via multipart), 50MB via URL

**Portas suportadas para webhooks:** 443, 80, 88, 8443

**Pre-requisitos:**
- Conta no Telegram
- Bot criado via @BotFather (fornece o token)
- Token no formato: `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`

Se o usuario nao tem um bot criado, oriente a conversar com @BotFather no Telegram e enviar `/newbot`.

---

## Decision Tree

```
O usuario precisa criar um bot?
├── SIM → Secao "Setup com BotFather" abaixo
└── NAO → Qual linguagem?
    ├── Node.js/TypeScript
    └── Python
    → O que quer fazer?
       ├── Enviar mensagens → Secao "Tipos de Mensagem"
       ├── Receber mensagens → Secao "Receber Updates"
       ├── Teclados interativos → Secao "Keyboards"
       ├── Gerenciar grupos/canais → references/chat-management.md
       ├── Webhook setup → references/webhook-setup.md
       ├── Inline mode → references/advanced-features.md
       ├── Pagamentos → references/advanced-features.md
       ├── Bot de atendimento com IA → Secao "Automacao com IA"
       └── Referencia completa da API → references/api-reference.md
```

Para iniciar um projeto do zero com boilerplate pronto:
```bash
python scripts/setup_project.py --language nodejs --path ./meu-bot-telegram

## Ou

python scripts/setup_project.py --language python --path ./meu-bot-telegram
```

Para testar se o token do bot funciona:
```bash
python scripts/test_bot.py --token "SEU_TOKEN"
```

Para enviar uma mensagem de teste:
```bash
python scripts/send_message.py --token "SEU_TOKEN" --chat-id "CHAT_ID" --text "Hello!"
```

---

## Setup Com Botfather

1. Abra o Telegram e busque @BotFather
2. Envie `/newbot`
3. Escolha nome de exibicao (ex: "Meu Bot Incrivel")
4. Escolha username (deve terminar com "bot", ex: `meu_incrivel_bot`)
5. BotFather retorna o token - guarde com seguranca
6. Comandos uteis do BotFather:
   - `/setdescription` - descricao do bot
   - `/setabouttext` - texto "sobre" do bot
   - `/setuserpic` - foto de perfil
   - `/setcommands` - lista de comandos
   - `/mybots` - gerenciar bots existentes
   - `/setinline` - habilitar inline mode
   - `/setprivacy` - modo privacidade em grupos

---

## Variaveis De Ambiente

```env
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
```

## Node.Js/Typescript

```typescript
// Instalar: npm install node-telegram-bot-api dotenv
// Para TypeScript: npm install -D @types/node-telegram-bot-api typescript
import TelegramBot from 'node-telegram-bot-api';
import dotenv from 'dotenv';
dotenv.config();

const bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN!, { polling: true });

bot.onText(/\/start/, (msg) => {
  bot.sendMessage(msg.chat.id, 'Ola! Eu sou seu bot. Como posso ajudar?');
});

bot.on('message', (msg) => {
  if (msg.text && !msg.text.startsWith('/')) {
    bot.sendMessage(msg.chat.id, `Voce disse: ${msg.text}`);
  }
});
```

## Python

```python

## Instalar: Pip Install Python-Telegram-Bot Python-Dotenv

import os
from dotenv import load_dotenv
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes

load_dotenv()

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text('Ola! Eu sou seu bot. Como posso ajudar?')

async def echo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(f'Voce disse: {update.message.text}')

app = Application.builder().token(os.getenv('TELEGRAM_BOT_TOKEN')).build()
app.add_handler(CommandHandler('start', start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, echo))
app.run_polling()
```

## Sem Biblioteca (Http Puro)

```python
import requests

TOKEN = "SEU_TOKEN"
BASE = f"https://api.telegram.org/bot{TOKEN}"

## Verificar Bot

r = requests.get(f"{BASE}/getMe")
print(r.json())

## Enviar Mensagem

r = requests.post(f"{BASE}/sendMessage", json={
    "chat_id": "CHAT_ID",
    "text": "Hello from pure HTTP!",
    "parse_mode": "HTML"
})
print(r.json())
```

---

## Tipos De Mensagem

O Telegram suporta diversos tipos de conteudo. Todos os metodos aceitam `chat_id`, `reply_parameters` (para responder), `reply_markup` (para keyboards), `disable_notification` e `protect_content`.

## Html (Recomendado)

await bot.send_message(
    chat_id=chat_id,
    text="<b>Negrito</b>, <i>italico</i>, <code>codigo</code>, <a href='https://example.com'>link</a>",
    parse_mode="HTML"
)

## Markdownv2 (Escapar Caracteres Especiais: _ * [ ] ( ) ~ ` > # + - = | { } . !)

await bot.send_message(
    chat_id=chat_id,
    text="*Negrito*, _italico_, `codigo`, [link](https://example\\.com)",
    parse_mode="MarkdownV2"
)
```

## Foto (Por Url, File_Id Ou Upload)

await bot.send_photo(chat_id, photo="https://example.com/img.jpg", caption="Legenda aqui")

## Documento

await bot.send_document(chat_id, document=open("relatorio.pdf", "rb"), caption="Relatorio mensal")

## Video

await bot.send_video(chat_id, video="https://example.com/video.mp4", caption="Assista!")

## Audio

await bot.send_audio(chat_id, audio=open("musica.mp3", "rb"), title="Minha Musica")

## Voz (Ogg Com Opus)

await bot.send_voice(chat_id, voice=open("audio.ogg", "rb"))

## Localizacao

await bot.send_location(chat_id, latitude=-23.5505, longitude=-46.6333)

## Contato

await bot.send_contact(chat_id, phone_number="+5511999999999", first_name="Joao")

## Enquete

await bot.send_poll(
    chat_id, question="Qual sua cor favorita?",
    options=["Azul", "Verde", "Vermelho"],
    is_anonymous=False
)

## Grupo De Midias

await bot.send_media_group(chat_id, media=[
    InputMediaPhoto("url1", caption="Foto 1"),
    InputMediaPhoto("url2"),
    InputMediaVideo("url3")
])

## Acao De Chat (Typing, Upload_Photo, Etc.)

await bot.send_chat_action(chat_id, action="typing")
```

## Node.Js Equivalente

```typescript
// Foto
bot.sendPhoto(chatId, 'https://example.com/img.jpg', { caption: 'Legenda' });

// Documento
bot.sendDocument(chatId, fs.createReadStream('relatorio.pdf'), { caption: 'Relatorio' });

// Localizacao
bot.sendLocation(chatId, -23.5505, -46.6333);

// Enquete
bot.sendPoll(chatId, 'Qual sua cor favorita?', ['Azul', 'Verde', 'Vermelho']);
```

---

## Inline Keyboard (Botoes Dentro Da Mensagem)

```python
from telegram import InlineKeyboardButton, InlineKeyboardMarkup

keyboard = InlineKeyboardMarkup([
    [InlineKeyboardButton("Opcao A", callback_data="opt_a"),
     InlineKeyboardButton("Opcao B", callback_data="opt_b")],
    [InlineKeyboardButton("Abrir Site", url="https://example.com")],
    [InlineKeyboardButton("Compartilhar", switch_inline_query="texto")]
])

await bot.send_message(chat_id, "Escolha uma opcao:", reply_markup=keyboard)

## Handler De Callback

async def button_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()  # Importante: sempre responder o callback
    await query.edit_message_text(f"Voce escolheu: {query.data}")

app.add_handler(CallbackQueryHandler(button_callback))
```

## Reply Keyboard (Teclado Customizado)

```python
from telegram import ReplyKeyboardMarkup, KeyboardButton

keyboard = ReplyKeyboardMarkup(
    [[KeyboardButton("Enviar Localizacao", request_location=True)],
     [KeyboardButton("Enviar Contato", request_contact=True)],
     ["Opcao 1", "Opcao 2"]],
    resize_keyboard=True,
    one_time_keyboard=True
)

await bot.send_message(chat_id, "Escolha:", reply_markup=keyboard)
```

## Remover Teclado

```python
from telegram import ReplyKeyboardRemove
await bot.send_message(chat_id, "Teclado removido", reply_markup=ReplyKeyboardRemove())
```

---

## Receber Updates

Existem duas formas de receber updates: **Long Polling** e **Webhooks**.

## Long Polling (Desenvolvimento)

Mais simples, ideal para desenvolvimento. O bot faz requisicoes periodicas ao servidor do Telegram.

```python

## Python-Telegram-Bot Ja Faz Isso Automaticamente

app.run_polling(allowed_updates=Update.ALL_TYPES)
```

```typescript
// node-telegram-bot-api com polling
const bot = new TelegramBot(token, { polling: true });
```

## Webhooks (Producao)

Para producao, webhooks sao mais eficientes. O Telegram envia updates via POST para sua URL HTTPS.

Leia `references/webhook-setup.md` para configuracao completa com Express, Flask, ngrok e deploy.

Setup rapido:

```python

## Flask Webhook

from flask import Flask, request
import requests

app = Flask(__name__)
TOKEN = "SEU_TOKEN"
BASE = f"https://api.telegram.org/bot{TOKEN}"

@app.route(f"/webhook/{TOKEN}", methods=["POST"])
def webhook():
    update = request.get_json()
    if "message" in update and "text" in update["message"]:
        chat_id = update["message"]["chat"]["id"]
        text = update["message"]["text"]
        requests.post(f"{BASE}/sendMessage", json={
            "chat_id": chat_id,
            "text": f"Recebi: {text}"
        })
    return "OK", 200

## Registrar Webhook

requests.post(f"{BASE}/setWebhook", json={
    "url": "https://seu-dominio.com/webhook/" + TOKEN,
    "allowed_updates": ["message", "callback_query"],
    "secret_token": "seu_secret_seguro_aqui"
})
```

---

## Comandos Do Bot

Registre comandos para aparecerem no menu do Telegram:

```python
from telegram import BotCommand

await bot.set_my_commands([
    BotCommand("start", "Iniciar o bot"),
    BotCommand("help", "Ver comandos disponiveis"),
    BotCommand("settings", "Configuracoes"),
    BotCommand("status", "Ver status do servico"),
])
```

Via HTTP:
```bash
curl -X POST "https://api.telegram.org/bot$TOKEN/setMyCommands" \
  -H "Content-Type: application/json" \
  -d '{"commands":[{"command":"start","description":"Iniciar o bot"},{"command":"help","description":"Ajuda"}]}'
```

---

## Automacao Com Ia

Padrao para bot de atendimento com IA (AI assistant, GPT, etc.):

```python
from telegram import Update
from telegram.ext import Application, MessageHandler, filters, ContextTypes
import anthropic  # ou openai

client = anthropic.Anthropic()
user_conversations = {}  # chat_id -> messages history

async def ai_response(update: Update, context: ContextTypes.DEFAULT_TYPE):
    chat_id = update.message.chat_id
    user_text = update.message.text

    # Indicar que esta digitando
    await context.bot.send_chat_action(chat_id, "typing")

    # Manter historico
    if chat_id not in user_conversations:
        user_conversations[chat_id] = []

    user_conversations[chat_id].append({"role": "user", "content": user_text})

    # Chamar IA
    response = client.messages.create(
        model="example-balanced-model",
        max_tokens=1024,
        system="Voce e um assistente prestativo. Responda em portugues.",
        messages=user_conversations[chat_id]
    )

    reply = response.content[0].text
    user_conversations[chat_id].append({"role": "assistant", "content": reply})

    # Limitar historico (ultimas 20 mensagens)
    if len(user_conversations[chat_id]) > 20:
        user_conversations[chat_id] = user_conversations[chat_id][-20:]

    await update.message.reply_text(reply)

app = Application.builder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, ai_response))
app.run_polling()
```

---

## Editar Texto

await bot.edit_message_text(
    chat_id=chat_id,
    message_id=msg.message_id,
    text="Texto atualizado!",
    parse_mode="HTML"
)

## Editar Markup (Botoes)

await bot.edit_message_reply_markup(
    chat_id=chat_id,
    message_id=msg.message_id,
    reply_markup=new_keyboard
)

## Deletar Mensagem

await bot.delete_message(chat_id=chat_id, message_id=msg.message_id)

## Encaminhar Mensagem

await bot.forward_message(
    chat_id=dest_chat_id,
    from_chat_id=source_chat_id,
    message_id=msg.message_id
)
```

---

## Tratamento De Erros

```python
from telegram.error import TelegramError, BadRequest, TimedOut, NetworkError

async def safe_send(bot, chat_id, text, **kwargs):
    """Envio com retry e tratamento de erros."""
    max_retries = 3
    for attempt in range(max_retries):
        try:
            return await bot.send_message(chat_id, text, **kwargs)
        except TimedOut:
            if attempt < max_retries - 1:
                await asyncio.sleep(2 ** attempt)
                continue
            raise
        except BadRequest as e:
            if "chat not found" in str(e).lower():
                print(f"Chat {chat_id} nao encontrado")
                return None
            raise
        except NetworkError:
            if attempt < max_retries - 1:
                await asyncio.sleep(2 ** attempt)
                continue
            raise
```

---

## Rate Limits

- **Mensagens em chat privado:** ~30 msg/segundo
- **Mensagens em grupo:** ~20 msg/minuto por grupo
- **Broadcast geral:** ~30 msg/segundo no total
- **Bulk notifications:** use `asyncio.sleep(0.05)` entre envios para evitar flood

Se receber erro 429 (Too Many Requests), respeite o `retry_after` retornado.

---

## Referencia De Arquivos

| Topico | Arquivo |
|--------|---------|
| Setup de webhooks | `references/webhook-setup.md` |
| Gerenciamento de chats | `references/chat-management.md` |
| Recursos avancados | `references/advanced-features.md` |
| Referencia completa da API | `references/api-reference.md` |
| Boilerplate Node.js | `assets/boilerplate/nodejs/` |
| Boilerplate Python | `assets/boilerplate/python/` |
| Exemplos de payloads | `assets/examples/` |

## Best Practices

- Provide clear, specific context about your project and requirements
- Review all suggestions before applying them to production code
- Combine with other complementary skills for comprehensive analysis

## Common Pitfalls

- Using this skill for tasks outside its domain expertise
- Applying recommendations without understanding your specific context
- Not providing enough project context for accurate analysis

## Related Skills

- `instagram` - Complementary skill for enhanced analysis
- `social-orchestrator` - Complementary skill for enhanced analysis
- `whatsapp-cloud-api` - Complementary skill for enhanced analysis

---

## Imported Reference

---
name: telegram-mini-app
description: "Expert in building Telegram Mini Apps (TWA) - web apps that run inside Telegram with native-like experience. Covers the TON ecosystem, Telegram Web App API, payments, user authentication, and build..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Telegram Mini App

**Role**: Telegram Mini App Architect

You build apps where 800M+ Telegram users already are. You understand
the Mini App ecosystem is exploding - games, DeFi, utilities, social
apps. You know TON blockchain and how to monetize with crypto. You
design for the Telegram UX paradigm, not traditional web.

## Capabilities

- Telegram Web App API
- Mini App architecture
- TON Connect integration
- In-app payments
- User authentication via Telegram
- Mini App UX patterns
- Viral Mini App mechanics
- TON blockchain integration

## Patterns

### Mini App Setup

Getting started with Telegram Mini Apps

**When to use**: When starting a new Mini App

```javascript
## Mini App Setup

### Basic Structure
```html
<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
</head>
<body>
  <script>
    const tg = window.Telegram.WebApp;
    tg.ready();
    tg.expand();

    // User data
    const user = tg.initDataUnsafe.user;
    console.log(user.first_name, user.id);
  </script>
</body>
</html>
```

### React Setup
```jsx
// hooks/useTelegram.js
export function useTelegram() {
  const tg = window.Telegram?.WebApp;

  return {
    tg,
    user: tg?.initDataUnsafe?.user,
    queryId: tg?.initDataUnsafe?.query_id,
    expand: () => tg?.expand(),
    close: () => tg?.close(),
    ready: () => tg?.ready(),
  };
}

// App.jsx
function App() {
  const { tg, user, expand, ready } = useTelegram();

  useEffect(() => {
    ready();
    expand();
  }, []);

  return <div>Hello, {user?.first_name}</div>;
}
```

### Bot Integration
```javascript
// Bot sends Mini App
bot.command('app', (ctx) => {
  ctx.reply('Open the app:', {
    reply_markup: {
      inline_keyboard: [[
        { text: '🚀 Open App', web_app: { url: 'https://your-app.com' } }
      ]]
    }
  });
});
```
```

### TON Connect Integration

Wallet connection for TON blockchain

**When to use**: When building Web3 Mini Apps

```python
## TON Connect Integration

### Setup
```bash
npm install @tonconnect/ui-react
```

### React Integration
```jsx
import { TonConnectUIProvider, TonConnectButton } from '@tonconnect/ui-react';

// Wrap app
function App() {
  return (
    <TonConnectUIProvider manifestUrl="https://your-app.com/tonconnect-manifest.json">
      <MainApp />
    </TonConnectUIProvider>
  );
}

// Use in components
function WalletSection() {
  return (
    <TonConnectButton />
  );
}
```

### Manifest File
```json
{
  "url": "https://your-app.com",
  "name": "Your Mini App",
  "iconUrl": "https://your-app.com/icon.png"
}
```

### Send TON Transaction
```jsx
import { useTonConnectUI } from '@tonconnect/ui-react';

function PaymentButton({ amount, to }) {
  const [tonConnectUI] = useTonConnectUI();

  const handlePay = async () => {
    const transaction = {
      validUntil: Math.floor(Date.now() / 1000) + 60,
      messages: [{
        address: to,
        amount: (amount * 1e9).toString(), // TON to nanoton
      }]
    };

    await tonConnectUI.sendTransaction(transaction);
  };

  return <button onClick={handlePay}>Pay {amount} TON</button>;
}
```
```

### Mini App Monetization

Making money from Mini Apps

**When to use**: When planning Mini App revenue

```javascript
## Mini App Monetization

### Revenue Streams
| Model | Example | Potential |
|-------|---------|-----------|
| TON payments | Premium features | High |
| In-app purchases | Virtual goods | High |
| Ads (Telegram Ads) | Display ads | Medium |
| Referral | Share to earn | Medium |
| NFT sales | Digital collectibles | High |

### Telegram Stars (New!)
```javascript
// In your bot
bot.command('premium', (ctx) => {
  ctx.replyWithInvoice({
    title: 'Premium Access',
    description: 'Unlock all features',
    payload: 'premium',
    provider_token: '', // Empty for Stars
    currency: 'XTR', // Telegram Stars
    prices: [{ label: 'Premium', amount: 100 }], // 100 Stars
  });
});
```

### Viral Mechanics
```jsx
// Referral system
function ReferralShare() {
  const { tg, user } = useTelegram();
  const referralLink = `https://t.me/your_bot?start=ref_${user.id}`;

  const share = () => {
    tg.openTelegramLink(
      `https://t.me/share/url?url=${encodeURIComponent(referralLink)}&text=Check this out!`
    );
  };

  return <button onClick={share}>Invite Friends (+10 coins)</button>;
}
```

### Gamification for Retention
- Daily rewards
- Streak bonuses
- Leaderboards
- Achievement badges
- Referral bonuses
```

## Anti-Patterns

### ❌ Ignoring Telegram Theme

**Why bad**: Feels foreign in Telegram.
Bad user experience.
Jarring transitions.
Users don't trust it.

**Instead**: Use tg.themeParams.
Match Telegram colors.
Use native-feeling UI.
Test in both light/dark.

### ❌ Desktop-First Mini App

**Why bad**: 95% of Telegram is mobile.
Touch targets too small.
Doesn't fit in Telegram UI.
Scrolling issues.

**Instead**: Mobile-first always.
Test on real phones.
Touch-friendly buttons.
Fit within Telegram frame.

### ❌ No Loading States

**Why bad**: Users think it's broken.
Poor perceived performance.
High exit rate.
Confusion.

**Instead**: Show skeleton UI.
Loading indicators.
Progressive loading.
Optimistic updates.

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Not validating initData from Telegram | high | ## Validating initData |
| TON Connect not working on mobile | high | ## TON Connect Mobile Issues |
| Mini App feels slow and janky | medium | ## Mini App Performance |
| Custom buttons instead of MainButton | medium | ## Using MainButton Properly |

## Related Skills

Works well with: `telegram-bot-builder`, `frontend`, `blockchain-defi`, `viral-generator-builder`

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: twilio-communications
description: "Build communication features with Twilio: SMS messaging, voice calls, WhatsApp Business API, and user verification (2FA). Covers the full spectrum from simple notifications to complex IVR systems a..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Twilio Communications

## Patterns

### SMS Sending Pattern

Basic pattern for sending SMS messages with Twilio.
Handles the fundamentals: phone number formatting, message delivery,
and delivery status callbacks.

Key considerations:
- Phone numbers must be in E.164 format (+1234567890)
- Default rate limit: 80 messages per second (MPS)
- Messages over 160 characters are split (and cost more)
- Carrier filtering can block messages (especially to US numbers)


**When to use**: ['Sending notifications to users', 'Transactional messages (order confirmations, shipping)', 'Alerts and reminders']

```python
from twilio.rest import Client
from twilio.base.exceptions import TwilioRestException
import os
import re

class TwilioSMS:
    """
    SMS sending with proper error handling and validation.
    """

    def __init__(self):
        self.client = Client(
            os.environ["TWILIO_ACCOUNT_SID"],
            os.environ["TWILIO_AUTH_TOKEN"]
        )
        self.from_number = os.environ["TWILIO_PHONE_NUMBER"]

    def validate_e164(self, phone: str) -> bool:
        """Validate phone number is in E.164 format."""
        pattern = r'^\+[1-9]\d{1,14}$'
        return bool(re.match(pattern, phone))

    def send_sms(
        self,
        to: str,
        body: str,
        status_callback: str = None
    ) -> dict:
        """
        Send an SMS message.

        Args:
            to: Recipient phone number in E.164 format
            body: Message text (160 chars = 1 segment)
            status_callback: URL for delivery status webhooks

        Returns:
            Message SID and status
        """
        # Validate phone number format
        if not self.validate_e164(to):
            return {
                "success": False,
                "error": "Phone number must be in E.164 format (+1234567890)"
            }

        # Check message length (warn about segmentation)
        segment_count = (len(body) + 159) // 160
        if segment_count > 1:
            print(f"Warning: Message will be sent as {segment_count} segments")

        try:
            message = self.client.messages.create(
                to=to,
                from_=self.from_number,
                body=body,
                status_callback=status_callback
            )

            return {
                "success": True,
                "message_sid": message.sid,
                "status": message.status,
                "segments": segment_count
            }

        except TwilioRestException as e:
            return self._handle_error(e)

    def _handle_error(self, error: Twilio
```

### Twilio Verify Pattern (2FA/OTP)

Use Twilio Verify for phone number verification and 2FA.
Handles code generation, delivery, rate limiting, and fraud prevention.

Key benefits over DIY OTP:
- Twilio manages code generation and expiration
- Built-in fraud prevention (saved customers $82M+ blocking 747M attempts)
- Handles rate limiting automatically
- Multi-channel: SMS, Voice, Email, Push, WhatsApp

Google found SMS 2FA blocks "100% of automated bots, 96% of bulk
phishing attacks, and 76% of targeted attacks."


**When to use**: ['User phone number verification at signup', 'Two-factor authentication (2FA)', 'Password reset verification', 'High-value transaction confirmation']

```python
from twilio.rest import Client
from twilio.base.exceptions import TwilioRestException
import os
from enum import Enum
from typing import Optional

class VerifyChannel(Enum):
    SMS = "sms"
    CALL = "call"
    EMAIL = "email"
    WHATSAPP = "whatsapp"

class TwilioVerify:
    """
    Phone verification with Twilio Verify.
    Never store OTP codes - Twilio handles it.
    """

    def __init__(self, verify_service_sid: str = None):
        self.client = Client(
            os.environ["TWILIO_ACCOUNT_SID"],
            os.environ["TWILIO_AUTH_TOKEN"]
        )
        # Create a Verify Service in Twilio Console first
        self.service_sid = verify_service_sid or os.environ["TWILIO_VERIFY_SID"]

    def send_verification(
        self,
        to: str,
        channel: VerifyChannel = VerifyChannel.SMS,
        locale: str = "en"
    ) -> dict:
        """
        Send verification code to phone/email.

        Args:
            to: Phone number (E.164) or email
            channel: SMS, call, email, or whatsapp
            locale: Language code for message

        Returns:
            Verification status
        """
        try:
            verification = self.client.verify \
                .v2 \
                .services(self.service_sid) \
                .verifications \
                .create(
                    to=to,
                    channel=channel.value,
                    locale=locale
                )

            return {
                "success": True,
                "status": verification.status,  # "pending"
                "channel": channel.value,
                "valid": verification.valid
            }

        except TwilioRestException as e:
            return self._handle_verify_error(e)

    def check_verification(self, to: str, code: str) -> dict:
        """
        Check if verification code is correct.

        Args:
            to: Phone number or email that received code
            code: The code entered by user

        R
```

### TwiML IVR Pattern

Build Interactive Voice Response (IVR) systems using TwiML.
TwiML (Twilio Markup Language) is XML that tells Twilio what to do
when receiving calls.

Core TwiML verbs:
- <Say>: Text-to-speech
- <Play>: Play audio file
- <Gather>: Collect keypad/speech input
- <Dial>: Connect to another number
- <Record>: Record caller's voice
- <Redirect>: Move to another TwiML endpoint

Key insight: Twilio makes HTTP request to your webhook, you return
TwiML, Twilio executes it. Stateless, so use URL params or sessions.


**When to use**: ['Phone menu systems (press 1 for sales...)', 'Automated customer support', 'Appointment reminders with confirmation', 'Voicemail systems']

```python
from flask import Flask, request, Response
from twilio.twiml.voice_response import VoiceResponse, Gather
from twilio.request_validator import RequestValidator
import os

app = Flask(__name__)

def validate_twilio_request(f):
    """Decorator to validate requests are from Twilio."""
    def wrapper(*args, **kwargs):
        validator = RequestValidator(os.environ["TWILIO_AUTH_TOKEN"])

        # Get request details
        url = request.url
        params = request.form.to_dict()
        signature = request.headers.get("X-Twilio-Signature", "")

        if not validator.validate(url, params, signature):
            return "Invalid request", 403

        return f(*args, **kwargs)
    wrapper.__name__ = f.__name__
    return wrapper

@app.route("/voice/incoming", methods=["POST"])
@validate_twilio_request
def incoming_call():
    """Handle incoming call with IVR menu."""
    response = VoiceResponse()

    # Gather digits with timeout
    gather = Gather(
        num_digits=1,
        action="/voice/menu-selection",
        method="POST",
        timeout=5
    )
    gather.say(
        "Welcome to Acme Corp. "
        "Press 1 for sales. "
        "Press 2 for support. "
        "Press 3 to leave a message."
    )
    response.append(gather)

    # If no input, repeat
    response.redirect("/voice/incoming")

    return Response(str(response), mimetype="text/xml")

@app.route("/voice/menu-selection", methods=["POST"])
@validate_twilio_request
def menu_selection():
    """Route based on menu selection."""
    response = VoiceResponse()
    digit = request.form.get("Digits", "")

    if digit == "1":
        # Transfer to sales
        response.say("Connecting you to sales.")
        response.dial(os.environ["SALES_PHONE"])

    elif digit == "2":
        # Transfer to support
        response.say("Connecting you to support.")
        response.dial(os.environ["SUPPORT_PHONE"])

    elif digit == "3":
        # Voicemail
        response.say("Please leave a message after 
```

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | ## Track opt-out status in your database |
| Issue | medium | ## Implement retry logic for transient failures |
| Issue | high | ## Register for A2P 10DLC (US requirement) |
| Issue | critical | ## ALWAYS validate the signature |
| Issue | high | ## Track session windows per user |
| Issue | critical | ## Never hardcode credentials |
| Issue | medium | ## Implement application-level rate limiting too |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Amazon Alexa
---
name: amazon-alexa
description: Integracao completa com Amazon Alexa para criar skills de voz inteligentes, transformar Alexa em assistente com AI assistant como cerebro (projeto Auri) e integrar com AWS ecosystem (Lambda, DynamoDB,...
risk: safe
source: community
date_added: '2026-03-06'
author: renat
tags:
- voice
- alexa
- aws
- smart-home
- iot
tools:
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
---

# AMAZON ALEXA — Voz Inteligente com AI assistant

## Overview

Integracao completa com Amazon Alexa para criar skills de voz inteligentes, transformar Alexa em assistente com AI assistant como cerebro (projeto Auri) e integrar com AWS ecosystem (Lambda, DynamoDB, Polly, Transcribe, Lex, Smart Home).

## When to Use This Skill

- When you need specialized assistance with this domain

## Do Not Use This Skill When

- The task is unrelated to amazon alexa
- A simpler, more specific tool can handle the request
- The user needs general-purpose assistance without domain expertise

## How It Works

> Voce e o especialista em Alexa e AWS Voice. Missao: transformar
> qualquer dispositivo Alexa em assistente ultra-inteligente usando
> AI assistant como LLM backend, com voz neural, memoria persistente e
> controle de Smart Home. Projeto-chave: AURI.

---

## 1. Visao Geral Do Ecossistema

```
[Alexa Device] → [Alexa Cloud] → [AWS Lambda] → [LLM API]
    Fala          Transcricao      Logica          Inteligencia
      ↑               ↑               ↑                ↑
   Usuario         Intent        Handler          Anthropic
                               + DynamoDB
                               + Polly TTS
                               + APL Visual
```

## Componentes Da Arquitetura Auri

| Componente | Servico AWS | Funcao |
|-----------|-------------|--------|
| Voz → Texto | Alexa ASR nativo | Reconhecimento de fala |
| NLU | ASK Interaction Model + Lex V2 | Extrair intent e slots |
| Backend | AWS Lambda (Python/Node.js) | Logica e orquestracao |
| LLM | LLM API (Anthropic) | Inteligencia e respostas |
| Persistencia | Amazon DynamoDB | Historico e preferencias |
| Texto → Voz | Amazon Polly (neural) | Fala natural da Auri |
| Interface Visual | APL (Alexa Presentation Language) | Telas em Echo Show |
| Smart Home | Alexa Smart Home API | Controle de dispositivos |
| Automacao | Alexa Routines API | Rotinas inteligentes |

---

## 2.1 Pre-Requisitos

```bash

## Ask Cli

npm install -g ask-cli
ask configure

## Aws Cli

pip install awscli
aws configure
```

## Criar Skill Com Template

ask new \
  --template hello-world \
  --skill-name auri \
  --language pt-BR

## └── .Ask/Ask-Resources.Json

```

## 2.3 Configurar Invocation Name

No arquivo `models/pt-BR.json`:
```json
{
  "interactionModel": {
    "languageModel": {
      "invocationName": "auri"
    }
  }
}
```

---

## 3.1 Intents Essenciais Para Auri

```json
{
  "interactionModel": {
    "languageModel": {
      "invocationName": "auri",
      "intents": [
        {"name": "AMAZON.HelpIntent"},
        {"name": "AMAZON.StopIntent"},
        {"name": "AMAZON.CancelIntent"},
        {"name": "AMAZON.FallbackIntent"},
        {
          "name": "ChatIntent",
          "slots": [{"name": "query", "type": "AMAZON.SearchQuery"}],
          "samples": [
            "{query}",
            "me ajuda com {query}",
            "quero saber sobre {query}",
            "o que voce sabe sobre {query}",
            "explique {query}",
            "pesquise {query}"
          ]
        },
        {
          "name": "SmartHomeIntent",
          "slots": [
            {"name": "device", "type": "AMAZON.Room"},
            {"name": "action", "type": "ActionType"}
          ],
          "samples": [
            "{action} a {device}",
            "controla {device}",
            "acende {device}",
            "apaga {device}"
          ]
        },
        {
          "name": "RoutineIntent",
          "slots": [{"name": "routine", "type": "RoutineType"}],
          "samples": [
            "ativa rotina {routine}",
            "executa {routine}",
            "modo {routine}"
          ]
        }
      ],
      "types": [
        {
          "name": "ActionType",
          "values": [
            {"name": {"value": "liga", "synonyms": ["acende", "ativa", "liga"]}},
            {"name": {"value": "desliga", "synonyms": ["apaga", "desativa", "desliga"]}}
          ]
        },
        {
          "name": "RoutineType",
          "values": [
            {"name": {"value": "bom dia", "synonyms": ["acordar", "manhã"]}},
            {"name": {"value": "boa noite", "synonyms": ["dormir", "descansar"]}},
            {"name": {"value": "trabalho", "synonyms": ["trabalhar", "foco"]}},
            {"name": {"value": "sair", "synonyms": ["saindo", "goodbye"]}}
          ]
        }
      ]
    }
  }
}
```

---

## 4.1 Handler Principal Python

```python
import os
import time
import anthropic
import boto3
from ask_sdk_core.skill_builder import SkillBuilder
from ask_sdk_core.handler_input import HandlerInput
from ask_sdk_core.utils import is_intent_name, is_request_type
from ask_sdk_model import Response
from ask_sdk_dynamodb_persistence_adapter import DynamoDbPersistenceAdapter

## ============================================================

@sb.request_handler(can_handle_func=is_request_type("LaunchRequest"))
def launch_handler(handler_input: HandlerInput) -> Response:
    attrs = handler_input.attributes_manager.persistent_attributes
    name = attrs.get("name", "")
    greeting = f"Oi{', ' + name if name else ''}! Eu sou a Auri. Como posso ajudar?"
    return (handler_input.response_builder
            .speak(greeting).ask("Em que posso ajudar?").response)


@sb.request_handler(can_handle_func=is_intent_name("ChatIntent"))
def chat_handler(handler_input: HandlerInput) -> Response:
    try:
        # Obter query
        slots = handler_input.request_envelope.request.intent.slots
        query = slots["query"].value if slots.get("query") else None
        if not query:
            return (handler_input.response_builder
                    .speak("Pode repetir? Nao entendi bem.").ask("Pode repetir?").response)

        # Carregar historico
        attrs = handler_input.attributes_manager.persistent_attributes
        history = attrs.get("history", [])

        # Montar mensagens para AI assistant
        messages = history[-MAX_HISTORY:]
        messages.append({"role": "user", "content": query})

        # Chamar AI assistant
        client = anthropic.Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
        response = client.messages.create(
            model=MODEL_NAME,
            max_tokens=512,
            system=AURI_SYSTEM_PROMPT,
            messages=messages
        )
        reply = response.content[0].text

        # Truncar para nao exceder timeout
        if len(reply) > MAX_RESPONSE_CHARS:
            reply = reply[:MAX_RESPONSE_CHARS] + "... Quer que eu continue?"

        # Salvar historico
        history.append({"role": "user", "content": query})
        history.append({"role": "assistant", "content": reply})
        attrs["history"] = history[-50:]  # Manter ultimas 50
        handler_input.attributes_manager.persistent_attributes = attrs
        handler_input.attributes_manager.save_persist

## 4.2 Variaveis De Ambiente Lambda

```
ANTHROPIC_API_KEY=sk-...  (armazenar em Secrets Manager)
DYNAMODB_TABLE=auri-users
AWS_REGION=us-east-1
```

## 4.3 Requirements.Txt

```
ask-sdk-core>=1.19.0
ask-sdk-dynamodb-persistence-adapter>=1.19.0
anthropic>=0.40.0
boto3>=1.34.0
```

---

## 5.1 Criar Tabela

```bash
aws dynamodb create-table \
  --table-name auri-users \
  --attribute-definitions AttributeName=userId,AttributeType=S \
  --key-schema AttributeName=userId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region us-east-1
```

## 5.2 Schema Do Usuario

```json
{
  "userId": "amzn1.ask.account.XXXXX",
  "name": "Joao",
  "history": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ],
  "preferences": {
    "language": "pt-BR",
    "voice": "Vitoria",
    "personality": "assistente profissional"
  },
  "smartHome": {
    "devices": {},
    "routines": {}
  },
  "updatedAt": 1740960000,
  "ttl": 1748736000
}
```

## 5.3 Ttl Automatico (Expirar Dados Antigos)

```python
import time

## Adicionar Ttl De 180 Dias Ao Salvar

attrs["ttl"] = int(time.time()) + (180 * 24 * 3600)
```

---

## 6.1 Vozes Disponiveis (Portugues)

| Voice | Idioma | Tipo | Recomendado |
|-------|--------|------|-------------|
| `Vitoria` | pt-BR | Neural | ✅ Auri PT-BR |
| `Camila` | pt-BR | Neural | Alternativa |
| `Ricardo` | pt-BR | Standard | Masculino |
| `Ines` | pt-PT | Neural | Portugal |

## 6.2 Integrar Polly Na Resposta

```python
import boto3
import base64

def synthesize_polly(text: str, voice_id: str = "Vitoria") -> str:
    """Retorna URL de audio Polly para usar em Alexa."""
    client = boto3.client("polly", region_name="us-east-1")
    response = client.synthesize_speech(
        Text=text,
        OutputFormat="mp3",
        VoiceId=voice_id,
        Engine="neural"
    )
    # Salvar em S3 e retornar URL
    # (necessario para usar audio customizado no Alexa)
    return upload_to_s3(response["AudioStream"].read())

def speak_with_polly(handler_input, text, voice_id="Vitoria"):
    """Retornar resposta usando voz Polly customizada via SSML."""
    audio_url = synthesize_polly(text, voice_id)
    ssml = f'<speak><audio src="{audio_url}"/></speak>'
    return handler_input.response_builder.speak(ssml)
```

## 6.3 Ssml Para Controle De Voz

```xml
<speak>
  <prosody rate="90%" pitch="+5%">
    Oi! Eu sou a Auri.
  </prosody>
  <break time="0.5s"/>
  <emphasis level="moderate">Como posso ajudar?</emphasis>
</speak>
```

---

## 7.1 Template De Chat

```json
{
  "type": "APL",
  "version": "2023.3",
  "theme": "dark",
  "mainTemplate": {
    "parameters": ["payload"],
    "items": [{
      "type": "Container",
      "width": "100%",
      "height": "100%",
      "backgroundColor": "#1a1a2e",
      "items": [
        {
          "type": "Text",
          "text": "AURI",
          "fontSize": "32px",
          "color": "#e94560",
          "textAlign": "center",
          "paddingTop": "20px"
        },
        {
          "type": "Text",
          "text": "${payload.lastResponse}",
          "fontSize": "24px",
          "color": "#ffffff",
          "padding": "20px",
          "maxLines": 8,
          "grow": 1
        },
        {
          "type": "Text",
          "text": "Diga algo para continuar...",
          "fontSize": "18px",
          "color": "#888888",
          "textAlign": "center",
          "paddingBottom": "20px"
        }
      ]
    }]
  }
}
```

## 7.2 Adicionar Apl Na Resposta

```python
@sb.request_handler(can_handle_func=is_intent_name("ChatIntent"))
def chat_with_apl(handler_input: HandlerInput) -> Response:
    # ... obter reply do AI assistant ...

    # Verificar se device suporta APL
    supported = handler_input.request_envelope.context.system.device.supported_interfaces
    has_apl = getattr(supported, "alexa_presentation_apl", None) is not None

    if has_apl:
        apl_directive = {
            "type": "Alexa.Presentation.APL.RenderDocument",
            "token": "auri-chat",
            "document": CHAT_APL_DOCUMENT,
            "datasources": {"payload": {"lastResponse": reply}}
        }
        handler_input.response_builder.add_directive(apl_directive)

    return handler_input.response_builder.speak(reply).ask("Mais alguma coisa?").response
```

---

## 8.1 Ativar Smart Home Skill

No `skill.json`, adicionar:
```json
{
  "apis": {
    "smartHome": {
      "endpoint": {
        "uri": "arn:aws:lambda:us-east-1:123456789:function:auri-smart-home"
      }
    }
  }
}
```

## 8.2 Handler De Smart Home

```python
def handle_smart_home_directive(event, context):
    namespace = event["directive"]["header"]["namespace"]
    name = event["directive"]["header"]["name"]
    endpoint_id = event["directive"]["endpoint"]["endpointId"]

    if namespace == "Alexa.PowerController":
        state = "ON" if name == "TurnOn" else "OFF"
        # Chamar sua API de smart home
        control_device(endpoint_id, {"power": state})
        return build_smart_home_response(endpoint_id, "powerState", state)

    elif namespace == "Alexa.BrightnessController":
        brightness = event["directive"]["payload"]["brightness"]
        control_device(endpoint_id, {"brightness": brightness})
        return build_smart_home_response(endpoint_id, "brightness", brightness)
```

## 8.3 Discovery De Dispositivos

```python
def handle_discovery(event, context):
    return {
        "event": {
            "header": {
                "namespace": "Alexa.Discovery",
                "name": "Discover.Response",
                "payloadVersion": "3"
            },
            "payload": {
                "endpoints": [
                    {
                        "endpointId": "light-sala-001",
                        "friendlyName": "Luz da Sala",
                        "displayCategories": ["LIGHT"],
                        "capabilities": [
                            {
                                "type": "AlexaInterface",
                                "interface": "Alexa.PowerController",
                                "version": "3"
                            },
                            {
                                "type": "AlexaInterface",
                                "interface": "Alexa.BrightnessController",
                                "version": "3"
                            }
                        ]
                    }
                ]
            }
        }
    }
```

---

## Deploy Completo (Skill + Lambda)

cd auri/
ask deploy

## Verificar Status

ask status

## Testar No Simulador

ask dialog --locale pt-BR

## Teste Especifico De Intent

ask simulate \
  --text "abrir auri" \
  --locale pt-BR \
  --skill-id amzn1.ask.skill.YOUR-SKILL-ID
```

## Criar Lambda Manualmente

aws lambda create-function \
  --function-name auri-skill \
  --runtime python3.11 \
  --role arn:aws:iam::ACCOUNT:role/auri-lambda-role \
  --handler lambda_function.handler \
  --timeout 8 \
  --memory-size 512 \
  --zip-file fileb://function.zip

## Adicionar Trigger Alexa

aws lambda add-permission \
  --function-name auri-skill \
  --statement-id alexa-skill-trigger \
  --action lambda:InvokeFunction \
  --principal alexa-appkit.amazon.com \
  --event-source-token amzn1.ask.skill.YOUR-SKILL-ID
```

## Usar Secrets Manager

aws secretsmanager create-secret \
  --name auri/anthropic-key \
  --secret-string '{"ANTHROPIC_API_KEY": "sk-..."}'

## Lambda Acessa Via Sdk:

import boto3, json
def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response['SecretString'])
```

---

## Fase 1 — Setup (Dia 1)

```
[ ] Conta Amazon Developer criada
[ ] Conta AWS configurada (free tier)
[ ] ASK CLI instalado e configurado
[ ] IAM Role criada com permissoes: Lambda, DynamoDB, Polly, Logs
[ ] Anthropic API key armazenada em Secrets Manager
```

## Fase 2 — Skill Base (Dia 2-3)

```
[ ] ask new --template hello-world --skill-name auri
[ ] Interaction model definido (pt-BR.json)
[ ] LaunchRequest handler funcionando
[ ] ChatIntent handler com AI assistant integrado
[ ] ask deploy funcionando
[ ] Teste basico no ASK simulator
```

## Fase 3 — Persistencia (Dia 4)

```
[ ] DynamoDB table criada
[ ] Persistencia de historico funcionando
[ ] TTL configurado
[ ] Preferencias do usuario salvas
```

## Fase 4 — Polly + Apl (Dia 5-6)

```
[ ] Polly integrado com voz Vitoria (neural)
[ ] APL template de chat criado
[ ] APL renderizando em Echo Show simulator
```

## Fase 5 — Smart Home (Opcional)

```
[ ] Smart Home skill habilitada
[ ] Discovery de dispositivos funcionando
[ ] PowerController implementado
[ ] Teste com device real
```

## Fase 6 — Publicacao

```
[ ] Teste completo de todas funcionalidades
[ ] Performance OK (< 8s timeout)
[ ] Certificacao Amazon submetida
[ ] Publicado na Alexa Skills Store
```

---

## 11. Comandos Rapidos

| Acao | Comando |
|------|---------|
| Criar skill | `ask new --template hello-world` |
| Deploy | `ask deploy` |
| Simular | `ask simulate --text "abre a auri"` |
| Dialog interativo | `ask dialog --locale pt-BR` |
| Ver logs | `ask smapi get-skill-simulation` |
| Validar modelo | `ask validate --locales pt-BR` |
| Exportar skill | `ask smapi export-package --skill-id ID` |
| Listar skills | `ask list skills` |

---

## 12. Referencias

- Boilerplate Python completo: `assets/boilerplate/lambda_function.py`
- Interaction model PT-BR: `assets/interaction-models/pt-BR.json`
- APL chat template: `assets/apl-templates/chat-interface.json`
- Smart Home examples: `references/smart-home-api.md`
- ASK SDK Python docs: https://github.com/alexa/alexa-skills-kit-sdk-for-python
- AI assistant + Alexa guide: vendor AI assistant integration guide

## Best Practices

- Provide clear, specific context about your project and requirements
- Review all suggestions before applying them to production code
- Combine with other complementary skills for comprehensive analysis

## Common Pitfalls

- Using this skill for tasks outside its domain expertise
- Applying recommendations without understanding your specific context
- Not providing enough project context for accurate analysis

## Imported Module: Automate Whatsapp
---
name: automate-whatsapp
description: Automate Whatsapp
---

404: Not Found

## Imported Module: Integrate Whatsapp
---
name: integrate-whatsapp
description: Integrate Whatsapp
---

404: Not Found

## Imported Module: Observe Whatsapp
---
name: observe-whatsapp
description: Observe Whatsapp
---

404: Not Found

## Imported Module: Slack Bot Builder
---
name: slack-bot-builder
description: "Build Slack apps using the Bolt framework across Python, JavaScript, and Java. Covers Block Kit for rich UIs, interactive components, slash commands, event handling, OAuth installation flows, and W..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Slack Bot Builder

## Patterns

### Bolt App Foundation Pattern

The Bolt framework is Slack's recommended approach for building apps.
It handles authentication, event routing, request verification, and
HTTP request processing so you can focus on app logic.

Key benefits:
- Event handling in a few lines of code
- Security checks and payload validation built-in
- Organized, consistent patterns
- Works for experiments and production

Available in: Python, JavaScript (Node.js), Java


**When to use**: ['Starting any new Slack app', 'Migrating from legacy Slack APIs', 'Building production Slack integrations']

```python
# Python Bolt App
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler
import os

# Initialize with tokens from environment
app = App(
    token=os.environ["SLACK_BOT_TOKEN"],
    signing_secret=os.environ["SLACK_SIGNING_SECRET"]
)

# Handle messages containing "hello"
@app.message("hello")
def handle_hello(message, say):
    """Respond to messages containing 'hello'."""
    user = message["user"]
    say(f"Hey there <@{user}>!")

# Handle slash command
@app.command("/ticket")
def handle_ticket_command(ack, body, client):
    """Handle /ticket slash command."""
    # Acknowledge immediately (within 3 seconds)
    ack()

    # Open a modal for ticket creation
    client.views_open(
        trigger_id=body["trigger_id"],
        view={
            "type": "modal",
            "callback_id": "ticket_modal",
            "title": {"type": "plain_text", "text": "Create Ticket"},
            "submit": {"type": "plain_text", "text": "Submit"},
            "blocks": [
                {
                    "type": "input",
                    "block_id": "title_block",
                    "element": {
                        "type": "plain_text_input",
                        "action_id": "title_input"
                    },
                    "label": {"type": "plain_text", "text": "Title"}
                },
                {
                    "type": "input",
                    "block_id": "desc_block",
                    "element": {
                        "type": "plain_text_input",
                        "multiline": True,
                        "action_id": "desc_input"
                    },
                    "label": {"type": "plain_text", "text": "Description"}
                },
                {
                    "type": "input",
                    "block_id": "priority_block",
                    "element": {
                        "type": "static_select",
                        "action_id": "priority_select",
   
```

### Block Kit UI Pattern

Block Kit is Slack's UI framework for building rich, interactive messages.
Compose messages using blocks (sections, actions, inputs) and elements
(buttons, menus, text inputs).

Limits:
- Up to 50 blocks per message
- Up to 100 blocks in modals/Home tabs
- Block text limited to 3000 characters

Use Block Kit Builder to prototype: https://app.slack.com/block-kit-builder


**When to use**: ['Building rich message layouts', 'Adding interactive components to messages', 'Creating forms in modals', 'Building Home tab experiences']

```python
from slack_bolt import App
import os

app = App(token=os.environ["SLACK_BOT_TOKEN"])

def build_notification_blocks(incident: dict) -> list:
    """Build Block Kit blocks for incident notification."""
    severity_emoji = {
        "critical": ":red_circle:",
        "high": ":large_orange_circle:",
        "medium": ":large_yellow_circle:",
        "low": ":white_circle:"
    }

    return [
        # Header
        {
            "type": "header",
            "text": {
                "type": "plain_text",
                "text": f"{severity_emoji.get(incident['severity'], '')} Incident Alert"
            }
        },
        # Details section
        {
            "type": "section",
            "fields": [
                {
                    "type": "mrkdwn",
                    "text": f"*Incident:*\n{incident['title']}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Severity:*\n{incident['severity'].upper()}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Service:*\n{incident['service']}"
                },
                {
                    "type": "mrkdwn",
                    "text": f"*Reported:*\n<!date^{incident['timestamp']}^{date_short} {time}|{incident['timestamp']}>"
                }
            ]
        },
        # Description
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": f"*Description:*\n{incident['description'][:2000]}"
            }
        },
        # Divider
        {"type": "divider"},
        # Action buttons
        {
            "type": "actions",
            "block_id": f"incident_actions_{incident['id']}",
            "elements": [
                {
                    "type": "button",
                    "text": {"type": "plain_text", "text": "Acknowledge"},
                    "style": "primary",
                    "action_id": "acknowle
```

### OAuth Installation Pattern

Enable users to install your app in their workspaces via OAuth 2.0.
Bolt handles most of the OAuth flow, but you need to configure it
and store tokens securely.

Key OAuth concepts:
- Scopes define permissions (request minimum needed)
- Tokens are workspace-specific
- Installation data must be stored persistently
- Users can add scopes later (additive)

70% of users abandon installation when confronted with excessive
permission requests - request only what you need!


**When to use**: ['Distributing app to multiple workspaces', 'Building public Slack apps', 'Enterprise-grade integrations']

```python
from slack_bolt import App
from slack_bolt.oauth.oauth_settings import OAuthSettings
from slack_sdk.oauth.installation_store import FileInstallationStore
from slack_sdk.oauth.state_store import FileOAuthStateStore
import os

# For production, use database-backed stores
# For example: PostgreSQL, MongoDB, Redis

class DatabaseInstallationStore:
    """Store installation data in your database."""

    async def save(self, installation):
        """Save installation when user completes OAuth."""
        await db.installations.upsert({
            "team_id": installation.team_id,
            "enterprise_id": installation.enterprise_id,
            "bot_token": encrypt(installation.bot_token),
            "bot_user_id": installation.bot_user_id,
            "bot_scopes": installation.bot_scopes,
            "user_id": installation.user_id,
            "installed_at": installation.installed_at
        })

    async def find_installation(self, *, enterprise_id, team_id, user_id=None, is_enterprise_install=False):
        """Find installation for a workspace."""
        record = await db.installations.find_one({
            "team_id": team_id,
            "enterprise_id": enterprise_id
        })

        if record:
            return Installation(
                bot_token=decrypt(record["bot_token"]),
                # ... other fields
            )
        return None

# Initialize OAuth-enabled app
app = App(
    signing_secret=os.environ["SLACK_SIGNING_SECRET"],
    oauth_settings=OAuthSettings(
        client_id=os.environ["SLACK_CLIENT_ID"],
        client_secret=os.environ["SLACK_CLIENT_SECRET"],
        scopes=[
            "channels:history",
            "channels:read",
            "chat:write",
            "commands",
            "users:read"
        ],
        user_scopes=[],  # User token scopes if needed
        installation_store=DatabaseInstallationStore(),
        state_store=FileOAuthStateStore(expiration_seconds=600)
    )
)

# OAuth routes are handled a
```

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | critical | ## Acknowledge immediately, process later |
| Issue | critical | ## Proper state validation |
| Issue | critical | ## Never hardcode or log tokens |
| Issue | high | ## Request minimum required scopes |
| Issue | medium | ## Know and respect the limits |
| Issue | high | ## Socket Mode: Only for development |
| Issue | critical | ## Bolt handles this automatically |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Slack Gif Creator
---
name: slack-gif-creator
description: "Knowledge and utilities for creating animated GIFs optimized for Slack. Provides constraints, validation tools, and animation concepts. Use when users request animated GIFs for Slack like \"..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# Slack GIF Creator

A toolkit providing utilities and knowledge for creating animated GIFs optimized for Slack.

## Slack Requirements

**Dimensions:**
- Emoji GIFs: 128x128 (recommended)
- Message GIFs: 480x480

**Parameters:**
- FPS: 10-30 (lower is smaller file size)
- Colors: 48-128 (fewer = smaller file size)
- Duration: Keep under 3 seconds for emoji GIFs

## Core Workflow

```python
from core.gif_builder import GIFBuilder
from PIL import Image, ImageDraw

# 1. Create builder
builder = GIFBuilder(width=128, height=128, fps=10)

# 2. Generate frames
for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))
    draw = ImageDraw.Draw(frame)

    # Draw your animation using PIL primitives
    # (circles, polygons, lines, etc.)

    builder.add_frame(frame)

# 3. Save with optimization
builder.save('output.gif', num_colors=48, optimize_for_emoji=True)
```

## Drawing Graphics

### Working with User-Uploaded Images
If a user uploads an image, consider whether they want to:
- **Use it directly** (e.g., "animate this", "split this into frames")
- **Use it as inspiration** (e.g., "make something like this")

Load and work with images using PIL:
```python
from PIL import Image

uploaded = Image.open('file.png')
# Use directly, or just as reference for colors/style
```

### Drawing from Scratch
When drawing graphics from scratch, use PIL ImageDraw primitives:

```python
from PIL import ImageDraw

draw = ImageDraw.Draw(frame)

# Circles/ovals
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# Stars, triangles, any polygon
points = [(x1, y1), (x2, y2), (x3, y3), ...]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b), width=3)

# Lines
draw.line([(x1, y1), (x2, y2)], fill=(r, g, b), width=5)

# Rectangles
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)
```

**Don't use:** Emoji fonts (unreliable across platforms) or assume pre-packaged graphics exist in this skill.

### Making Graphics Look Good

Graphics should look polished and creative, not basic. Here's how:

**Use thicker lines** - Always set `width=2` or higher for outlines and lines. Thin lines (width=1) look choppy and amateurish.

**Add visual depth**:
- Use gradients for backgrounds (`create_gradient_background`)
- Layer multiple shapes for complexity (e.g., a star with a smaller star inside)

**Make shapes more interesting**:
- Don't just draw a plain circle - add highlights, rings, or patterns
- Stars can have glows (draw larger, semi-transparent versions behind)
- Combine multiple shapes (stars + sparkles, circles + rings)

**Pay attention to colors**:
- Use vibrant, complementary colors
- Add contrast (dark outlines on light shapes, light outlines on dark shapes)
- Consider the overall composition

**For complex shapes** (hearts, snowflakes, etc.):
- Use combinations of polygons and ellipses
- Calculate points carefully for symmetry
- Add details (a heart can have a highlight curve, snowflakes have intricate branches)

Be creative and detailed! A good Slack GIF should look polished, not like placeholder graphics.

## Available Utilities

### GIFBuilder (`core.gif_builder`)
Assembles frames and optimizes for Slack:
```python
builder = GIFBuilder(width=128, height=128, fps=10)
builder.add_frame(frame)  # Add PIL Image
builder.add_frames(frames)  # Add list of frames
builder.save('out.gif', num_colors=48, optimize_for_emoji=True, remove_duplicates=True)
```

### Validators (`core.validators`)
Check if GIF meets Slack requirements:
```python
from core.validators import validate_gif, is_slack_ready

# Detailed validation
passes, info = validate_gif('my.gif', is_emoji=True, verbose=True)

# Quick check
if is_slack_ready('my.gif'):
    print("Ready!")
```

### Easing Functions (`core.easing`)
Smooth motion instead of linear:
```python
from core.easing import interpolate

# Progress from 0.0 to 1.0
t = i / (num_frames - 1)

# Apply easing
y = interpolate(start=0, end=400, t=t, easing='ease_out')

# Available: linear, ease_in, ease_out, ease_in_out,
#           bounce_out, elastic_out, back_out
```

### Frame Helpers (`core.frame_composer`)
Convenience functions for common needs:
```python
from core.frame_composer import (
    create_blank_frame,         # Solid color background
    create_gradient_background,  # Vertical gradient
    draw_circle,                # Helper for circles
    draw_text,                  # Simple text rendering
    draw_star                   # 5-pointed star
)
```

## Animation Concepts

### Shake/Vibrate
Offset object position with oscillation:
- Use `math.sin()` or `math.cos()` with frame index
- Add small random variations for natural feel
- Apply to x and/or y position

### Pulse/Heartbeat
Scale object size rhythmically:
- Use `math.sin(t * frequency * 2 * math.pi)` for smooth pulse
- For heartbeat: two quick pulses then pause (adjust sine wave)
- Scale between 0.8 and 1.2 of base size

### Bounce
Object falls and bounces:
- Use `interpolate()` with `easing='bounce_out'` for landing
- Use `easing='ease_in'` for falling (accelerating)
- Apply gravity by increasing y velocity each frame

### Spin/Rotate
Rotate object around center:
- PIL: `image.rotate(angle, resample=Image.BICUBIC)`
- For wobble: use sine wave for angle instead of linear

### Fade In/Out
Gradually appear or disappear:
- Create RGBA image, adjust alpha channel
- Or use `Image.blend(image1, image2, alpha)`
- Fade in: alpha from 0 to 1
- Fade out: alpha from 1 to 0

### Slide
Move object from off-screen to position:
- Start position: outside frame bounds
- End position: target location
- Use `interpolate()` with `easing='ease_out'` for smooth stop
- For overshoot: use `easing='back_out'`

### Zoom
Scale and position for zoom effect:
- Zoom in: scale from 0.1 to 2.0, crop center
- Zoom out: scale from 2.0 to 1.0
- Can add motion blur for drama (PIL filter)

### Explode/Particle Burst
Create particles radiating outward:
- Generate particles with random angles and velocities
- Update each particle: `x += vx`, `y += vy`
- Add gravity: `vy += gravity_constant`
- Fade out particles over time (reduce alpha)

## Optimization Strategies

Only when asked to make the file size smaller, implement a few of the following methods:

1. **Fewer frames** - Lower FPS (10 instead of 20) or shorter duration
2. **Fewer colors** - `num_colors=48` instead of 128
3. **Smaller dimensions** - 128x128 instead of 480x480
4. **Remove duplicates** - `remove_duplicates=True` in save()
5. **Emoji mode** - `optimize_for_emoji=True` auto-optimizes

```python
# Maximum optimization for emoji
builder.save(
    'emoji.gif',
    num_colors=48,
    optimize_for_emoji=True,
    remove_duplicates=True
)
```

## Philosophy

This skill provides:
- **Knowledge**: Slack's requirements and animation concepts
- **Utilities**: GIFBuilder, validators, easing functions
- **Flexibility**: Create the animation logic using PIL primitives

It does NOT provide:
- Rigid animation templates or pre-made functions
- Emoji font rendering (unreliable across platforms)
- A library of pre-packaged graphics built into the skill

**Note on user uploads**: This skill doesn't include pre-built graphics, but if a user uploads an image, use PIL to load and work with it - interpret based on their request whether they want it used directly or just as inspiration.

Be creative! Combine concepts (bouncing + rotating, pulsing + sliding, etc.) and use PIL's full capabilities.

## Dependencies

```bash
pip install pillow imageio numpy
```

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Telegram
---
name: telegram
description: Integracao completa com Telegram Bot API. Setup com BotFather, mensagens, webhooks, inline keyboards, grupos, canais. Boilerplates Node.js e Python.
risk: critical
source: community
date_added: '2026-03-06'
author: renat
tags:
- messaging
- telegram
- bots
- webhooks
tools:
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
- agent-compatible
---

# Telegram Bot API - Integracao Profissional

## Overview

Integracao completa com Telegram Bot API. Setup com BotFather, mensagens, webhooks, inline keyboards, grupos, canais. Boilerplates Node.js e Python.

## When to Use This Skill

- When the user mentions "telegram" or related topics
- When the user mentions "bot telegram" or related topics
- When the user mentions "telegram bot" or related topics
- When the user mentions "api telegram" or related topics
- When the user mentions "chatbot telegram" or related topics
- When the user mentions "mensagem telegram" or related topics

## Do Not Use This Skill When

- The task is unrelated to telegram
- A simpler, more specific tool can handle the request
- The user needs general-purpose assistance without domain expertise

## How It Works

Skill para implementar bots profissionais no Telegram usando a Bot API oficial. Suporta Node.js/TypeScript e Python.

## Overview

A Telegram Bot API permite criar bots que interagem com usuarios via mensagens, comandos, inline keyboards, pagamentos e muito mais. Bots sao criados pelo @BotFather e autenticados via token unico.

**Base URL:** `https://api.telegram.org/bot<TOKEN>/METHOD_NAME`
**Metodos HTTP:** GET e POST
**Formatos de parametros:** query string, application/x-www-form-urlencoded, application/json, multipart/form-data (uploads)
**Limite de arquivos:** 50MB download, 20MB upload (via multipart), 50MB via URL

**Portas suportadas para webhooks:** 443, 80, 88, 8443

**Pre-requisitos:**
- Conta no Telegram
- Bot criado via @BotFather (fornece o token)
- Token no formato: `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`

Se o usuario nao tem um bot criado, oriente a conversar com @BotFather no Telegram e enviar `/newbot`.

---

## Decision Tree

```
O usuario precisa criar um bot?
├── SIM → Secao "Setup com BotFather" abaixo
└── NAO → Qual linguagem?
    ├── Node.js/TypeScript
    └── Python
    → O que quer fazer?
       ├── Enviar mensagens → Secao "Tipos de Mensagem"
       ├── Receber mensagens → Secao "Receber Updates"
       ├── Teclados interativos → Secao "Keyboards"
       ├── Gerenciar grupos/canais → references/chat-management.md
       ├── Webhook setup → references/webhook-setup.md
       ├── Inline mode → references/advanced-features.md
       ├── Pagamentos → references/advanced-features.md
       ├── Bot de atendimento com IA → Secao "Automacao com IA"
       └── Referencia completa da API → references/api-reference.md
```

Para iniciar um projeto do zero com boilerplate pronto:
```bash
python scripts/setup_project.py --language nodejs --path ./meu-bot-telegram

## Ou

python scripts/setup_project.py --language python --path ./meu-bot-telegram
```

Para testar se o token do bot funciona:
```bash
python scripts/test_bot.py --token "SEU_TOKEN"
```

Para enviar uma mensagem de teste:
```bash
python scripts/send_message.py --token "SEU_TOKEN" --chat-id "CHAT_ID" --text "Hello!"
```

---

## Setup Com Botfather

1. Abra o Telegram e busque @BotFather
2. Envie `/newbot`
3. Escolha nome de exibicao (ex: "Meu Bot Incrivel")
4. Escolha username (deve terminar com "bot", ex: `meu_incrivel_bot`)
5. BotFather retorna o token - guarde com seguranca
6. Comandos uteis do BotFather:
   - `/setdescription` - descricao do bot
   - `/setabouttext` - texto "sobre" do bot
   - `/setuserpic` - foto de perfil
   - `/setcommands` - lista de comandos
   - `/mybots` - gerenciar bots existentes
   - `/setinline` - habilitar inline mode
   - `/setprivacy` - modo privacidade em grupos

---

## Variaveis De Ambiente

```env
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
```

## Node.Js/Typescript

```typescript
// Instalar: npm install node-telegram-bot-api dotenv
// Para TypeScript: npm install -D @types/node-telegram-bot-api typescript
import TelegramBot from 'node-telegram-bot-api';
import dotenv from 'dotenv';
dotenv.config();

const bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN!, { polling: true });

bot.onText(/\/start/, (msg) => {
  bot.sendMessage(msg.chat.id, 'Ola! Eu sou seu bot. Como posso ajudar?');
});

bot.on('message', (msg) => {
  if (msg.text && !msg.text.startsWith('/')) {
    bot.sendMessage(msg.chat.id, `Voce disse: ${msg.text}`);
  }
});
```

## Python

```python

## Instalar: Pip Install Python-Telegram-Bot Python-Dotenv

import os
from dotenv import load_dotenv
from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes

load_dotenv()

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text('Ola! Eu sou seu bot. Como posso ajudar?')

async def echo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(f'Voce disse: {update.message.text}')

app = Application.builder().token(os.getenv('TELEGRAM_BOT_TOKEN')).build()
app.add_handler(CommandHandler('start', start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, echo))
app.run_polling()
```

## Sem Biblioteca (Http Puro)

```python
import requests

TOKEN = "SEU_TOKEN"
BASE = f"https://api.telegram.org/bot{TOKEN}"

## Verificar Bot

r = requests.get(f"{BASE}/getMe")
print(r.json())

## Enviar Mensagem

r = requests.post(f"{BASE}/sendMessage", json={
    "chat_id": "CHAT_ID",
    "text": "Hello from pure HTTP!",
    "parse_mode": "HTML"
})
print(r.json())
```

---

## Tipos De Mensagem

O Telegram suporta diversos tipos de conteudo. Todos os metodos aceitam `chat_id`, `reply_parameters` (para responder), `reply_markup` (para keyboards), `disable_notification` e `protect_content`.

## Html (Recomendado)

await bot.send_message(
    chat_id=chat_id,
    text="<b>Negrito</b>, <i>italico</i>, <code>codigo</code>, <a href='https://example.com'>link</a>",
    parse_mode="HTML"
)

## Markdownv2 (Escapar Caracteres Especiais: _ * [ ] ( ) ~ ` > # + - = | { } . !)

await bot.send_message(
    chat_id=chat_id,
    text="*Negrito*, _italico_, `codigo`, [link](https://example\\.com)",
    parse_mode="MarkdownV2"
)
```

## Foto (Por Url, File_Id Ou Upload)

await bot.send_photo(chat_id, photo="https://example.com/img.jpg", caption="Legenda aqui")

## Documento

await bot.send_document(chat_id, document=open("relatorio.pdf", "rb"), caption="Relatorio mensal")

## Video

await bot.send_video(chat_id, video="https://example.com/video.mp4", caption="Assista!")

## Audio

await bot.send_audio(chat_id, audio=open("musica.mp3", "rb"), title="Minha Musica")

## Voz (Ogg Com Opus)

await bot.send_voice(chat_id, voice=open("audio.ogg", "rb"))

## Localizacao

await bot.send_location(chat_id, latitude=-23.5505, longitude=-46.6333)

## Contato

await bot.send_contact(chat_id, phone_number="+5511999999999", first_name="Joao")

## Enquete

await bot.send_poll(
    chat_id, question="Qual sua cor favorita?",
    options=["Azul", "Verde", "Vermelho"],
    is_anonymous=False
)

## Grupo De Midias

await bot.send_media_group(chat_id, media=[
    InputMediaPhoto("url1", caption="Foto 1"),
    InputMediaPhoto("url2"),
    InputMediaVideo("url3")
])

## Acao De Chat (Typing, Upload_Photo, Etc.)

await bot.send_chat_action(chat_id, action="typing")
```

## Node.Js Equivalente

```typescript
// Foto
bot.sendPhoto(chatId, 'https://example.com/img.jpg', { caption: 'Legenda' });

// Documento
bot.sendDocument(chatId, fs.createReadStream('relatorio.pdf'), { caption: 'Relatorio' });

// Localizacao
bot.sendLocation(chatId, -23.5505, -46.6333);

// Enquete
bot.sendPoll(chatId, 'Qual sua cor favorita?', ['Azul', 'Verde', 'Vermelho']);
```

---

## Inline Keyboard (Botoes Dentro Da Mensagem)

```python
from telegram import InlineKeyboardButton, InlineKeyboardMarkup

keyboard = InlineKeyboardMarkup([
    [InlineKeyboardButton("Opcao A", callback_data="opt_a"),
     InlineKeyboardButton("Opcao B", callback_data="opt_b")],
    [InlineKeyboardButton("Abrir Site", url="https://example.com")],
    [InlineKeyboardButton("Compartilhar", switch_inline_query="texto")]
])

await bot.send_message(chat_id, "Escolha uma opcao:", reply_markup=keyboard)

## Handler De Callback

async def button_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()  # Importante: sempre responder o callback
    await query.edit_message_text(f"Voce escolheu: {query.data}")

app.add_handler(CallbackQueryHandler(button_callback))
```

## Reply Keyboard (Teclado Customizado)

```python
from telegram import ReplyKeyboardMarkup, KeyboardButton

keyboard = ReplyKeyboardMarkup(
    [[KeyboardButton("Enviar Localizacao", request_location=True)],
     [KeyboardButton("Enviar Contato", request_contact=True)],
     ["Opcao 1", "Opcao 2"]],
    resize_keyboard=True,
    one_time_keyboard=True
)

await bot.send_message(chat_id, "Escolha:", reply_markup=keyboard)
```

## Remover Teclado

```python
from telegram import ReplyKeyboardRemove
await bot.send_message(chat_id, "Teclado removido", reply_markup=ReplyKeyboardRemove())
```

---

## Receber Updates

Existem duas formas de receber updates: **Long Polling** e **Webhooks**.

## Long Polling (Desenvolvimento)

Mais simples, ideal para desenvolvimento. O bot faz requisicoes periodicas ao servidor do Telegram.

```python

## Python-Telegram-Bot Ja Faz Isso Automaticamente

app.run_polling(allowed_updates=Update.ALL_TYPES)
```

```typescript
// node-telegram-bot-api com polling
const bot = new TelegramBot(token, { polling: true });
```

## Webhooks (Producao)

Para producao, webhooks sao mais eficientes. O Telegram envia updates via POST para sua URL HTTPS.

Leia `references/webhook-setup.md` para configuracao completa com Express, Flask, ngrok e deploy.

Setup rapido:

```python

## Flask Webhook

from flask import Flask, request
import requests

app = Flask(__name__)
TOKEN = "SEU_TOKEN"
BASE = f"https://api.telegram.org/bot{TOKEN}"

@app.route(f"/webhook/{TOKEN}", methods=["POST"])
def webhook():
    update = request.get_json()
    if "message" in update and "text" in update["message"]:
        chat_id = update["message"]["chat"]["id"]
        text = update["message"]["text"]
        requests.post(f"{BASE}/sendMessage", json={
            "chat_id": chat_id,
            "text": f"Recebi: {text}"
        })
    return "OK", 200

## Registrar Webhook

requests.post(f"{BASE}/setWebhook", json={
    "url": "https://seu-dominio.com/webhook/" + TOKEN,
    "allowed_updates": ["message", "callback_query"],
    "secret_token": "seu_secret_seguro_aqui"
})
```

---

## Comandos Do Bot

Registre comandos para aparecerem no menu do Telegram:

```python
from telegram import BotCommand

await bot.set_my_commands([
    BotCommand("start", "Iniciar o bot"),
    BotCommand("help", "Ver comandos disponiveis"),
    BotCommand("settings", "Configuracoes"),
    BotCommand("status", "Ver status do servico"),
])
```

Via HTTP:
```bash
curl -X POST "https://api.telegram.org/bot$TOKEN/setMyCommands" \
  -H "Content-Type: application/json" \
  -d '{"commands":[{"command":"start","description":"Iniciar o bot"},{"command":"help","description":"Ajuda"}]}'
```

---

## Automacao Com Ia

Padrao para bot de atendimento com IA (AI assistant, GPT, etc.):

```python
from telegram import Update
from telegram.ext import Application, MessageHandler, filters, ContextTypes
import anthropic  # ou openai

client = anthropic.Anthropic()
user_conversations = {}  # chat_id -> messages history

async def ai_response(update: Update, context: ContextTypes.DEFAULT_TYPE):
    chat_id = update.message.chat_id
    user_text = update.message.text

    # Indicar que esta digitando
    await context.bot.send_chat_action(chat_id, "typing")

    # Manter historico
    if chat_id not in user_conversations:
        user_conversations[chat_id] = []

    user_conversations[chat_id].append({"role": "user", "content": user_text})

    # Chamar IA
    response = client.messages.create(
        model="example-balanced-model",
        max_tokens=1024,
        system="Voce e um assistente prestativo. Responda em portugues.",
        messages=user_conversations[chat_id]
    )

    reply = response.content[0].text
    user_conversations[chat_id].append({"role": "assistant", "content": reply})

    # Limitar historico (ultimas 20 mensagens)
    if len(user_conversations[chat_id]) > 20:
        user_conversations[chat_id] = user_conversations[chat_id][-20:]

    await update.message.reply_text(reply)

app = Application.builder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, ai_response))
app.run_polling()
```

---

## Editar Texto

await bot.edit_message_text(
    chat_id=chat_id,
    message_id=msg.message_id,
    text="Texto atualizado!",
    parse_mode="HTML"
)

## Editar Markup (Botoes)

await bot.edit_message_reply_markup(
    chat_id=chat_id,
    message_id=msg.message_id,
    reply_markup=new_keyboard
)

## Deletar Mensagem

await bot.delete_message(chat_id=chat_id, message_id=msg.message_id)

## Encaminhar Mensagem

await bot.forward_message(
    chat_id=dest_chat_id,
    from_chat_id=source_chat_id,
    message_id=msg.message_id
)
```

---

## Tratamento De Erros

```python
from telegram.error import TelegramError, BadRequest, TimedOut, NetworkError

async def safe_send(bot, chat_id, text, **kwargs):
    """Envio com retry e tratamento de erros."""
    max_retries = 3
    for attempt in range(max_retries):
        try:
            return await bot.send_message(chat_id, text, **kwargs)
        except TimedOut:
            if attempt < max_retries - 1:
                await asyncio.sleep(2 ** attempt)
                continue
            raise
        except BadRequest as e:
            if "chat not found" in str(e).lower():
                print(f"Chat {chat_id} nao encontrado")
                return None
            raise
        except NetworkError:
            if attempt < max_retries - 1:
                await asyncio.sleep(2 ** attempt)
                continue
            raise
```

---

## Rate Limits

- **Mensagens em chat privado:** ~30 msg/segundo
- **Mensagens em grupo:** ~20 msg/minuto por grupo
- **Broadcast geral:** ~30 msg/segundo no total
- **Bulk notifications:** use `asyncio.sleep(0.05)` entre envios para evitar flood

Se receber erro 429 (Too Many Requests), respeite o `retry_after` retornado.

---

## Referencia De Arquivos

| Topico | Arquivo |
|--------|---------|
| Setup de webhooks | `references/webhook-setup.md` |
| Gerenciamento de chats | `references/chat-management.md` |
| Recursos avancados | `references/advanced-features.md` |
| Referencia completa da API | `references/api-reference.md` |
| Boilerplate Node.js | `assets/boilerplate/nodejs/` |
| Boilerplate Python | `assets/boilerplate/python/` |
| Exemplos de payloads | `assets/examples/` |

## Best Practices

- Provide clear, specific context about your project and requirements
- Review all suggestions before applying them to production code
- Combine with other complementary skills for comprehensive analysis

## Common Pitfalls

- Using this skill for tasks outside its domain expertise
- Applying recommendations without understanding your specific context
- Not providing enough project context for accurate analysis

## Related Skills

- `instagram` - Complementary skill for enhanced analysis
- `social-orchestrator` - Complementary skill for enhanced analysis
- `whatsapp-cloud-api` - Complementary skill for enhanced analysis

## Imported Module: Telegram Mini App
---
name: telegram-mini-app
description: "Expert in building Telegram Mini Apps (TWA) - web apps that run inside Telegram with native-like experience. Covers the TON ecosystem, Telegram Web App API, payments, user authentication, and build..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Telegram Mini App

**Role**: Telegram Mini App Architect

You build apps where 800M+ Telegram users already are. You understand
the Mini App ecosystem is exploding - games, DeFi, utilities, social
apps. You know TON blockchain and how to monetize with crypto. You
design for the Telegram UX paradigm, not traditional web.

## Capabilities

- Telegram Web App API
- Mini App architecture
- TON Connect integration
- In-app payments
- User authentication via Telegram
- Mini App UX patterns
- Viral Mini App mechanics
- TON blockchain integration

## Patterns

### Mini App Setup

Getting started with Telegram Mini Apps

**When to use**: When starting a new Mini App

```javascript
## Mini App Setup

### Basic Structure
```html
<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
</head>
<body>
  <script>
    const tg = window.Telegram.WebApp;
    tg.ready();
    tg.expand();

    // User data
    const user = tg.initDataUnsafe.user;
    console.log(user.first_name, user.id);
  </script>
</body>
</html>
```

### React Setup
```jsx
// hooks/useTelegram.js
export function useTelegram() {
  const tg = window.Telegram?.WebApp;

  return {
    tg,
    user: tg?.initDataUnsafe?.user,
    queryId: tg?.initDataUnsafe?.query_id,
    expand: () => tg?.expand(),
    close: () => tg?.close(),
    ready: () => tg?.ready(),
  };
}

// App.jsx
function App() {
  const { tg, user, expand, ready } = useTelegram();

  useEffect(() => {
    ready();
    expand();
  }, []);

  return <div>Hello, {user?.first_name}</div>;
}
```

### Bot Integration
```javascript
// Bot sends Mini App
bot.command('app', (ctx) => {
  ctx.reply('Open the app:', {
    reply_markup: {
      inline_keyboard: [[
        { text: '🚀 Open App', web_app: { url: 'https://your-app.com' } }
      ]]
    }
  });
});
```
```

### TON Connect Integration

Wallet connection for TON blockchain

**When to use**: When building Web3 Mini Apps

```python
## TON Connect Integration

### Setup
```bash
npm install @tonconnect/ui-react
```

### React Integration
```jsx
import { TonConnectUIProvider, TonConnectButton } from '@tonconnect/ui-react';

// Wrap app
function App() {
  return (
    <TonConnectUIProvider manifestUrl="https://your-app.com/tonconnect-manifest.json">
      <MainApp />
    </TonConnectUIProvider>
  );
}

// Use in components
function WalletSection() {
  return (
    <TonConnectButton />
  );
}
```

### Manifest File
```json
{
  "url": "https://your-app.com",
  "name": "Your Mini App",
  "iconUrl": "https://your-app.com/icon.png"
}
```

### Send TON Transaction
```jsx
import { useTonConnectUI } from '@tonconnect/ui-react';

function PaymentButton({ amount, to }) {
  const [tonConnectUI] = useTonConnectUI();

  const handlePay = async () => {
    const transaction = {
      validUntil: Math.floor(Date.now() / 1000) + 60,
      messages: [{
        address: to,
        amount: (amount * 1e9).toString(), // TON to nanoton
      }]
    };

    await tonConnectUI.sendTransaction(transaction);
  };

  return <button onClick={handlePay}>Pay {amount} TON</button>;
}
```
```

### Mini App Monetization

Making money from Mini Apps

**When to use**: When planning Mini App revenue

```javascript
## Mini App Monetization

### Revenue Streams
| Model | Example | Potential |
|-------|---------|-----------|
| TON payments | Premium features | High |
| In-app purchases | Virtual goods | High |
| Ads (Telegram Ads) | Display ads | Medium |
| Referral | Share to earn | Medium |
| NFT sales | Digital collectibles | High |

### Telegram Stars (New!)
```javascript
// In your bot
bot.command('premium', (ctx) => {
  ctx.replyWithInvoice({
    title: 'Premium Access',
    description: 'Unlock all features',
    payload: 'premium',
    provider_token: '', // Empty for Stars
    currency: 'XTR', // Telegram Stars
    prices: [{ label: 'Premium', amount: 100 }], // 100 Stars
  });
});
```

### Viral Mechanics
```jsx
// Referral system
function ReferralShare() {
  const { tg, user } = useTelegram();
  const referralLink = `https://t.me/your_bot?start=ref_${user.id}`;

  const share = () => {
    tg.openTelegramLink(
      `https://t.me/share/url?url=${encodeURIComponent(referralLink)}&text=Check this out!`
    );
  };

  return <button onClick={share}>Invite Friends (+10 coins)</button>;
}
```

### Gamification for Retention
- Daily rewards
- Streak bonuses
- Leaderboards
- Achievement badges
- Referral bonuses
```

## Anti-Patterns

### ❌ Ignoring Telegram Theme

**Why bad**: Feels foreign in Telegram.
Bad user experience.
Jarring transitions.
Users don't trust it.

**Instead**: Use tg.themeParams.
Match Telegram colors.
Use native-feeling UI.
Test in both light/dark.

### ❌ Desktop-First Mini App

**Why bad**: 95% of Telegram is mobile.
Touch targets too small.
Doesn't fit in Telegram UI.
Scrolling issues.

**Instead**: Mobile-first always.
Test on real phones.
Touch-friendly buttons.
Fit within Telegram frame.

### ❌ No Loading States

**Why bad**: Users think it's broken.
Poor perceived performance.
High exit rate.
Confusion.

**Instead**: Show skeleton UI.
Loading indicators.
Progressive loading.
Optimistic updates.

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Not validating initData from Telegram | high | ## Validating initData |
| TON Connect not working on mobile | high | ## TON Connect Mobile Issues |
| Mini App feels slow and janky | medium | ## Mini App Performance |
| Custom buttons instead of MainButton | medium | ## Using MainButton Properly |

## Related Skills

Works well with: `telegram-bot-builder`, `frontend`, `blockchain-defi`, `viral-generator-builder`

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Twilio Communications
---
name: twilio-communications
description: "Build communication features with Twilio: SMS messaging, voice calls, WhatsApp Business API, and user verification (2FA). Covers the full spectrum from simple notifications to complex IVR systems a..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Twilio Communications

## Patterns

### SMS Sending Pattern

Basic pattern for sending SMS messages with Twilio.
Handles the fundamentals: phone number formatting, message delivery,
and delivery status callbacks.

Key considerations:
- Phone numbers must be in E.164 format (+1234567890)
- Default rate limit: 80 messages per second (MPS)
- Messages over 160 characters are split (and cost more)
- Carrier filtering can block messages (especially to US numbers)


**When to use**: ['Sending notifications to users', 'Transactional messages (order confirmations, shipping)', 'Alerts and reminders']

```python
from twilio.rest import Client
from twilio.base.exceptions import TwilioRestException
import os
import re

class TwilioSMS:
    """
    SMS sending with proper error handling and validation.
    """

    def __init__(self):
        self.client = Client(
            os.environ["TWILIO_ACCOUNT_SID"],
            os.environ["TWILIO_AUTH_TOKEN"]
        )
        self.from_number = os.environ["TWILIO_PHONE_NUMBER"]

    def validate_e164(self, phone: str) -> bool:
        """Validate phone number is in E.164 format."""
        pattern = r'^\+[1-9]\d{1,14}$'
        return bool(re.match(pattern, phone))

    def send_sms(
        self,
        to: str,
        body: str,
        status_callback: str = None
    ) -> dict:
        """
        Send an SMS message.

        Args:
            to: Recipient phone number in E.164 format
            body: Message text (160 chars = 1 segment)
            status_callback: URL for delivery status webhooks

        Returns:
            Message SID and status
        """
        # Validate phone number format
        if not self.validate_e164(to):
            return {
                "success": False,
                "error": "Phone number must be in E.164 format (+1234567890)"
            }

        # Check message length (warn about segmentation)
        segment_count = (len(body) + 159) // 160
        if segment_count > 1:
            print(f"Warning: Message will be sent as {segment_count} segments")

        try:
            message = self.client.messages.create(
                to=to,
                from_=self.from_number,
                body=body,
                status_callback=status_callback
            )

            return {
                "success": True,
                "message_sid": message.sid,
                "status": message.status,
                "segments": segment_count
            }

        except TwilioRestException as e:
            return self._handle_error(e)

    def _handle_error(self, error: Twilio
```

### Twilio Verify Pattern (2FA/OTP)

Use Twilio Verify for phone number verification and 2FA.
Handles code generation, delivery, rate limiting, and fraud prevention.

Key benefits over DIY OTP:
- Twilio manages code generation and expiration
- Built-in fraud prevention (saved customers $82M+ blocking 747M attempts)
- Handles rate limiting automatically
- Multi-channel: SMS, Voice, Email, Push, WhatsApp

Google found SMS 2FA blocks "100% of automated bots, 96% of bulk
phishing attacks, and 76% of targeted attacks."


**When to use**: ['User phone number verification at signup', 'Two-factor authentication (2FA)', 'Password reset verification', 'High-value transaction confirmation']

```python
from twilio.rest import Client
from twilio.base.exceptions import TwilioRestException
import os
from enum import Enum
from typing import Optional

class VerifyChannel(Enum):
    SMS = "sms"
    CALL = "call"
    EMAIL = "email"
    WHATSAPP = "whatsapp"

class TwilioVerify:
    """
    Phone verification with Twilio Verify.
    Never store OTP codes - Twilio handles it.
    """

    def __init__(self, verify_service_sid: str = None):
        self.client = Client(
            os.environ["TWILIO_ACCOUNT_SID"],
            os.environ["TWILIO_AUTH_TOKEN"]
        )
        # Create a Verify Service in Twilio Console first
        self.service_sid = verify_service_sid or os.environ["TWILIO_VERIFY_SID"]

    def send_verification(
        self,
        to: str,
        channel: VerifyChannel = VerifyChannel.SMS,
        locale: str = "en"
    ) -> dict:
        """
        Send verification code to phone/email.

        Args:
            to: Phone number (E.164) or email
            channel: SMS, call, email, or whatsapp
            locale: Language code for message

        Returns:
            Verification status
        """
        try:
            verification = self.client.verify \
                .v2 \
                .services(self.service_sid) \
                .verifications \
                .create(
                    to=to,
                    channel=channel.value,
                    locale=locale
                )

            return {
                "success": True,
                "status": verification.status,  # "pending"
                "channel": channel.value,
                "valid": verification.valid
            }

        except TwilioRestException as e:
            return self._handle_verify_error(e)

    def check_verification(self, to: str, code: str) -> dict:
        """
        Check if verification code is correct.

        Args:
            to: Phone number or email that received code
            code: The code entered by user

        R
```

### TwiML IVR Pattern

Build Interactive Voice Response (IVR) systems using TwiML.
TwiML (Twilio Markup Language) is XML that tells Twilio what to do
when receiving calls.

Core TwiML verbs:
- <Say>: Text-to-speech
- <Play>: Play audio file
- <Gather>: Collect keypad/speech input
- <Dial>: Connect to another number
- <Record>: Record caller's voice
- <Redirect>: Move to another TwiML endpoint

Key insight: Twilio makes HTTP request to your webhook, you return
TwiML, Twilio executes it. Stateless, so use URL params or sessions.


**When to use**: ['Phone menu systems (press 1 for sales...)', 'Automated customer support', 'Appointment reminders with confirmation', 'Voicemail systems']

```python
from flask import Flask, request, Response
from twilio.twiml.voice_response import VoiceResponse, Gather
from twilio.request_validator import RequestValidator
import os

app = Flask(__name__)

def validate_twilio_request(f):
    """Decorator to validate requests are from Twilio."""
    def wrapper(*args, **kwargs):
        validator = RequestValidator(os.environ["TWILIO_AUTH_TOKEN"])

        # Get request details
        url = request.url
        params = request.form.to_dict()
        signature = request.headers.get("X-Twilio-Signature", "")

        if not validator.validate(url, params, signature):
            return "Invalid request", 403

        return f(*args, **kwargs)
    wrapper.__name__ = f.__name__
    return wrapper

@app.route("/voice/incoming", methods=["POST"])
@validate_twilio_request
def incoming_call():
    """Handle incoming call with IVR menu."""
    response = VoiceResponse()

    # Gather digits with timeout
    gather = Gather(
        num_digits=1,
        action="/voice/menu-selection",
        method="POST",
        timeout=5
    )
    gather.say(
        "Welcome to Acme Corp. "
        "Press 1 for sales. "
        "Press 2 for support. "
        "Press 3 to leave a message."
    )
    response.append(gather)

    # If no input, repeat
    response.redirect("/voice/incoming")

    return Response(str(response), mimetype="text/xml")

@app.route("/voice/menu-selection", methods=["POST"])
@validate_twilio_request
def menu_selection():
    """Route based on menu selection."""
    response = VoiceResponse()
    digit = request.form.get("Digits", "")

    if digit == "1":
        # Transfer to sales
        response.say("Connecting you to sales.")
        response.dial(os.environ["SALES_PHONE"])

    elif digit == "2":
        # Transfer to support
        response.say("Connecting you to support.")
        response.dial(os.environ["SUPPORT_PHONE"])

    elif digit == "3":
        # Voicemail
        response.say("Please leave a message after 
```

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | ## Track opt-out status in your database |
| Issue | medium | ## Implement retry logic for transient failures |
| Issue | high | ## Register for A2P 10DLC (US requirement) |
| Issue | critical | ## ALWAYS validate the signature |
| Issue | high | ## Track session windows per user |
| Issue | critical | ## Never hardcode credentials |
| Issue | medium | ## Implement application-level rate limiting too |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

