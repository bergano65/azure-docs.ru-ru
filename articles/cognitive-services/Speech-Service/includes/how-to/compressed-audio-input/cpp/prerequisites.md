---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: b38857cd5ea12767f872690dfcdcb84dcbbb3f7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83585030"
---
Обработка сжатого аудио-сигнала реализуется с помощью [гстреамер](https://gstreamer.freedesktop.org). По соображениям лицензирования двоичные файлы Гстреамер не компилируются и не связываются с пакетом SDK для распознавания речи. Разработчикам необходимо установить несколько зависимостей и подключаемых модулей.

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu, 16,04, 18,04 или Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos-78"></a>[RHEL/CentOS 7/8](#tab/centos)

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