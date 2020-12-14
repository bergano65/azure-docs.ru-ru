---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356285"
---
В этих примерах показано, как с помощью Azure Monitor создавать оповещения для подписок, подключенных для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Запрашивает последний день активности в управляющем клиенте и [сообщает о любых добавленных или удаленных делегированиях](../articles/lighthouse/how-to/monitor-delegation-changes.md) (или попытках, которые не были успешными).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Этот шаблон отвечает за создание оповещения Azure и подключение к существующей группе действий.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Создание нескольких оповещений журнала на основе запросов Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Развертывание оповещения в клиенте при делегировании пользователем подписки на управляющий клиент.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Отображение журналов действий Azure в подписках с возможностью фильтрации по доменному имени. |
