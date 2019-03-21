---
title: Сбор данных Azure Information Protection в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных Azure Information Protection в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c5866d3096823f91a70b28c7c5dd1790e1b3bf8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537173"
---
# <a name="collect-data-from-azure-information-protection"></a>Сбор данных из Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно осуществлять потоковую передачу журналов из [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) в Azure Sentinel одним щелчком. Azure Information Protection помогает защитить данные ли он хранится в облаке или в локальными инфраструктурами и управления и справки для защиты электронной почты, документов и конфиденциальных данных, которые вы предоставляете общий доступ за пределами организации. Простая классификация и встроенные метки и разрешения улучшить защиту данных все время с помощью Azure Information Protection. При подключении Azure Information Protection Sentinel Azure, вы потока все оповещения из Azure Information Protection в Azure Sentinel.


## <a name="prerequisites"></a>Технические условия

- Пользователь с глобального администратора, администратора безопасности или разрешения на защиту сведения


## <a name="connect-to-azure-information-protection"></a>Подключение к Azure Information Protection

Если у вас уже есть Azure Information Protection, убедитесь, что это [включена в сети](https://docs.microsoft.com/azure/information-protection/activate-service).
Если развертывается Azure Information Protection и получение данных, данные оповещения можно легко настроить потоковую передачу в Azure Sentinel.


1. В Azure Sentinel, выберите **сбора данных** и нажмите кнопку **Azure Information Protection** плитку.

2. Перейдите к [портала Azure Information Protection](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade) 

3. В разделе **подключения**, настройте потоковую передачу журналов из Azure Information Protection для Azure Sentinel, щелкнув [настройки аналитики](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade)

4. Выберите рабочую область, в которую вы развернули Azure Sentinel. 

5. Последовательно выберите **ОК**.

6. Чтобы использовать соответствующей схемы в Log Analytics для оповещений Azure Information Protection, поиск **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как соединиться с Azure Information Protection Azure Sentinel. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
