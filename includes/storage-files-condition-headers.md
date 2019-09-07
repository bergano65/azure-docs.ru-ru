---
title: включение файла
description: включение файла
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737497"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Ошибка Кондитионхеадерснотсуппортед из веб-приложения, использующего службы файлов Azure, из браузера

Ошибка Кондитионхеадерснотсуппортед возникает при доступе к содержимому, размещенному в службе файлов Azure, через приложение, которое использует условные заголовки, такие как веб-браузер, и происходит сбой доступа. В состоянии ошибки заголовки условий не поддерживаются.

![Ошибка условных заголовков службы файлов Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Причина:

Условные заголовки пока не поддерживаются. Приложения, реализующие их, должны запрашивать полный файл при каждом обращении к файлу.

### <a name="workaround"></a>Возможное решение

При передаче нового файла свойство Cache-Control по умолчанию имеет значение No-cache. Чтобы заставить приложение запрашивать файл каждый раз, необходимо обновить свойство Cache-Control с "No-cache" на "No-cache", "без кэша", "не требуется-повторно проверить". Это можно сделать с помощью [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).

![Изменение кэша содержимого обозревателя хранилищ для условных заголовков службы файлов Azure](media/storage-files-condition-headers/storage-explorer-cache.png)