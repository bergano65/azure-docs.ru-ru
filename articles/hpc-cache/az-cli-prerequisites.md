---
title: Azure CLI необходимых компонентов для кэша HPC Azure
description: Шаги настройки перед использованием Azure CLI для создания или изменения кэша Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100334"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Настройка Azure CLI для кэша HPC Azure

Выполните следующие действия, чтобы подготовить среду перед использованием Azure CLI для создания кэша Azure HPC или управления им.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Установка Azure CLI

Для кэша HPC Azure требуется версия 2,7 или более поздняя Azure CLI. Запустите `az --version`, чтобы узнать установленную версию и зависимые библиотеки. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>Установка расширения кэша Azure HPC

Функции кэша HPC Azure не являются частью основной базы кода, поэтому также необходимо установить ссылку на расширение. Выполните приведенные далее инструкции.

1. Вход

   Выполните вход с помощью команды [AZ login](/cli/azure/reference-index#az-login) , если вы используете локальную версию интерфейса командной строки.

    ```azurecli
    az login
    ```

    Выполните аутентификацию, следуя инструкциям в окне терминала.

   > [!TIP]
   > Если у вас несколько подписок, необходимо выбрать одну из них. Выберите его при запуске Cloud Shell сеанса на портале Azure или следуйте инструкциям в статье [Приступая к работе с Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in) , чтобы настроить подписку из командной строки.

2. Установка расширения Azure CLI

   Функции кэширования Azure HPC предоставляются в виде расширения Azure CLI — они еще не являются частью основного пакета интерфейса командной строки. Необходимо установить ссылку на расширение, прежде чем ее можно будет использовать.

   Расширения Azure CLI предоставляют доступ к экспериментальным и предварительным командам. Дополнительные сведения о расширениях, включая обновление и удаление, см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).

   Установите расширение для кэша HPC Azure, выполнив следующую команду:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Задать группу ресурсов по умолчанию (необязательно)

Большая часть команд для работы с кэшем HPC требует передачи группы ресурсов кэша. Группу ресурсов по умолчанию можно задать с помощью команды [AZ configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Дальнейшие действия

После установки расширения Azure CLI и входа в систему можно использовать Azure CLI для создания систем кэша Azure HPC и управления ими.

* [Создание Azure HPC Cache](hpc-cache-create.md)
* [Azure CLI документация по кэшу HPC](/cli/azure/ext/hpc-cache/hpc-cache)
