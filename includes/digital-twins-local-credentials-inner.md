---
author: baanders
description: Включаемый файл для настройки локальной проверки подлинности для DefaultAzureCredential в примерах Azure Digital Twins — без вводных сведений
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 3cb053f9673532ac19e2098678ec341f2f676486
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329488"
---
При использовании `DefaultAzureCredential` пример будет искать учетные данные в локальной среде, например имя для входа Azure в локальной версии [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), в Visual Studio либо Visual Studio Code. Поэтому вам нужно *войти в Azure локально* с помощью одного из этих механизмов, чтобы настроить учетные данные для примера.

Если вы используете для запуска примера кода Visual Studio или Visual Studio Code, убедитесь, что вошли в этот редактор с теми же учетными данными Azure, которые хотите использовать для доступа к своему экземпляру Azure Digital Twins.

Если это не так, можно [установить локальную версию Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), запустить на компьютере командную строку и выполнить команду `az login`, чтобы войти в учетную запись Azure. После этого при запуске примера кода вход в систему должен выполняться автоматически.