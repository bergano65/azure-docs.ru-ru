---
title: Создание кластера и базы данных Azure Data Explorer с помощью CLI
description: В этой статье описывается создание кластера и базы данных Azure Data Explorer с помощью Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812690"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Создание кластера и базы данных Azure Data Explorer с помощью CLI

В этой статье описывается создание кластера и базы данных Azure Data Explorer с помощью Azure CLI.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Отличия между программными интерфейсами плоскости управления и плоскости данных

Существуют две разные библиотеки API (API плоскости управления и API плоскости данных).
API управления используются для управления ресурсами, например, для создания кластера или базы данных, удаления подключения к данным, изменения количества экземпляров и т. д. API плоскости данных используются для взаимодействия с данными, выполнения запросов, приема данных и т. д.

## <a name="configure-the-cli-parameters"></a>Настройка параметров CLI

Вход в учетную запись

```Bash
az login
```

Укажите подписку, в которой необходимо создать кластер.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

Создайте кластер, используя приведенную ниже команду.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Укажите следующие значения.

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | name | *azureclitest* | Необходимое имя кластера.|
   | sku | *D13_v2* | Номер SKU, который будет использоваться для кластера. |
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |

При необходимости можно использовать дополнительные необязательные параметры, например емкость кластера и т. д.

Чтобы проверить, успешно ли создан кластер, можно запустить следующую команду.

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Если результат содержит параметр provisioningState со значением Succeeded, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

Создайте базу данных, используя приведенную ниже команду.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Укажите следующие значения.

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | cluster-name | *azureclitest* | Имя кластера, в котором необходимо создать базу данных.|
   | name | *clidatabase* | Необходимое имя базы данных.|
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | soft-delete-period | *3650:00:00:00* | Время, на протяжении которого данные должны храниться таким образом, чтобы они были доступны для запроса. |
   | hot-cache-period | *3650:00:00:00* | Время, на протяжении которого данные должны храниться в кэше. |

Вы можете увидеть созданную базу данных, выполнив следующую команду.

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими нашими краткими и подробными руководствами, сохраните созданные ресурсы.

При удалении кластера также удаляются все содержащиеся в нем базы данных. Чтобы удалить кластер, используйте приведенную ниже команду.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство Прием данных из концентратора событий в Azure Data Explorer](ingest-data-event-hub.md)
