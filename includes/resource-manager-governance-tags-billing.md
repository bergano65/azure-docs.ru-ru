---
title: включить файл
description: включить файл
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901176"
---
После применения тегов к ресурсам можно просмотреть затраты на ресурсы с этими тегами. Однако сведения о последнем использовании отобразятся в анализе затрат только через некоторое время. Если сведения о затратах на ресурсы доступны, их можно просмотреть в группах ресурсов в подписке. Чтобы пользователи увидели сведения о затратах, им необходимы права [доступа на уровне подписки к сведениям о выставлении счетов](../articles/cost-management-billing/manage/manage-billing-access.md).

Чтобы просмотреть сведения о затратах по тегам на портале, выберите подписку и щелкните **Анализ затрат**.

![Анализ затрат](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Затем отфильтруйте результаты по значению тега и нажмите кнопку **Применить**.

![Просмотр данных о затратах по тегу](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Изучите также статью [Общие сведения об API потребления ресурсов Azure](../articles/cost-management-billing/manage/consumption-api-overview.md), чтобы узнать о том, как получить сведения о затратах с помощью программных средств.
