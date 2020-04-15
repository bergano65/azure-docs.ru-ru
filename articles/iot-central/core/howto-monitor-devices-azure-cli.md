---
title: Мониторинг подключения устройств с помощью обозревателя Azure IoT Central
description: Отслеживайте сообщения с устройств и изменения двойников устройств с помощью интерфейса командной строки обозревателя IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 0fa1b58547e0260ec5486003cf4cb85d855de33a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310230"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Мониторинг подключения устройств с помощью Azure CLI

*Этот раздел предназначен для разработчиков и администраторов.*

Используйте расширение Azure CLI IoT, чтобы видеть сообщения, которые ваши устройства отправляют в IoT Central, и наблюдать изменения в близнеце устройства. Этот инструмент можно использовать для отладки и наблюдения за подключением устройств и диагностики проблем сообщений устройств, не достигающих облака или устройств, не реагирующих на двойные изменения.

[Для получения более подробной информации посетите ссылку на расширения Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Предварительные требования

+ Azure CLI установлен и является версией 2.0.7 или выше. Проверьте версию вашего Azure `az --version`CLI, запустив. Узнайте, как установить и обновить с [документов Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Рабочая или школьная учетная запись в Azure, добавленная в качестве пользователя в приложении IoT Central.

## <a name="install-the-iot-central-extension"></a>Установка центрального расширения IoT

Чтобы установить средство, в командной строке выполните следующую команду:

```azurecli
az extension add --name azure-iot
```

Проверьте версию расширения, запустив:

```azurecli
az --version
```

Вы должны увидеть расширение лазурное йота 0.8.1 или выше. Если это не так, запустите:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Использование расширения

В следующих разделах описаны общие команды и `az iot central`параметры, которые можно использовать при запуске. Чтобы просмотреть полный набор команд `--help` и `az iot central` опций, перейдите на или любой из его подкоманд.

### <a name="login"></a>Имя входа

Начните с вхена в Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Получите идентификатор приложения вашего приложения IoT Central
В **настройках администрирования/приложения**скопируйте **идентификатор приложения.** Это значение используется в последующих шагах.

### <a name="monitor-messages"></a>Мониторинг сообщений
Мониторинг сообщений, отправляемых в приложение IoT Central с ваших устройств. Выход включает в себя все заголовки и аннотации.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Просмотр свойств устройства
Просмотр текущих свойств устройства чтения и чтения/записи для данного устройства.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как использовать IoT Central Explorer, следующим шагом является [изучение управления устройствами IoT Central.](howto-manage-devices.md)
