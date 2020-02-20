---
title: Экспорт данных верхнего уровня из подписки Azure | Документация Майкрософт
description: В этой статье описано, как просмотреть все идентификаторы подписки Azure, связанной с учетной записью.
keywords: ''
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 83b6a9db0107cb35448aec491c81c2630e87ddd7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199727"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Экспорт и просмотр данных верхнего уровня из подписки
Чтобы просмотреть набор идентификаторов подписок, связанных с учетными данными пользователя, [загрузите файл JSON из Центра управления учетной записью Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Загруженный JSON-файл содержит следующие сведения:
- Адрес электронной почты: адрес электронной почты, связанный с учетной записью.
- Puid: глобальный уникальный идентификатор, связанный с учетной записью выставления счетов;
- SubscriptionIds: список подписок в учетной записи, перечисленных за идентификаторами.

### <a name="subscriptionsjson-sample"></a>Пример файла subscriptions.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
