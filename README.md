# 🚨 SAP CPI - Email Error Notification
## SAP BTP CPI - CustomEmailNotification


![Capa](imagens/capa-linkedin.png)



Este projeto demonstra a implementação de um iFlow no SAP Cloud Integration (CPI) para envio automático de notificações de erro por e-mail.


## 📌 Objetivo

Automatizar o monitoramento de falhas em integrações, notificando rapidamente a equipe responsável com informações relevantes para análise e reprocessamento.

---

## ✉️ Funcionalidades

- Envio automático de e-mail em caso de erro
- Template HTML com visual baseado no SAP Fiori
- Informações dinâmicas:
  - CPI Tenant
  - Nome do iFlow
  - Data/Hora
  - Message ID
- Mensagem amigável para time funcional + técnico
- Estrutura compatível com Outlook e clientes de e-mail

---

## 🎨 Layout

O e-mail segue boas práticas de compatibilidade:

- Uso de tabelas (email-friendly)
- Estilo inline (evita quebra no Outlook)
- Destaque visual para status de erro
- Organização em formato de "card" (inspirado no SAP Fiori)

---

## 🛠 Tecnologias utilizadas

- SAP Integration Suite (CPI)
- Content Modifier
- SMTP Adapter
- HTML (Email Template)

---

## 🚀 Possíveis melhorias

- Retry automático com Data Store
- Integração com Microsoft Teams / Slack
- Inclusão de logs técnicos detalhados
- Link direto para Message Monitoring
- Tratamento avançado de exceções (Groovy Script)

---

## 📊 Benefícios

- Redução do tempo de resposta a falhas
- Melhor visibilidade operacional
- Padronização de notificações
- Apoio ao suporte e operação

---


---


# :building_construction: Arquitetura do iFlow

O fluxo foi desenvolvido no SAP Cloud Integration (CPI) seguindo as etapas abaixo.
### Criando nosso Iflow
![Fluxo](imagens/Screenshot_1.png)
<br><br>

### Criando o Integration Flow
![Fluxo](imagens/Screenshot_2.png)
```
Address: /NotificationEmail
```
<br>

### Adicionando o Artefato do Integration Flow
![Fluxo](imagens/Screenshot_3.png)

<br>

### Criando o Integration Flow
![Fluxo](imagens/Screenshot_4.png)
```
CustomEmailNotification
```

<br>
:gear: Etapas da Integração

<br>

### :one:  Manage Security

Criando nosso usuário para enviar o E-Mail
### Security Material
![Fluxo](imagens/Screenshot_5.png)

<br>

### Criando Credentials
![Fluxo](imagens/Screenshot_6.png)

<br>

### Editando Credentials
![Fluxo](imagens/Screenshot_7.png)
```
GmailUser
```
<br>

### :two:  Configuração Google Gmail
### Acessar ao Site
```
https://myaccount.google.com/apppasswords
```
![Fluxo](imagens/Screenshot_8.png)
```
SAP CPI
```
<br>

### Armarzenar a senha
![Fluxo](imagens/Screenshot_9.png)

<br>

### Adicionar a senha nas Credentials
![Fluxo](imagens/Screenshot_10.png)

<br>

### Editando nosso Iflow
![Fluxo](imagens/Screenshot_11.png)

<br>

### :two:  HTTPS Sender

<br>

### Adicionando o HTTPS
![Fluxo](imagens/Screenshot_12.png)

<br>

### Configurando o Endpoint
O fluxo é iniciado através de um endpoint HTTPS, permitindo que aplicações externas consultem o serviço de clima.

![Fluxo](imagens/Screenshot_13.png)

```
/clima
```
<br>




























<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>




### :three: Content Modifier – Definição da cidade

Nesta etapa são definidas as coordenadas geográficas da cidade consultada.

Exemplo utilizado:

latitude  = -23.68
longitude = -46.62

Essas coordenadas representam a cidade de São Paulo.

### Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_13.png)

<br>

### Configurando o Content Modifier - Property
![Fluxo](imagens/Screenshot_14.png)
Renomeamos o Content Modifier 
```
General
Name: cidade
```
Em Property adicionamos
```
Exchange Property
create   -   longitude   -   Constant   -   -46.62
create   -   latitude   -    Constant   -    -23.68
```

<br>

### Renomeamos o Receiver
![Fluxo](imagens/Screenshot_15.png)
```
WeatherAPI
```

<br>

### :four: Request Reply – Consumo da API

O CPI realiza uma chamada HTTP para a API Open-Meteo, buscando as condições climáticas atuais.

O retorno é recebido no formato JSON.

### Adicionamos o Request Reply
![Fluxo](imagens/Screenshot_16.png)

<br>

### Adicionamos o Adapter HTTP
![Fluxo](imagens/Screenshot_17.png)

<br>

### Configuração do Request Reply
![Fluxo](imagens/Screenshot_18.png)
```
Address: https://api.open-meteo.com/v1/forecast?latitude=${property.latitude}&longitude=${property.longitude}&current_weather=true
Proxy Type: Internet
Method: GET
Authentication: None
```

<br>

### :five: JSON → XML Converter

Como o SAP CPI trabalha melhor com XML em expressões XPath, o JSON retornado pela API é convertido para XML.

Exemplo simplificado do XML gerado:
```
<root>
   <current_weather>
      <temperature>22.5</temperature>
      <weathercode>3</weathercode>
   </current_weather>
</root>
```

<br>

### Adicionando o JSON → XML Converter
![Fluxo](imagens/Screenshot_19.png)

<br>

### Configurando o JSON → XML Converter
![Fluxo](imagens/Screenshot_20.png)
Em Processing
```
Add XML Root Element
Name: root
```

<br>

### :six: Router – Classificação Inteligente do Clima

O Router analisa os dados recebidos e classifica o clima em diferentes cenários com base em:

weathercode

temperatura

Exemplos de regras implementadas:

☀️ Sol agradável weathercode = 0 temperature > 15 temperature <= 25 <br>
🌥 Nublado frio weathercode = 3 temperature <= 15 <br>
🌫 Névoa weathercode = 45 ou 48 <br>
❄ Neve weathercode entre 71 e 86 <br>
🌧 Chuva weathercode entre 51 e 82 <br>

Cada condição também considera faixas de temperatura:

Status	Temperatura <br>
FRIO	≤ 15°C <br>
AGRADÁVEL	15°C – 25°C <br>
QUENTE	≥ 25°C <br>

### Adicionamos o Router
![Fluxo](imagens/Screenshot_14.png)

<br>

### Definindo a Route 1 Default
![Fluxo](imagens/Screenshot_15.png)

<br>

### :seven:  Content Modifier – Status	Erro


Quando houver erro na temperatura, o mesmo vai retornar um XML
### Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_16.png)

<br>

### Configurando o Body 
![Fluxo](imagens/Screenshot_17.png)
```
Type: Constant
```

```
Body:
<Response>
    <message> Erro na temperatura</message>
</Response>
```

<br>

### :eight: Content Modifier

<br>

### Adicionar Content Modifier
![Fluxo](imagens/Screenshot_18.png)

Vamos renomear cada Content Modifiy

<br>

### Configurando o Property Content Modifier
![Fluxo](imagens/Screenshot_19.png)
```
Content Modifier - NevoaAGRADAVEL
Exchange Property
Create - statustemperature - Constant - AGRADAVEL
Create - temperature - XPath - /root/current_weather/temperature - java.lang.String
```

<br>

### Configurando o Body Content Modifier

Cada cenário gera uma mensagem específica para o sistema de logística.

📦 Exemplo de Resposta da Integração

```
<Response>
    <weather>CLOUDY</weather>
    <city>São Paulo</city>
    <message>Céu nublado com temperatura confortável. Clima estável.</message>
    <temperature>22.3</temperature>
    <status>AGRADAVEL</status>
</Response>
```

![Fluxo](imagens/Screenshot_20.png)

```
Message Body
Type: Expression
Body: 
<Response>
    <weather>CLOUDY</weather>
    <city>São Paulo</city>
    <message>Névoa leve com temperatura moderada. Atenção à visibilidade.</message>
    <temperature>${property.temperature}</temperature>
    <status>${property.statustemperature}</status>
</Response>
```
<br>

### Adicionando as Rotas
![Fluxo](imagens/Screenshot_21.png)
Adicionando as rotas quando for FRIO, QUENTE ou AGRADAVEL

```



<br><br>

## 📦 Exemplo prático – iFlow para baixar

📦 [Download do iFlow – Integracao-de-Clima](https://github.com/souzajean/Integracao-de-Clima/raw/main/Package/Weather-Condition-Integration.zip)




> O arquivo pode ser importado diretamente no SAP Integration Suite (CPI).
