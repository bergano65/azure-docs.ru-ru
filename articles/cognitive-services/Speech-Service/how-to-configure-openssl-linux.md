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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331150"
---
# <a name="configure-openssl-for-linux"></a>Настройка OpenSSL для Linux

При использовании любой версии Speech SDK до 1.9.0 [OpenSSL](https://www.openssl.org) динамически настроен на версию хост-системы. В более поздних версиях речи SDK OpenSSL (версия [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)статически связан атак с основной библиотекой речи SDK.

Чтобы обеспечить подключение, убедитесь, что сертификаты OpenSSL были установлены в вашей системе. Выполнить команду:
```bash
openssl version -d
```

Выход на ubuntu / Debian систем должны быть:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Проверьте, `certs` есть ли субдиректория в рамках OPENSSLDIR. В приведенном выше примере, было бы `/usr/lib/ssl/certs`.

* Если есть `/usr/lib/ssl/certs` и он содержит много `.crt` отдельных файлов сертификата (с или `.pem` расширение), нет необходимости в дальнейших действиях.

* Если OPENSSLDIR является `/usr/lib/ssl` чем-то другим, чем и/или есть один файл пакета сертификатов вместо нескольких отдельных файлов, необходимо установить соответствующую переменную среды SSL, чтобы указать, где можно найти сертификаты.

## <a name="examples"></a>Примеры

- OPENSSLDIR `/opt/ssl`. Существует `certs` субдиректория `.crt` со `.pem` многими или файлами.
Установите `SSL_CERT_DIR` переменную `/opt/ssl/certs` среды, чтобы указать на перед запуском программы, которая использует speech SDK. Пример:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` (как на системах на основе RHEL/CentOS). Существует `certs` субдиректория с файлом пакета `ca-bundle.crt`сертификата, например.
Установите `SSL_CERT_FILE` переменную среды, чтобы указать на этот файл перед запуском программы, использующей Speech SDK. Пример:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения о пакете SDK службы "Речь"](speech-sdk.md)
