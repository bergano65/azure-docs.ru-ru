---
title: включить файл
description: включить файл
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737497"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Ошибка ConditionHeadersNotПоддерживается из веб-приложения с помощью файлов Azure из браузера

Ошибка ConditionHeadersNotSupported возникает при доступе к содержимому, размещенном в Azure Files через приложение, в котором используются условные заголовки, такие как веб-браузер, доступ к сбоям. Ошибка гласит, что заголовки условий не поддерживаются.

![Ошибка условных заголовков файлов Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Причина

Условные заголовки пока не поддерживаются. Приложения, реализующие их, должны будут запрашивать полный файл каждый раз, когда файл будет доступен.

### <a name="workaround"></a>Обходной путь

При загрузке нового файла свойство управления кэшем по умолчанию является "некэшным". Чтобы заставить приложение запрашивать файл каждый раз, свойство управления кэшом файла должно быть обновлено с "без кэша" на "не-кэш, не-магазин, обязательно-повторное". Это может быть достигнуто с помощью [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

![Модификация кэша содержимого хранилища для условных заголовков Azure Files](media/storage-files-condition-headers/storage-explorer-cache.png)