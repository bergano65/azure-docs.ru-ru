---
title: Экспорт данных верхнего уровня из подписки Azure | Документация Майкрософт
description: В этой статье описано, как просмотреть все идентификаторы подписки Azure, связанной с учетной записью.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918807"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Экспорт и просмотр данных верхнего уровня из подписки
Чтобы просмотреть набор идентификаторов подписок, связанных с учетными данными пользователя, [загрузите файл JSON из Центра управления учетной записью Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Загруженный JSON-файл содержит следующие сведения:
- Электронная почта: адрес электронной почты, связанный с учетной записью.
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
