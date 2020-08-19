---
title: Получение пар "ключ-значение" на момент времени
titleSuffix: Azure App Configuration
description: Получение старых пар "ключ-значение" с помощью моментальных снимков на момент времени в конфигурации приложения Azure, которая поддерживает запись изменений значений ключа.
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b1d559d82cb22d8a787785c6d8c6a5101d89793a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586636"
---
# <a name="point-in-time-snapshot"></a>Моментальный снимок на определенный момент времени

В конфигурации приложений Azure сохраняется запись об изменениях, внесенных в значения ключа. Эта запись представляет собой временную шкалу изменений "ключ — значение". Можно восстановить историю любого значения ключа и предоставить свое значение в любое время в течение периода истории ключей (7 дней для хранилищ на уровне Free или 30 дней для магазинов уровня "Стандартный"). С помощью этой функции можно «пройти по времени» назад и получить старое значение ключа. Например, можно восстановить параметры конфигурации, используемые до последнего развертывания, чтобы выполнить откат приложения к предыдущей конфигурации.

## <a name="key-value-retrieval"></a>Получение пары "ключ — значение"

Для извлечения прошлых значений ключа можно использовать портал Azure или CLI. В Azure CLI используйте `az appconfig revision list` , добавив соответствующие параметры для получения необходимых значений.  Укажите экземпляр конфигурации приложения Azure, указав имя хранилища ( `--name <app-config-store-name>` ) или с помощью строки подключения ( `--connection-string <your-connection-string>` ). Ограничьте вывод, указав конкретный момент времени ( `--datetime` ) и указав максимальное число возвращаемых элементов ( `--top` ).

Если Azure CLI не установлен локально, можно также использовать Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Извлеките все записанные изменения в значения ключей.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

Получение всех записанных изменений для ключа `environment` и меток `test` и `prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Получение всех записанных изменений в иерархическом пространстве ключа `environment:prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Получение всех записанных изменений для ключа `color` в заданный момент времени.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Извлеките последние 10 записанных изменений в значения ключа и возвратите только значения для `key` `label` метки времени, и `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-приложения ASP.NET Core](./quickstart-aspnet-core-app.md)  
