# Servidor AWS - Gerenciamento de Screens e Webhooks
Documentação do servidor da AWS para fazer o regenciamento dos programas existentes.
Esta documentação foi revisada e atualizada em 03/11/2025.

## 📄 Objetivo
O servidor AWS hospeda os **webhooks** e **serviços de integração**. Cada processo é executado dentro de uma **screen** -- um ambiente virtual no terminal que permite rodar programas continuamente mesmo após encerrar a sessão SSH.

Essa prática garente **estabilidade, persistência dos serviços e segurança em produção**

## ⚙️ Estrutura do servidor
- Diretório onde estão licalizados todos os scrips:
```bash
/home/suporte
```
Dentro desse diretório existe o arquivo `LEIAME-INICIAR-PROGRAMAS`, que contém intruções básicas para iniciar todos os programas.

## 🚨 Verificação Inicial (após logar no servidor)
Ao conectar via SSH, **verifique se as screens estão ativas**
```
screen -ls
```

### 🔍 Exemplo de saída esperada:
```
There are screens on:
        3392723.carterizacaobp  (11/03/25 15:09:29)     (Detached)
        4083367.santander       (08/22/25 10:01:15)     (Detached)
        2868329.bling   (07/23/25 17:02:29)     (Detached)
        2181656.carterizacao    (06/13/25 10:29:08)     (Detached)
        62238.tokenml   (06/10/25 15:13:55)     (Detached)
        59694.token1023 (06/10/25 15:13:13)     (Detached)
        57932.pix       (06/10/25 15:12:41)     (Detached)
        53217.token1050 (06/10/25 15:11:18)     (Detached)
        48421.meli1023  (06/10/25 15:09:56)     (Detached)
        44093.puxaxml   (06/10/25 15:08:45)     (Detached)
        40268.abbiamo   (06/10/25 15:07:38)     (Detached)
10 Sockets in /run/screen/S-root.
```
Se a lista não aparecer de forma semelhante, **consulte o arquivo**:
```
/home/suporte/LEIAME-INICIAR-PROGRAMAS
```
Para restaurar as screen.

## 🧩 Conceito de Screen
O **GNU Screen** é um gerenciador de sessões no terminal Linux que permite:

- Manter processos em execução após sair do SSH.
- Criar múltiplas sessões simultâneas.
- Reconectar a sessões existentes.

### 👨‍💻 Principais comandos:

| Ação                                 | Comando            |
|--------------------------------------|--------------------|
| Lista todas as screens               | `screen -ls`       |
| Criar nova screen                    | `screen -S <nome>` |
| Sair da screen mantando ela ativa    | `CTRL + A + D`     |
| Reconectar a screen                  | `screen -r <nome>` |
| Desalocar uma screen ocupada         | `screen -d <nome>` |
| Encerrar uma screen (de dentro dela) | `exit`             |

## 💻 Inicialização de Webhooks e Serviços
O passo a passo para inicializar as screen em produção (última atualização 03/11/2025):

### Webhook da abbiamo:
```
screen -S abbiamo
cd /home/suporte
python3 servidor_webhookv3.py
```

### Busca XML do Mercado Livre
```
screen -S token1050
cd /home/suporte/meli/1050
python3 puxaxml.py
```

### Gerador de Token - Loja 1050
```
screen -S token1050
cd /home/suporte/meli/1050/token
python3 gerador_token.py
```

### Webhook de Notificações PIX
```
screen -S pix
cd /home/suporte
python3 servidor_webhook_pix.py
```

### Gerador de token - Loja 1023
```
screen -S token1023
cd /home/suporte/meli/1023/token
python3 gerador_token.py
```

### Webhook do Mercado Livre
```
screen -S tokenml
cd /home/suporte/meli/token_ml
source venv/bin/activate
python3 routes.py
```

### API de Carterização (PPL)
```
screen -S carterizacao
cd /home/suporte/ez_chat/api_carterizacao
source venv/bin/activate
python3 routes.py
```

### Webhook Santander
```
screen -S santander
cd /home/suporte/santander/webhook
source venv/bin/activate
python3 servidor_webhook_santanderv2.py
```

### API de Carterização (BP)
```
screen -S carterizacaobp
cd /home/suporte/ez_chat/api/carterizacao_bp
source venv/bin/activate
python3 routes.py
```

## ⚙️ Gerenciamento das Screens

### 📄 Listagem todas as screens
```
screen -ls
```

### 🔍 Acessar uma screen específica
```
screen -r <nome_da_screen>
```

Exemplo:
```
screen -r santander
```

### 🔓 Desalocar uma screen travada (Attached)
Se aparecer `(Attached)` ao listar:
```
screen -d <nome_da_screen>
```
Depois reconecte com:
```
screen -r <nome_da_screen>
```
