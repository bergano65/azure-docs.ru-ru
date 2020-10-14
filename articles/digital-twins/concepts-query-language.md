---
title: Язык запросов
titleSuffix: Azure Digital Twins
description: Основные сведения о языке запросов Azure Digital двойников.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d656f19f6f4030025ff1393c3e5017466b3333fd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044400"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>О языке запросов для Azure Digital двойников

Вспомним, что Центр цифрового двойников Azure — это [**двойника граф**](concepts-twins-graph.md), созданный на основе **цифровых двойников** и **отношений**. Этот граф можно запросить для получения сведений о цифровых двойников и отношениях, которые она содержит. Эти запросы написаны на пользовательском языке запросов, похожем на SQL, который называется **языком запросов Azure Digital двойников**.

Чтобы отправить запрос в службу из клиентского приложения, вы будете использовать [**API запросов**](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)цифровых двойников Azure. Это позволяет разработчикам создавать запросы и применять фильтры для поиска наборов цифровых двойников в графе двойника, а также другую информацию о сценарии цифрового двойников Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать запросы и просматривать примеры кода клиента в разделе "инструкции. Создание [*запросов к графу двойника"*](how-to-query-graph.md).