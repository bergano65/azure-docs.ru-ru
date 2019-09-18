---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67133510"
---
Ниже приведено максимальное количество для каждого типа объекта для службы "Политика Azure". Запись _Область_ означает либо подписку, либо [группу управления](../articles/governance/management-groups/overview.md).

| Where | Что | Максимальное количество |
|---|---|---|
| Область | Определения политик | 500 |
| Область | Определения инициативы | 100 |
| Клиент | Определения инициативы | 1000 |
| Область | Назначение политик или инициатив | 100 |
| Определение политики | Параметры | 20 |
| Определение инициативы | Политики | 100 |
| Определение инициативы | Параметры | 100 |
| Назначение политик или инициатив | Исключения (не области) | 400 |
| Правило политики | Вложенные условные выражения | 512 |
