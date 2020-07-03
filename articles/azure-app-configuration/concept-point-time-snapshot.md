---
title: Получение пар "ключ-значение" на момент времени
titleSuffix: Azure App Configuration
description: Получение старых пар "ключ-значение" с помощью моментальных снимков на момент времени в конфигурации приложения Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523668"
---
# <a name="point-in-time-snapshot"></a>Моментальный снимок на определенный момент времени

В конфигурации приложений Azure сохраняется запись об изменениях в парах "ключ-значение". Эта запись представляет собой временную шкалу изменений "ключ — значение". Вы можете восстановить историю любого значения ключа и предоставить свое значение в любое время в течение предыдущих семи дней. С помощью этой функции можно «пройти по времени» назад и получить старое значение ключа. Например, можно восстановить параметры конфигурации, используемые до последнего развертывания, чтобы выполнить откат приложения к предыдущей конфигурации.

## <a name="key-value-retrieval"></a>Получение пары "ключ — значение"

Можно использовать Azure PowerShell для получения значений прежних ключей.  Используйте `az appconfig revision list`, добавив соответствующие параметры для получения необходимых значений.  Укажите экземпляр конфигурации приложения Azure, указав имя хранилища (`--name {app-config-store-name}`) или с помощью строки подключения (`--connection-string {your-connection-string}`). Ограничьте вывод, указав конкретный момент времени (`--datetime`) и указав максимальное число возвращаемых элементов (`--top`).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Извлеките все записанные изменения в значения ключей.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Получение всех записанных изменений для ключа `environment` и меток `test` и `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Получение всех записанных изменений в иерархическом пространстве `environment:prod`ключа.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Получение всех записанных изменений для ключа `color` в заданный момент времени.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Извлеките последние 10 записанных изменений в значения ключа и возвратите только значения для `key`метки времени `label`, и `last-modified` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Создание веб-приложения ASP.NET Core](./quickstart-aspnet-core-app.md)  
