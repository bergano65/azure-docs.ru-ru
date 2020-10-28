---
author: baanders
description: Включаемый файл для настройки локальной проверки подлинности для DefaultAzureCredential в примерах Azure Digital Twins — с вводными сведениями
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494653"
---
### <a name="set-up-local-azure-credentials"></a>Настройка локальных учетных данных Azure

В этом примере используется [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (часть библиотеки `Azure.Identity`) для аутентификации пользователей с помощью экземпляра Azure Digital Twins, запускаемого на локальном компьютере. Дополнительные сведения о различных способах аутентификации клиентского приложения в Azure Digital Twins см. в [*практическом руководстве по написанию кода аутентификации приложения*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]