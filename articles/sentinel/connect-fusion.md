---
title: Включение Fusion в предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как включить функцию Fusion в Azure Sentinel.
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
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780439"
---
# <a name="enable-fusion"></a>Включение Fusion

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Машинное обучение в Azure Sentinel встроена прямо с самого начала. Мы продуманно разрабатываем систему с помощью нововведений в МАШИНном обучении, предназначенных для создания аналитиков безопасности, специалистов по обработке и анализу данных безопасности. Одним из таких нововведений является Fusion-метка Azure, созданная специально для сокращения выносливостиов оповещений.

Fusion использует алгоритмы машинного обучения на основе графов для корреляции между миллионами аномальных действий низкого качества из различных продуктов, таких как защита идентификации Azure AD, и Microsoft Cloud App Security, чтобы объединить их в управляемое число интересные инциденты безопасности.

## <a name="enable-fusion"></a>Включение Fusion

1. В портал Azure щелкните значок, чтобы открыть Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  В диалоговом окне **Добро пожаловать в Cloud Shell** окна ниже выберите PowerShell.

3.  Выберите подписку, в которой вы развернули Azure Sentinel, и **Создайте хранилище**.

4. После проверки подлинности и создания диска Azure в командной строке выполните следующие команды:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Отключить Fusion

Выполните ту же процедуру, что и выше, и выполните следующую команду:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Просмотр состояния Fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Следующие шаги

В этом документе вы узнали, как включить функцию Fusion в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

