---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 10e7db521781c21b3fe5feda0880fee7e603afab
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128330"
---
Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). По соображениям лицензирования двоичные файлы Гстреамер не компилируются и не связываются с пакетом SDK для распознавания речи. Разработчикам необходимо установить несколько зависимостей и подключаемых модулей.

> [!NOTE]
> См. список [поддерживаемых дистрибутивов Linux и целевых архитектур](~/articles/cognitive-services/speech-service/speech-sdk.md).

# <a name="ubuntudebian"></a>[Ubuntu и Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL или CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - Если вы используете RHEL/CentOS 7, выполните инструкции по [настройке пакета SDK службы "Речь" в RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Если вы используете RHEL/CentOS 8, выполните инструкции по [настройке OpenSSL для Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

> [!IMPORTANT]
> Формат кодирования звука опус не поддерживается пакетом SDK для распознавания речи на RHEL/CentOS 7.

---