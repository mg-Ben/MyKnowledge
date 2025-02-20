---
tags:
  - Networks
  - Protocols
  - Application_Layer
---
# Core principles
_SMTP stands for Simple Mail Transfer Protocol_
## SMTP Server
Receives emails and forwards them to the corresponding recipient. It is the analogous to the old post boxes.
For example, in many applications, you can specify to send your mails to:
- The public Outlook SMTP Server (`smtp-mail.outlook.com:587`, see [Configuración POP, IMAP y SMTP para Outlook.com - Soporte técnico de Microsoft](https://support.microsoft.com/es-es/office/configuraci%C3%B3n-pop-imap-y-smtp-para-outlook-com-d088b986-291d-42b8-9564-9c414e2aa040))
- The Google SMTP server (`smtp.gmail.com:587`, see [Enviar correo electrónico desde impresoras, escáneres o aplicaciones - Ayuda de Administrador de Google Workspace](https://support.google.com/a/answer/176600?hl=es))
And those servers will forward notifications to the corresponding recipient.
Depending on the target SMTP Server, you might need to authenticate.

> For example, for Google (Gmail) SMTP Server, you will need to specify two parameters: _username_ and _password_. The _username_ is the gmail account from which you want to send the notification. The _password_ must be an **Application Password** if you are connecting to the SMTP Server from an Application (such as [[Prometheus#Alertmanager]])