---
title: Часто задаваемые вопросы Центра безопасности Azure — вопросы о существующих агентах по анализу журналов
description: Эти часто задаваемые вопросы отвечают на вопросы клиентов, уже использующих агент Log Analytics и рассматривающих Azure Security Center, продукт, который помогает предотвращать, обнаруживать и реагировать на угрозы.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436164"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Часто задаваемые вопросы для клиентов, уже использующих журналы Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Переопределяет ли центр безопасности все установленные подключения между виртуальными машинами и рабочими областями?

Если vM уже установил агент log Analytics в качестве расширения Azure, Центр безопасности не отменяет существующее рабочее пространство. Он использует существующую рабочую область. VM будет защищен при условии, что на рабочем пространстве, которому оно сообщает, будет установлено решение "Security" или "SecurityCenterFree". 

Решение Центра безопасности устанавливается на выбранное на экране сбора данных рабочее пространство, если оно уже не присутствует, а решение применяется только к соответствующим ВМ. Когда вы добавляете решение, оно по умолчанию автоматически развертывается на всех агентах Windows и Linux, подключенных к рабочей области Log Analytics. Функция [нацеливания решений](../operations-management-suite/operations-management-suite-solution-targeting.md) позволяет применять к решениям область действия.

> [!TIP]
> Если агент Log Analytics установлен непосредственно на VM (не в качестве расширения Azure), Центр безопасности не устанавливает агент Log Analytics, а мониторинг безопасности ограничен.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Устанавливает ли центр безопасности решения в моих существующих рабочих областях Log Analytics? Как это влияет на выставление счетов?
Центр безопасности определяет, что виртуальная машина уже подключена к рабочей области, которую вы создали. Затем центр безопасности включает решения в этой рабочей области в соответствии с ценовой категорией. Решения применяются только к релевантным виртуальным машинам Azure с помощью функции [нацеливания решений](../operations-management-suite/operations-management-suite-solution-targeting.md). Поэтому счета выставляются без изменений.

- **Уровень "Бесплатный"**  — центр безопасности устанавливает в рабочей области решение SecurityCenterFree. Вам не будет выставлен счет за свободный уровень.
- **Уровень "Стандартный"** — центр безопасности устанавливает в рабочей области решение Security.

   ![Решения в рабочей области по умолчанию](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>В моей среде уже есть рабочие области. Можно ли использовать их для сбора данных безопасности?
Если vM уже установил агент log Analytics в качестве расширения Azure, Центр безопасности использует существующее подключенное рабочее пространство. Центр безопасности устанавливается в рабочей области (если он еще не установлен), и решение применяется только к релевантным виртуальным машинам при помощи функции [нацеливания решений](../operations-management-suite/operations-management-suite-solution-targeting.md).

Когда Центр безопасности устанавливает агент Log Analytics на vMs, он использует рабочее пространство по умолчанию (ы), созданное Центром безопасности.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>В моих рабочих областях уже есть решение для обеспечения безопасности. Как это влияет на выставление счетов?
Решение Security & Audit используется для включения функций стандартного уровня Центра безопасности для MMs Azure. Если решение "Безопасность и аудит" уже установлено в рабочей области, центр безопасности использует существующее решение. Счета будут выставляться без изменений.