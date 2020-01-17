---
title: Настройка OpenSSL для Linux
titleSuffix: Azure Cognitive Services
description: Узнайте, как настроить OpenSSL для Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156494"
---
# <a name="configure-openssl-for-linux"></a>Настройка OpenSSL для Linux

При использовании любой версии пакета SDK для распознавания речи перед 1.9.0, [OpenSSL](https://www.openssl.org) динамически настраивается в качестве версии системы узла. В более поздних версиях пакета SDK OpenSSL (версия [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) статически связывается с основной библиотекой пакета SDK для распознавания речи.

## <a name="troubleshoot-connectivity"></a>Устранение неполадок с подключением

Если при использовании 1.9.0 выпуска пакета SDK для распознавания речи возникают ошибки подключения, убедитесь, что каталог `ssl/certs` существует в `/usr/lib` Directory, который находится в файловой системе Linux. Если каталог `ssl/certs` *не существует*, проверьте, установлен ли в системе параметр OpenSSL, используя следующую команду:

```bash
which openssl
```

Затем найдите каталог OpenSSL `certs` и скопируйте содержимое этого каталога в каталог `/usr/lib/ssl/certs`. Затем повторите попытку, чтобы узнать, были ли устранены проблемы с подключением.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [О пакете SDK для распознавания речи](speech-sdk.md)