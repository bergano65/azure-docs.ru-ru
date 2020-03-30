---
title: Извлекать пары ключей-значения из точки в времени
titleSuffix: Azure App Configuration
description: Извлекать старые пары значений ключей с помощью моментальных снимков времени в конфигурации приложений Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523668"
---
# <a name="point-in-time-snapshot"></a>Моментальный снимок на определенный момент времени

Конфигурация приложений Azure ведет учет изменений, внесенных в пары ключей. Эта запись предоставляет хронологию изменений ключевой стоимости. Вы можете реконструировать историю любого ключевого значения и предоставить его прошлое значение в любой момент в течение предыдущих семи дней. Используя эту функцию, вы можете "путешествовать во времени" назад и получить старую ключевую ценность. Например, можно восстановить параметры конфигурации, используемые перед последним развертыванием, чтобы откатить приложение к предыдущей конфигурации.

## <a name="key-value-retrieval"></a>Получение пары "ключ — значение"

Для получения прошлых ключевых значений можно использовать Azure PowerShell.  Используйте, `az appconfig revision list`добавляя соответствующие параметры для получения требуемых значений.  Укажите экземпляр конфигурации приложения Azure,`--name {app-config-store-name}`предоставив либо название`--connection-string {your-connection-string}`магазина () или с помощью строки подключения (). Ограничьте выход, указав конкретный`--datetime`момент времени () и указав максимальное количество элементов для возврата ().`--top`

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Извлеките все записанные изменения в ключевые значения.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Извлекать все записанные `environment` изменения для `test` `prod`ключа и меток и .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Извлеките все записанные изменения `environment:prod`в иерархическом ключевом пространстве.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Извлекать все записанные `color` изменения для ключа в определенный момент времени.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Извлеките последние 10 записанных изменений в ключевые `key`значения `label`и `last-modified` верните только значения для, и отметки времени.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание веб-приложения ASP.NET Core](./quickstart-aspnet-core-app.md)  
