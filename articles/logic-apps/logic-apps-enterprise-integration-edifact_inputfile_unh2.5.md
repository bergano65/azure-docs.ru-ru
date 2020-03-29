---
title: UNH 2.5 сегментов в сообщениях EDIFACT
description: Разрешить сообщения EDIFACT с сегментами UNH2.5 в приложениях логики Azure с пакетом корпоративной интеграции
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792543"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Обработка документов EDIFACT с помощью сегментов UNH 2.5 в Azure Logic Apps

Если в документе EDIFACT существует сегмент UNH2.5, сегмент используется для поиска схемы. Например, в этом примере сообщения EDIFACT `EAN008`поле UNH:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Чтобы справиться с этим сообщением, выполните следующие действия, описанные ниже:

1. Обновление схемы.

1. Проверьте настройки соглашения.

## <a name="update-the-schema"></a>Обновление схемы

Для обработки сообщения необходимо развернуть схему с именем корневого узла UNH2.5. Например, название корня схемы для поля `EFACT_D03B_ORDERS_EAN008`UNH является . Для `D03B_ORDERS` каждого сегмента UNH2.5 необходимо развернуть индивидуальную схему.

## <a name="add-schema-to-edifact-agreement"></a>Добавление схемы к соглашению EDIFACT

### <a name="edifact-decode"></a>Декодирование EDIFACT

Для декодирования входящего сообщения установите схему в настройках получения соглашения EDIFACT:

1. На [портале Azure](https://portal.azure.com)откройте учетную запись интеграции.

1. Добавьте схему в учетную запись интеграции.

1. Настроили схему в настройках получения соглашения EDIFACT.

1. Выберите соглашение EDIFACT и **отберите Edit как JSON.** Добавьте значение UNH2.5 в `schemaReferences` раздел Соглашения о получении:

   ![Добавить UNH2.5 для получения соглашения](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Кодирование EDIFACT

Чтобы закодировать входящие сообщения, настроите схему в соглашении EDIFACT отправить настройки

1. На [портале Azure](https://portal.azure.com)откройте учетную запись интеграции.

1. Добавьте схему в учетную запись интеграции.

1. Настроили схему в настройках отправки соглашения EDIFACT.

1. Выберите соглашение EDIFACT и щелкните **Редактирование в качестве JSON**.  Добавьте значение UNH2.5 в **schemaReferences** для соглашения отправки.

1. Выберите соглашение EDIFACT и **отберите Edit как JSON.** Добавьте значение UNH2.5 в `schemaReferences` раздел Соглашения о отправке:

   ![Добавить UNH2.5 для отправки соглашения](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о соглашениях об [интеграционных счетах](../logic-apps/logic-apps-enterprise-integration-agreements.md)