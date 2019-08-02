---
title: включение файла
description: включение файла
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640449"
---
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

* **Windows**

    * [Python 2. x или 3. x](https://www.python.org/downloads/). Обязательно используйте 32-разрядную или 64-разрядную версию установки согласно требованиям программы настройки. При появлении запроса во время установки обязательно добавьте Python в переменную среды соответствующей платформы. Если вы используете Python 2.x, может потребоваться [установка или обновление *pip* — системы управления пакетами Python](https://pip.pypa.io/en/stable/installing/).

    * Если вы используете ОС Windows, убедитесь, что у вас есть правильная версия [распространяемого C++ пакета визуального](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) элемента, чтобы разрешить использование собственных библиотек DLL из Python. Рекомендуется использовать последнюю версию.

    * При необходимости установите пакет [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) с помощью команды.`pip install azure-iothub-device-client`

    * При необходимости установите пакет [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) с помощью команды.`pip install azure-iothub-service-client`

* **Mac OS**

    Для Mac OS требуется Python 3.7.0 (или 2,7) + либбуст-1,67 + 7.61.1 (все установленные через Homebrew). Любые другие дистрибутивы и ОС, вероятно, внедряют разные версии не&ных зависимостей, которые не будут работать, и приведут к Импортеррор во время выполнения.

    Сейчас пакеты *pip* для `azure-iothub-service-client` и `azure-iothub-device-client` доступны только для ОС Windows. Сведения для Linux и Mac OS см. в разделах, посвященных Linux и Mac OS, в разделе [Подготовка среды разработки для Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) POST.

> [!NOTE]
> При импорте iothub_client в образце произошли несколько отчетов об ошибках. Дополнительные сведения о работе с **импортеррор** проблемами см. в разделе [Работа с проблемами импортеррор](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
