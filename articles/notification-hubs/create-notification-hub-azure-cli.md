---
title: Краткое руководство. Создание концентратора уведомлений Azure с помощью Azure CLI | Документация Майкрософт
description: В этом руководстве показано, как создать концентратор уведомлений Azure с помощью Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069487"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Краткое руководство. Создание концентратора уведомлений Azure с помощью Azure CLI

Центры уведомлений Azure обеспечивают простой в использовании и масштабируемый механизм отправки push-уведомлений, который позволяет отправлять уведомления на любую платформу (iOS, Android, Windows, Kindle, Baidu и т. д.) из любой серверной части (облачной или локальной). Дополнительные сведения о службе см. в статье [Что такое Центры уведомлений Azure?](notification-hubs-push-notification-overview.md).

В этом кратком руководстве показано, как создать концентратор уведомлений с помощью Azure CLI. В первом разделе описано, как создать пространство имен для концентратора уведомлений и получить сведения о политике доступа для этого пространства имен. Во втором разделе приведены пошаговые инструкции по созданию концентратора уведомлений в существующем пространстве имен.  Вы также узнаете, как создать пользовательскую политику доступа.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для работы с концентраторами уведомлений требуется Azure CLI версии не ниже 2.0.67. Запустите `az --version`, чтобы узнать установленную версию и зависимые библиотеки. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Подготовка среды

1. Войдите.

   Выполните вход с помощью команды [az login](/cli/azure/reference-index#az-login), если вы используете локальную установку CLI.

    ```azurecli-interactive
    az login
    ```

    Выполните аутентификацию, следуя инструкциям в окне терминала.

2. Установите расширение Azure CLI.

   Чтобы выполнять команды Azure CLI для концентраторов уведомлений, установите [соответствующее расширение Azure CLI](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Создайте группу ресурсов.

   Концентраторы уведомлений Azure, как и все ресурсы Azure, должны быть развернуты в группе ресурсов. Группы ресурсов позволяют организовать соответствующие ресурсы Azure и управлять ими.

   Для этого краткого руководства создайте группу ресурсов с именем *spnhubgr* в регионе *eastus* с помощью следующей команды [az group create](/cli/azure/group#az-group-create):

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Создание пространства имен для концентратора уведомлений

1. Создание пространства имен для концентраторов уведомлений

   Пространство имен включает один или несколько концентраторов, и имя должно быть уникальным во всех подписках Azure.  Чтобы проверить доступность выбранного для службы пространства имен, воспользуйтесь командой [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).  Чтобы создать пространство имен, выполните команду [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create).  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Получите список ключей и строк подключения политики доступа для созданного пространства имен.

   Для нового пространства имен автоматически создается политика доступа с именем **RootManageSharedAccessKey**.  Каждая политика доступа имеет два набора ключей и строк подключения.  Чтобы получить список ключей и строк подключения для пространства имен, выполните команду [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Создание концентраторов уведомлений

1. Создайте первый концентратор уведомлений.

   Теперь вы можете создать концентратор уведомлений в новом пространстве имен.  Чтобы создать концентратор уведомлений, выполните команду [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create).

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Создайте второй концентратор уведомлений.

   Вы можете создать несколько концентраторов уведомлений в одном пространстве имен.  Чтобы создать второй концентратор уведомлений в том же пространстве имен, выполните команду `az notification-hub create` еще раз с другим именем концентратора.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Работа с политиками доступа

1. Создайте новое правило авторизации для концентратора уведомлений.

   Для каждого нового концентратора уведомлений автоматически создается политика доступа.  Чтобы создать и настроить собственную политику доступа, выполните команду [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create).

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Получите список политик доступа для концентратора уведомлений.

   Чтобы запросить список существующих политик доступа для концентратора уведомлений, выполните команду [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list).

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Не используйте в приложении политику **DefaultFullSharedAccessSignature**. Этот параметр можно использовать только в серверной части.  Используйте в клиентском приложении только политики доступа **Listen**.

3. Получение списка ключей и строк подключения для политик доступа концентратора уведомлений.

   Для каждой политики доступа определены два набора ключей и строк подключения.  Они потребуются позже для обработки push-уведомлений.  Чтобы получить список ключей и строк подключения для политики доступа концентратора уведомлений, выполните команду [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Для [пространства имен концентратора уведомлений](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) и самого [концентратора уведомлений](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) действуют разные политики доступа.  Убедитесь, что при запросе ключей и строк подключения используется правильная ссылка на Azure CLI.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>См. также раздел

Изучите возможности по управлению концентраторами уведомлений с помощью Azure CLI.

* [Полный список справочников по Azure CLI для концентраторов уведомлений](/cli/azure/ext/notification-hub/notification-hub)
* [Список справочников по Azure CLI для пространств имен концентраторов уведомлений](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Список справочников по Azure CLI для правил авторизации концентраторов уведомлений](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Список справочников по Azure CLI для учетных данных концентраторов уведомлений](/cli/azure/ext/notification-hub/notification-hub/credential)
