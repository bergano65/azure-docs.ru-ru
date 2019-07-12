---
title: Включение fusion в предварительной версии Sentinel Azure | Документация Майкрософт
description: Сведения о включении fusion в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 1e17afb2f06265975442c127baecc8cab75d63f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611261"
---
# <a name="enable-fusion"></a>Включение Fusion

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Машинное обучение в Azure Sentinel встроена с самого начала. Система продуманным разработаны с инновации машинного Обучения, направленные чтобы эффективность аналитиков по вопросам безопасности, обработке и анализу данных безопасности и инженеров. Один из таких инноваций — Azure Sentinel Fusion предусматривает особенно уменьшить усталости оповещения.

Fusion использует graph на основе алгоритмов машинного обучения для корреляции между миллионами нижней fidelity аномальных действий из различных продуктов, таких как защита идентификации Azure AD и Microsoft Cloud App Security, их объединить на удобное количество интересными случаями безопасности.

## <a name="enable-fusion"></a>Включение Fusion

1. На портале Azure выберите значок, чтобы открыть Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  В **Добро пожаловать в Cloud Shell** окон, которые открывает ниже, выберите PowerShell.

3.  Выберите подписку, в которой развернут Azure Sentinel, и **создать хранилище**.

4. После того как вы прошли проверку подлинности и построен на диск Azure, в командной строке, выполните следующие команды:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Отключить fusion

Выполните ту же процедуру, как описано выше и выполните следующую команду:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Просмотр состояния fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Следующие шаги

В этом документе вы узнали, как включить Fusion в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

