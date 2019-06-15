---
title: Проверка XML для интеграции с предприятием B2B в Azure Logic Apps | Документация Майкрософт
description: Проверка XML с помощью схем для решений B2B в Azure Logic Apps и Пакета интеграции Enterprise
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996342"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Проверка XML для интеграции с предприятием B2B в Azure Logic Apps с помощью Пакета интеграции Enterprise

Часто в сценариях B2B партнерам, участвующим в соглашении, необходимо убедиться в допустимости сообщений, которыми они обмениваются. И это нужно сделать до начала обработки данных. В пакете интеграции Enterprise можно использовать соединитель проверки XML, чтобы проверять документы на соответствие предопределенной схеме.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Проверка документа с помощью соединителя проверки XML

1. Создайте приложение логики и [свяжите его с учетной записью интеграции](../logic-apps/logic-apps-enterprise-integration-accounts.md "Дополнительные сведения о связывании учетной записи интеграции с приложением логики"), которая содержит схему для проверки данных XML.

2. Добавьте триггер **Request - When an HTTP request is received** (Запрос: при получении HTTP-запроса) в свое приложение логики.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Добавьте действие **Проверка XML**, щелкнув **Добавить действие**.

4. Введите *xml* в поле поиска, чтобы отфильтровать все действия и оставить только необходимые. Выберите действие **Проверка XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Чтобы указать содержимое XML, которое будет проверено, выберите **Содержимое**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Выберите тег body в качестве содержимого, которое будет проверено.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Чтобы определить схему для проверки ввода предыдущего *содержимого*, выберите **Имя схемы**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Сохраните результаты своих действий.  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Вы закончили работу с настройкой соединителя проверки. В реальном приложении вам нужно будет передать проверенные данные в бизнес-приложение (например, SalesForce). Чтобы отправить проверенные выходные данные в Salesforce, добавьте действие.

Теперь можно протестировать действие проверки, выполнив запрос к конечной точке HTTP.

## <a name="next-steps"></a>Дальнейшие действия
[Узнайте больше о пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise.")   

