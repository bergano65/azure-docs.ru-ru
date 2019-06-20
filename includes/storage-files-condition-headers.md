---
title: включение файла
description: включение файла
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185355"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Ошибка ConditionHeadersNotSupported из веб-приложения, использующих службу файлов Azure из браузера

Если доступ к содержимому, размещенных в службе файлов Azure через приложение, которое делает использование условных заголовков, таких как веб-браузер, доступа завершается ошибкой, отображает ошибку ConditionHeadersNotSupported.

![Ошибка ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Причина:

Условные заголовки еще не поддерживаются. Приложения, их реализации требуется запросить полный файл каждый раз, чтобы получить доступ к файлу.

### <a name="workaround"></a>Возможное решение

При отправке нового файла, свойство управления кэшем по умолчанию равно «no-cache». Чтобы принудительно применить для запроса в файл каждый раз, его управления кэшем свойство должно быть обновлен с «no-cache» до «no-cache, no-store, must-revalidate». Это можно сделать с помощью [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).

![Изменение содержимого кэша обозревателя хранилища](media/storage-files-condition-headers/storage-explorer-cache.png)