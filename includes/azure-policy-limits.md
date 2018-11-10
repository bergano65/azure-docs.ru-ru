---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964644"
---
Ниже приведено максимальное количество для каждого типа объекта для службы "Политика Azure". Запись _Область_ означает либо подписку, либо [группу управления](../articles/governance/management-groups/overview.md).

| Where | Что | Максимальное количество |
|---|---|---|
| Область | Определения политик | 250 |
| Область | Определения инициатив | 100 |
| Клиент | Определения инициатив | 1000 |
| Область | Назначения инициатив или политик | 100 |
| Определение политики | Параметры | 20 |
| Определение инициативы | Политики | 100 |
| Определение инициативы | Параметры | 100 |
| Назначения инициатив или политик | Исключения (не области) | 250 |
| Правило политики | Вложенные условные выражения | 512 |
