---
title: Пример скрипта Azure CLI для создания учетной записи пакетной службы в режиме подписки пользователя
description: Этот скрипт создает учетную запись пакетной службы Azure в режиме подписки пользователя. Эта учетная запись выделяет вычислительные узлы в подписке.
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: e589361da8442107f06a0933a1f1ac79a88945ff
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964083"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Пример использования CLI: создание учетной записи пакетной службы в режиме подписки пользователя

Этот скрипт создает учетную запись пакетной службы Azure в режиме подписки пользователя. Учетная запись, которая выделяет вычислительные узлы для подписки, должна пройти проверку подлинности с помощью маркера Azure Active Directory. Число выделенных вычислительных узлов учитывается в квоте выделенных виртуальных процессоров (ядер) для вашей подписки. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполните следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az role assignment create](/cli/azure/role); | Создайте новое назначение роли для пользователя, группы или субъекта-службы. |
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Создает хранилище ключей. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Обновляет политику безопасности указанного хранилища ключей. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Создает учетную запись пакетной службы.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Выполняет проверку подлинности с помощью указанной учетной записи пакетной службы для дальнейшего взаимодействия с интерфейсом командной строки.  |
| [az group delete](/cli/azure/group#az-group-delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
