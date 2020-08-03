---
title: Язык запросов
titleSuffix: Azure Digital Twins
description: Ознакомьтесь с основами языка хранилища запросов Azure Digital двойников.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 960fff073353375cd50b31bc7284134ca733f142
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488029"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>О языке запросов для Azure Digital двойников

Вспомним, что Центр цифрового двойников Azure — это [**двойника граф**](concepts-twins-graph.md), созданный на основе **цифровых двойников** и **отношений**. Этот граф можно запросить для получения сведений о цифровых двойников и отношениях, которые она содержит. Эти запросы написаны на пользовательском языке запросов, похожем на SQL, который называется **языком хранилища запросов Azure Digital двойников**.

Чтобы отправить запрос в службу из клиентского приложения, вы будете использовать [**API запросов**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)цифровых двойников Azure. Это позволяет разработчикам создавать запросы и применять фильтры для поиска наборов цифровых двойников в графе двойника, а также другую информацию о сценарии цифрового двойников Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать запросы и просматривать примеры кода клиента в разделе "инструкции. Создание [*запросов к графу двойника"*](how-to-query-graph.md).
