---
title: Краткое руководство. Создание кластера и базы данных Azure Data Explorer с помощью CLI
description: Из этого краткого руководства вы узнаете, как создать кластер и базу данных Azure Data Explorer с помощью Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881122"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Создание кластера и базы данных Azure Data Explorer с помощью CLI

В этом кратком руководстве описано создание кластера и базы данных Azure Data Explorer с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии. Выполните команду `az --version`, чтобы узнать номер версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Настройка параметров CLI

Следующие действия не требуются, если вы выполняете команды в Cloud Shell. Если вы используете CLI локально, выполните следующие действия, чтобы войти в учетную запись Azure и выбрать текущую подписку:

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurecli-interactive
    az login
    ```

2. Укажите подписку, в которой необходимо создать кластер. Замените `MyAzureSub` идентификатором именем подписки Azure, которую вы хотите использовать:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

1. Создайте кластер, используя приведенную ниже команду.

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | name | *azureclitest* | Необходимое имя кластера.|
   | sku | *D13_v2* | Номер SKU, который будет использоваться для кластера. |
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |

    Можно использовать дополнительные необязательные параметры, например емкость кластера и т. д.

2. Чтобы проверить, успешно ли создан кластер, можно выполнить следующую команду:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Если результат содержит параметр provisioningState со значением Succeeded, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

1. Создайте базу данных, используя приведенную ниже команду.

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | cluster-name | *azureclitest* | Имя кластера, в котором необходимо создать базу данных.|
   | name | *clidatabase* | Необходимое имя базы данных.|
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | soft-delete-period | *3650:00:00:00* | Время, на протяжении которого данные должны храниться таким образом, чтобы они были доступны для запроса. |
   | hot-cache-period | *3650:00:00:00* | Время, на протяжении которого данные должны храниться в кэше. |

2. Выполните следующую команду, чтобы просмотреть созданную базу данных:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

* Если вы планируете работать с другими нашими краткими и подробными руководствами, сохраните созданные ресурсы.
* Чтобы очистить ресурсы, удалите кластер. При удалении кластера также удаляются все содержащиеся в нем базы данных. Чтобы удалить кластер, используйте следующую команду:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство Прием данных с помощью библиотеки Python в Azure Data Explorer](python-ingest-data.md)
