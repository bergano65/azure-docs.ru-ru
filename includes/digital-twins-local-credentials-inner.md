---
author: baanders
description: Включаемый файл для настройки локальной проверки подлинности для DefaultAzureCredential в примерах Azure Digital Twins — без вводных сведений
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494698"
---
При использовании `DefaultAzureCredential` пример будет искать учетные данные в локальной среде, например имя для входа Azure в локальной версии [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) или Visual Studio либо Visual Studio Code. Это означает, что вам нужно **войти в Azure локально** с помощью одного из этих механизмов, чтобы настроить учетные данные для примера.

Если вы используете Visual Studio или Visual Studio Code для запуска примера кода, убедитесь, что вошли в этот редактор с теми же учетными данными Azure, которые хотите использовать для доступа к своему экземпляру Azure Digital Twins.

Если это не так, можно [установить локальную версию **Azure CLI**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), запустить командную строку на компьютере и выполнит команду `az login`, чтобы войти в учетную запись Azure. После этого при выполнении примера кода, вход в систему должен быть выполнен автоматически.