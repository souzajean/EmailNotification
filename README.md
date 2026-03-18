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

## 📊 Benefícios

- Redução do tempo de resposta a falhas
- Melhor visibilidade operacional
- Padronização de notificações
- Apoio ao suporte e operação

---


---


# :building_construction: Arquitetura do iFlow

### :one: O fluxo foi desenvolvido no SAP Cloud Integration (CPI) seguindo as etapas abaixo.

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

### :two:  Manage Security

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

### :three:  Configuração Google Gmail
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

### :four:  HTTPS Sender

<br>

### Adicionando o HTTPS
![Fluxo](imagens/Screenshot_12.png)

<br>

### Configurando o Endpoint
O fluxo é iniciado através de um endpoint HTTPS, permitindo que aplicações externas consultem o serviço.

![Fluxo](imagens/Screenshot_13.png)

```
/NotificationEmail
```
<br>

### :five: Content Modifier – Definição  Prepare Email Payload

Nesta etapa são definidas as configurações que vamos usar para o Pauload.


### Adicionando o Content Modifier
![Fluxo](imagens/Screenshot_14.png)

<br>

### Renomeando o Content Modifier
Renomeamos o Content Modifier 
![Fluxo](imagens/Screenshot_15.png)
```
Prepare Email Payload
```
<br>

### Configurando o Content Modifier - Header
![Fluxo](imagens/Screenshot_16.png)

Em Header adicionamos
```
Message Header
create   -   CPI_Tenant   -    Expression   -    ${header.CamelHttpUrl}          - java.lang.String
```
<br>

### Configurando o Content Modifier - Property
![Fluxo](imagens/Screenshot_17.png)

Em Property adicionamos
```
Exchange Property
create   -   Iflow_Name   -    Constant     -    NotificationEmail
create   -   Date_Now     -    Expression   -    ${date:now:yyyy-MM-dd HH:mm:ss}    - java.lang.String
```
<br>

### :six: End – Receiver

Nesta etapa, vamos utilizar o adapter de Email para que possamos realizar as conexões e configurações no adapter, para recebermos o e-mail da forma que queremos.

O retorno é recebido no formato HTML.

### Adicionamos o Adapter Mail
![Fluxo](imagens/Screenshot_18.png)

<br>

### Configuração do Mail - Connection
![Fluxo](imagens/Screenshot_19.png)
```
Address: smtp.gmail.com
Protection: SMTPS
Authentication: Plain User/Password
```
<br>

### Configuração do Mail - Processing
Vamos marcar Body Mime Type: Text/HTML
![Fluxo](imagens/Screenshot_20.png)

Subject:
```
CPI Monitoramento - ${header.Iflow_Name} - ${property.Date_Now}
```
<br>

<br>

Mail Body:
```
<!DOCTYPE html>
<html>
<body style="margin:0; padding:0; background-color:#f5f6f7; font-family: Arial, sans-serif;">

<table width="100%" cellpadding="0" cellspacing="0" style="background-color:#f5f6f7;">
  <tr>
    <td align="center">

      <!-- CONTAINER -->
      <table width="600" cellpadding="0" cellspacing="0" style="background-color:#ffffff; border:1px solid #d9d9d9;">

        <!-- HEADER FIORI -->
        <tr>
          <td style="background-color:#0a6ed1; color:#ffffff; padding:20px; font-size:20px; font-weight:bold;">
            SAP CPI - Monitoramento
          </td>
        </tr>

        <!-- STATUS -->
        <tr>
          <td style="padding:20px;">
            <span style="background-color:#bb0000; color:#ffffff; padding:6px 12px; font-size:12px; border-radius:4px;">
              ERRO
            </span>
          </td>
        </tr>

        <!-- CONTEÚDO -->
        <tr>
          <td style="padding:0 20px 20px 20px; color:#32363a;">

            <h2 style="margin:0 0 10px 0; font-size:20px; color:#32363a;">
              Falha no iFlow
            </h2>

            <p style="font-size:14px; line-height:20px;">
              Olá,<br><br>
              Ocorreu um erro no fluxo de integração do SAP CPI. Veja os detalhes abaixo:
            </p>

            <!-- CARD ESTILO FIORI -->
            <table width="100%" cellpadding="10" cellspacing="0" style="border:1px solid #e5e5e5; border-radius:4px;">
              
              <tr style="background-color:#fafafa;">
                <td width="40%"><b>CPI Tenant</b></td>
                <td>${header.CPI_Tenant}</td>
              </tr>

              <tr>
                <td><b>Name Iflow</b></td>
                <td>${header.Iflow_Name}</td>
              </tr>

              <tr style="background-color:#fafafa;">
                <td><b>Date Time</b></td>
                <td>${property.Date_Now}</td>
              </tr>

              <tr>
  		<td><b>Erro</b></td>
  		<td style="color:#bb0000;">
  		Falha detectada durante a execução do fluxo de integração.<br/>
  		Acesse o SAP CPI em <b>Monitoramento → Message Monitoring</b> para análise detalhada e reprocessamento.
		</td>
	      </tr>

            </table>

            <p style="margin-top:20px; font-size:14px;">
              Verifique o monitoramento do CPI ou acione o time responsável.
            </p>

          </td>
        </tr>

        <!-- FOOTER -->
        <tr>
          <td style="background-color:#f5f6f7; padding:15px; font-size:12px; color:#6a6d70; text-align:center;">
            SAP Integration Suite © Empresa
          </td>
        </tr>

      </table>

    </td>
  </tr>
</table>

</body>
</html>
```

<br>
<br>

### :seven: Postman
### Configuração do Postman
![Fluxo](imagens/Screenshot_21.png)

<br>

### :eight: E-MAIL GMAIL
### Email recebido do CPI
Email recebido do CPI
![Fluxo](imagens/Screenshot_22.png)



<br><br>

## 📦 Exemplo prático – iFlow para baixar

📦 [Download do iFlow – Integracao-de-Clima](https://github.com/souzajean/Integracao-de-Clima/raw/main/Package/Weather-Condition-Integration.zip)




> O arquivo pode ser importado diretamente no SAP Integration Suite (CPI).
