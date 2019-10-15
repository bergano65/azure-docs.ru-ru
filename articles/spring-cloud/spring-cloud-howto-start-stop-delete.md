---
title: Как запустить, приостановить или удалить приложение Azure Spring Cloud | Документация Майкрософт
description: Как запустить, приостановить или удалить приложение Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038443"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Как запустить, приостановить или удалить приложение Azure Spring Cloud

В этом руководстве объясняется, как изменить состояние приложения в Azure Spring Cloud с помощью портала Azure или интерфейса командной строки.

## <a name="using-the-azure-portal"></a>Использование портала Azure

После развертывания приложения, его можно **Start** (Запустить), **Stop** (Остановить) и **Delete** (Удалить) с помощью портала Azure.

1. Перейдите к экземпляру своей службы Azure Spring Cloud на портале Azure.
1. Выберите вкладку **Панель мониторинга приложений**.
1. Выберите приложение, состояние которого необходимо изменить.
2. На странице **Overview** для этого приложения найдите кнопки **Start/Stop** (Запустить/остановить), **Restart** (Перезапустить) и **Delete** (Удалить) в приложении.

## <a name="using-the-azure-cli"></a>Использование Azure CLI

> [!NOTE]
> В интерфейсе командной строки Azure можно использовать необязательные параметры и настроить значения по умолчанию. Дополнительные сведения см. в нашей [справочной документации](spring-cloud-cli-reference.md).

* Чтобы запустить приложение, выполните следующие действия.
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы остановить приложение, выполните следующие действия.
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы перезапустить приложение, выполните следующие действия.
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Чтобы удалить приложение, выполните следующие действия.
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
