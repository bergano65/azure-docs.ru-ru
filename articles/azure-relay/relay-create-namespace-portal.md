---
title: Создание пространства имен ретранслятора с помощью портала Azure | Документация Майкрософт
description: В этой статье представлено пошаговое руководство, в котором показано, как создать пространство имен ретранслятора с помощью портал Azure.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 78ab6753-877a-4426-92ec-a81675d62a57
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: b9811ef92aba4891627c20e0269be136582a3304
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83212000"
---
# <a name="create-a-relay-namespace-using-the-azure-portal"></a>Создание пространства имен ретранслятора с помощью портала Azure

Пространство имен — это контейнер, определяющий область действия для всех компонентов ретранслятора Azure. В одном пространстве имен могут содержаться несколько ретрансляторов. Часто пространства имен используются как контейнеры приложений. Сейчас создать пространство имен ретранслятора можно двумя способами:

1. с помощью портала Azure (описано в этой статье);
2. Шаблоны [Azure Resource Manager](../azure-resource-manager/management/overview.md) .

## <a name="create-a-namespace-in-the-azure-portal"></a>Создание пространства имен на портале Azure

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

Поздравляем! Вы создали пространство имен ретранслятора.

## <a name="next-steps"></a>Дальнейшие действия

* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Начало работы с .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)

