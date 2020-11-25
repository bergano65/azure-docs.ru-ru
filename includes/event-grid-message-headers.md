---
title: включить файл
description: включить файл
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005642"
---
## <a name="message-headers"></a>Заголовки сообщений
Ниже приведены свойства, которые вы получаете в заголовках сообщений:

| Имя свойства | Описание |
| ------------- | ----------- | 
| aeg-subscription-name | Имя подписки на события. |
| aeg-delivery-count | Число попыток, выполненных для события. |
| aeg-event-type | <p>Тип события.</p><p>Может иметь одно из следующих значений.</p><ul><li>субскриптионвалидатион</li><li>Уведомление</li><li>субскриптионделетион</li></ul> | 
| aeg-metadata-version | <p>Версия метаданных события.<p> Для **схемы событий Сетки событий** это свойство представляет версию метаданных, а для **схемы событий облака **—** версию спецификации**. </p>|
| aeg-data-version | <p>Версия данных события.</p><p>Для **схемы событий Сетки событий** это свойство представляет версию данных, а для **схемы событий облака** оно не используется.</p> |
| aeg-output-event-id | Идентификатор события Сетки событий. |


