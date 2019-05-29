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
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119302"
---
Ниже приведено максимальное количество для каждого типа объекта для службы "Политика Azure". Запись _Область_ означает либо подписку, либо [группу управления](../articles/governance/management-groups/overview.md).

| Where | Что | Максимальное количество |
|---|---|---|
| Область | Определения политик | 250 |
| Область | Определения инициативы | 100 |
| Клиент | Определения инициативы | 1000 |
| Область | Назначение политик или инициатив | 100 |
| Определение политики | Параметры | 20 |
| Определение инициативы | Политики | 100 |
| Определение инициативы | Параметры | 100 |
| Назначение политик или инициатив | Исключения (не области) | 250 |
| Правило политики | Вложенные условные выражения | 512 |
