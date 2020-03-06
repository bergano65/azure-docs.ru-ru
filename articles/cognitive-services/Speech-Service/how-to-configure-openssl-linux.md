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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331150"
---
# <a name="configure-openssl-for-linux"></a>Настройка OpenSSL для Linux

При использовании любой версии пакета SDK для распознавания речи перед 1.9.0, [OpenSSL](https://www.openssl.org) динамически настраивается в качестве версии системы узла. В более поздних версиях пакета SDK OpenSSL (версия [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) статически связывается с основной библиотекой пакета SDK для распознавания речи.

Чтобы обеспечить подключение, убедитесь, что сертификаты OpenSSL установлены в системе. Выполните команду:
```bash
openssl version -d
```

Выходные данные в системах на базе Ubuntu/Debian должны быть:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Проверьте наличие `certs` подкаталога в разделе ОПЕНССЛДИР. В приведенном выше примере это будет `/usr/lib/ssl/certs`.

* Если имеется `/usr/lib/ssl/certs` и он содержит много отдельных файлов сертификатов (с `.crt` или расширением `.pem`), дальнейшие действия не требуются.

* Если ОПЕНССЛДИР — что-то другое, чем `/usr/lib/ssl` и/или имеется отдельный файл пакета сертификатов, а не несколько отдельных файлов, необходимо задать соответствующую переменную среды SSL, чтобы указать, где можно найти сертификаты.

## <a name="examples"></a>Примеры

- ОПЕНССЛДИР — `/opt/ssl`. Существует `certs` подкаталог с множеством `.crt` или `.pem` файлов.
Перед запуском программы, использующей пакет SDK для распознавания речи, в качестве значения переменной среды `SSL_CERT_DIR` указывать на `/opt/ssl/certs`. Пример:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- ОПЕНССЛДИР — это `/etc/pki/tls` (например, в системах на базе RHEL и CentOS). Существует `certs` подкаталог с файлом пакета сертификатов, например `ca-bundle.crt`.
Перед запуском программы, использующей пакет SDK для распознавания речи, задайте для переменной среды `SSL_CERT_FILE`, чтобы она указывала на этот файл. Пример:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [О пакете SDK для распознавания речи](speech-sdk.md)
