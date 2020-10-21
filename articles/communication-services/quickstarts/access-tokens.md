---
title: Краткое руководство. Создание маркеров доступа и управление ими
titleSuffix: An Azure Communication Services quickstart
description: Узнайте, как управлять удостоверениями и маркерами доступа с помощью клиентской библиотеки администрирования Служб коммуникации Azure.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074130"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Краткое руководство. Создание маркеров доступа и управление ими

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Начало работы со Службами коммуникации Azure с помощью клиентской библиотеки администрирования Служб коммуникации для подготовки маркеров доступа и управления ими. Маркеры доступа позволяют выполнять проверку подлинности в клиентских библиотеках для чатов и вызовов непосредственно в Службах коммуникации Azure. Эти токены создаются в реализованной вами службе подготовки маркеров на стороне сервера. Затем они используются для инициализации клиентских библиотек Служб коммуникации на клиентских устройствах.

Обратите внимание, что все цены, указанные на изображениях в этом учебнике, приведены только для примера.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

В выходных данных приложения описывают каждое выполненное действие.
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку на Службы коммуникации, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней. См. сведения об [очистке ресурсов](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Next Steps

В этом кратком руководстве рассматривались следующие темы:

> [!div class="checklist"]
> * Управление идентификаторами
> * Выпуск маркеров доступа
> * Использование клиентской библиотеки администрирования Служб коммуникации Azure


> [!div class="nextstepaction"]
> [Добавление функции голосового вызова в приложение](./voice-video-calling/getting-started-with-calling.md)

Полезные ссылки

 - [Сведения о проверке подлинности](../concepts/authentication.md)
 - [Добавление чата в приложение](./chat/get-started.md)
 - [Сведения об архитектуре клиент-сервер](../concepts/client-and-server-architecture.md)
