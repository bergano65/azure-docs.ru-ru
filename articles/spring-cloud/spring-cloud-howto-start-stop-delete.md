---
title: Запуск, завершение и удаление облачного приложения Azure весны | Документация Майкрософт
description: Как запустить, приостановить или удалить приложение Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276839"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Запуск, завершение и удаление облачного приложения Azure весны

В этом руководство объясняется, как изменить состояние приложения в Azure Веснного облака с помощью портал Azure или Azure CLI.

## <a name="using-the-azure-portal"></a>Использование портала Azure

После развертывания приложения его можно запускать, прекращать и удалять с помощью портал Azure.

1. Перейдите к экземпляру своей службы Azure Spring Cloud на портале Azure.
1. Выберите вкладку **Панель мониторинга приложений**.
1. Выберите приложение, состояние которого необходимо изменить.
1. На странице **Обзор** для этого приложения выберите **Пуск/закрыть**, **перезапустить**или **Удалить**.

## <a name="using-the-azure-cli"></a>Использование Azure CLI

> [!NOTE]
> В интерфейсе командной строки Azure можно использовать необязательные параметры и настроить значения по умолчанию. Дополнительные сведения о Azure CLI см. в [справочной документации](spring-cloud-cli-reference.md).  

Сначала установите облачное расширение Azure "Весна" для Azure CLI следующим образом:

```azurecli
az extension add --name spring-cloud
```

Затем выберите любую из следующих Azure CLI операций:

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
