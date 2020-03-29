---
title: Запустите, остановите и удалите приложение Azure Spring Cloud Документы Майкрософт
description: Как запустить, приостановить или удалить приложение Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276839"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Запуск, остановка и удаление приложения Azure Spring Cloud

В этом руководстве объясняется, как изменить состояние приложения в облачности Azure Spring с помощью портала Azure или Azure CLI.

## <a name="using-the-azure-portal"></a>Использование портала Azure

После развертывания приложения можно запустить, остановить и удалить его с помощью портала Azure.

1. Перейдите к экземпляру своей службы Azure Spring Cloud на портале Azure.
1. Выберите вкладку **Панель мониторинга приложений**.
1. Выберите приложение, состояние которого необходимо изменить.
1. На странице **Обзора** для этого приложения выберите **Start/Stop,** **Restart**или **Удалить.**

## <a name="using-the-azure-cli"></a>Использование Azure CLI

> [!NOTE]
> В интерфейсе командной строки Azure можно использовать необязательные параметры и настроить значения по умолчанию. Узнайте больше о Azure CLI, прочитав [нашу справочную документацию.](spring-cloud-cli-reference.md)  

Во-первых, установите расширение Облака Azure Spring для Azure CLI следующим образом:

```azurecli
az extension add --name spring-cloud
```

Далее выберите любую из этих операций Azure CLI:

* Чтобы запустить приложение, выполните следующие действия.

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы остановить приложение, выполните следующие действия.

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
