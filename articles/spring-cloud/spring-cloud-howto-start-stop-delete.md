---
title: Как запускать, прекращать и удалять облачное приложение Azure весны | Документация Майкрософт
description: Как запускать, прекращать и удалять облачное приложение Azure весны
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607722"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Как запускать, прекращать и удалять облачное приложение Azure весны

В этом руководство объясняется, как изменить состояние приложения в Azure "Весна Cloud" с помощью портал Azure или CLI.

## <a name="using-the-azure-portal"></a>Использование портала Azure

После развертывания приложения его можно **запускать**, **прекращать**и **удалять** с помощью портал Azure.

1. Перейдите к экземпляру облачной службы Azure весны в портал Azure.
1. Перейдите на вкладку **панель мониторинга приложения** .
1. Выберите приложение, состояние которого необходимо изменить.
2. На странице **Обзор** для этого приложения найдите кнопки для запуска, **завершения**, **перезапуска**и **удаления** приложения.

## <a name="using-the-azure-cli"></a>Использование Azure CLI

> [!NOTE]
> Можно использовать необязательные параметры и настроить значения по умолчанию с помощью Azure CLI. Дополнительные сведения см. в [справочной документации](spring-cloud-cli-reference.md).  

Установите расширение "Весна Cloud" для Azure CLI:

```azurecli
az extension add --name spring-cloud
```

* Чтобы запустить приложение, выполните следующие действия.
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы прерывать работу приложения:
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы перезапустить приложение, выполните следующие действия.
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы удалить приложение, выполните следующие действия.
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
