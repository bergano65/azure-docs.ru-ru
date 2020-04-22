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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683162"
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
> [!NOTE]
> Стоит также отметить, что некоторые дистрибутивы Linux не имеют переменной среды TMPDIR или TMPDIR. Это приведет к тому, что Speech SDK будет каждый раз загружать список отзыва сертификатов (CRL), а не кэшировать CRL на диск для повторного использования до истечения срока их действия. Для повышения начальной производительности соединения можно [создать переменную среды под названием TMPDIR и установить ее на пути выбранного временного каталога.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Сведения о пакете SDK службы "Речь"](speech-sdk.md)
