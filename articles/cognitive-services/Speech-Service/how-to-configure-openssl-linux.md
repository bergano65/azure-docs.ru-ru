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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683162"
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

Проверьте наличие `certs` подкаталога в разделе опенсслдир. В приведенном выше примере это будет `/usr/lib/ssl/certs` .

* Если есть `/usr/lib/ssl/certs` и он содержит много отдельных файлов сертификатов (с `.crt` или `.pem` расширением), дальнейшие действия не требуются.

* Если ОПЕНССЛДИР — это что-то еще `/usr/lib/ssl` и/или имеется отдельный файл пакета сертификатов, а не несколько отдельных файлов, необходимо задать соответствующую переменную среды SSL, чтобы указать, где можно найти сертификаты.

## <a name="examples"></a>Примеры

- ОПЕНССЛДИР — `/opt/ssl` . Существует `certs` подкаталог с множеством `.crt` `.pem` файлов или.
Задайте переменную среды `SSL_CERT_DIR` для указания `/opt/ssl/certs` перед запуском программы, использующей РЕЧЕВОЙ пакет SDK. Пример:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- ОПЕНССЛДИР `/etc/pki/tls` (как и в системах на базе RHEL/CentOS). `certs`Например, существует подкаталог с файлом пакета сертификатов `ca-bundle.crt` .
Установите переменную среды, `SSL_CERT_FILE` чтобы она указывала на этот файл, перед запуском программы, использующей речевой пакет SDK. Пример:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Также следует отметить, что в некоторых дистрибутивах Linux не определена переменная среды TMP или TMPDIR. Это приведет к тому, что речевой пакет SDK скачивает список отзыва сертификатов (CRL) каждый раз, а не кэширует его на диск для повторного использования до истечения срока действия. Чтобы улучшить начальную производительность подключения, можно [создать переменную среды с именем TMPDIR и задать путь к выбранному временному каталогу.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о пакете SDK службы "Речь"](speech-sdk.md)
