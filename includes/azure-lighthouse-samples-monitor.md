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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421137"
---
В этих примерах показано, как с помощью Azure Monitor создавать оповещения для подписок, подключенных для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Запрашивает последний день активности в управляющем клиенте и [сообщает о любых добавленных или удаленных делегированиях](../articles/lighthouse/how-to/monitor-delegation-changes.md) (или попытках, которые не были успешными).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Этот шаблон отвечает за создание оповещения Azure и подключение к существующей группе действий.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Создание нескольких оповещений журнала на основе запросов Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Развертывание оповещения в клиенте при делегировании пользователем подписки на управляющий клиент.|
