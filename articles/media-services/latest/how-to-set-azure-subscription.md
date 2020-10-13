---
title: Как найти подписку Azure
description: Найдите подписку Azure, чтобы можно было настроить среду.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal
ms.openlocfilehash: 8924b77cddc9efc4d2b1b8451df38de77b37c09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267366"
---
# <a name="find-your-azure-subscription"></a>Поиск подписки Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Портал](#tab/portal/)

## <a name="use-the-azure-portal"></a>Использование портала Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. В разделе "службы Azure" выберите подписки. (Если в списке нет подписок, может потребоваться переключить клиенты Azure AD.) Идентификаторы подписки перечислены во втором столбце.
1. Скопируйте идентификатор подписки и вставьте его в текстовый документ по своему усмотрению для последующего использования.

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Использование командной строки Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>Перечисление подписок Azure с помощью интерфейса командной строки

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>См. также

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)

---

## <a name="next-steps"></a>Дальнейшие действия

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
