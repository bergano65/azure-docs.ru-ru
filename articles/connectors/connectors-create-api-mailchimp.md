---
title: Подключение к MailChimp с помощью Azure Logic Apps | Документация Майкрософт
description: Создавайте маркетинговые сообщения и управляйте ими с помощью интерфейсов REST API MailChimp и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ffe2760c004db24d503ee381b8752c875c286fca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105842"
---
# <a name="get-started-with-the-mailchimp-connector"></a>Начало работы с соединителем MailChimp
MailChimp — это служба SaaS, которая позволяет компаниям управлять маркетинговыми мероприятиями по электронной почте, включая отправку маркетинговых сообщений электронной почты, автоматических сообщений и целевых кампаний, и автоматизировать их.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-mailchimp"></a>Создание подключения к MailChimp
Для создания приложений логики с помощью MailChimp необходимо создать **подключение**, а затем указать данные для следующих свойств.

| Свойство | Обязательно для заполнения | Описание |
| --- | --- | --- |
| Токен |Да |Укажите учетные данные MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/mailchimp/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).