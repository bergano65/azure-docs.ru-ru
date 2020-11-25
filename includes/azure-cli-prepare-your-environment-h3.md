---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000064"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Подготовка среды к работе с Azure CLI

- [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) можно использовать в среде Bash.

   [![Внедрение запуска](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)   
- При необходимости [установите](/cli/azure/install-azure-cli) Azure CLI, чтобы выполнять справочные команды CLI.
   - Если вы используете локальную установку, выполните вход с помощью команды Azure CLI [az login](/cli/azure/reference-index#az-login).  Чтобы выполнить аутентификацию, следуйте инструкциям в окне терминала.  Сведения о дополнительных возможностях входа см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).
  - Если появится запрос, установите расширения Azure CLI при первом использовании.  Дополнительные сведения о расширениях см. в статье [Использование расширений с Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Выполните команду [az version](/cli/azure/reference-index?#az_version), чтобы узнать установленную версию и зависимые библиотеки. Чтобы обновиться до последней версии, выполните команду [az upgrade](/cli/azure/reference-index?#az_upgrade).