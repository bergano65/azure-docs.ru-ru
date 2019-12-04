---
title: Сегменты UNH 2,5 в сообщениях EDIFACT
description: Разрешение сообщений EDIFACT с сегментами UNH 2.5 в Azure Logic Apps с Пакет интеграции Enterprise
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792543"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Обработка документов EDIFACT с помощью сегментов UNH 2.5 в Azure Logic Apps

Если в документе EDIFACT существует сегмент UNH 2.5, для поиска в схеме используется сегмент. Например, в этом образце сообщения EDIFACT поле UNH имеет `EAN008`:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Для обработки этого сообщения выполните следующие действия, описанные ниже.

1. Обновите схему.

1. Проверьте параметры соглашения.

## <a name="update-the-schema"></a>Обновление схемы

Чтобы обработать сообщение, необходимо развернуть схему с именем корневого узла UNH 2.5. Например, корневым именем схемы для поля Sample UNH является `EFACT_D03B_ORDERS_EAN008`. Для каждой `D03B_ORDERS`, имеющей другой сегмент UNH 2.5, необходимо развернуть отдельную схему.

## <a name="add-schema-to-edifact-agreement"></a>Добавить схему в соглашение EDIFACT

### <a name="edifact-decode"></a>Декодирование EDIFACT

Чтобы декодировать входящее сообщение, настройте схему в параметрах получения соглашения EDIFACT:

1. В [портал Azure](https://portal.azure.com)Откройте учетную запись интеграции.

1. Добавьте схему в учетную запись интеграции.

1. Настройте схему в параметрах получения соглашения EDIFACT.

1. Выберите соглашение EDIFACT и выберите **изменить как JSON**. Добавьте значение UNH 2.5 в раздел `schemaReferences` соглашения об получении:

   ![Добавление UNH 2.5 к соглашению о получении](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Кодирование EDIFACT

Чтобы закодировать входящее сообщение, настройте схему в параметрах отправки соглашения EDIFACT.

1. В [портал Azure](https://portal.azure.com)Откройте учетную запись интеграции.

1. Добавьте схему в учетную запись интеграции.

1. Настройте схему в параметрах отправки в соглашении EDIFACT.

1. Выберите соглашение EDIFACT и щелкните **Редактирование в качестве JSON**.  Добавьте значение UNH 2.5 в соглашение Send **schemareferences для**

1. Выберите соглашение EDIFACT и выберите **изменить как JSON**. Добавьте значение UNH 2.5 в раздел `schemaReferences` "Отправка соглашения":

   ![Добавление UNH 2.5 в соглашение об отправке](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [соглашениях для учетных записей интеграции](../logic-apps/logic-apps-enterprise-integration-agreements.md)