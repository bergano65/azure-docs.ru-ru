---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986755"
---
В этих примерах показано, как с помощью Azure Monitor создавать оповещения для подписок, подключенных для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Запрашивает последний день активности в управляющем клиенте и [сообщает о любых добавленных или удаленных делегированиях](../articles/lighthouse/how-to/monitor-delegation-changes.md) (или попытках, которые не были успешными).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Этот шаблон отвечает за создание оповещения Azure и подключение к существующей группе действий.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Создание нескольких оповещений журнала на основе запросов Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Развертывание оповещения в клиенте при делегировании пользователем подписки на управляющий клиент.|
