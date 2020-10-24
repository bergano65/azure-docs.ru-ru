---
author: baanders
description: включить файл для Дефаултазурекредентиал в примерах цифровых двойников Azure — Примечание
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494496"
---
>[!NOTE]
> В этом примере используется [дефаултазурекредентиал](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (часть `Azure.Identity` библиотеки) для проверки подлинности пользователей с помощью экземпляра Azure Digital двойников при запуске на локальном компьютере. При таком типе проверки подлинности пример будет искать учетные данные Azure в локальной среде, такие как имя входа из локального [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) или в Visual Studio/Visual Studio Code.
>
> Дополнительные сведения об использовании `DefaultAzureCredential` и других вариантах проверки подлинности см. [*в разделе Практические руководства. Написание кода проверки подлинности приложения*](../articles/digital-twins/how-to-authenticate-client.md).