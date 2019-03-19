---
title: Сбор данных Cloud App Security в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных Cloud App Security в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245494"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Сбор данных из Microsoft Cloud App Security 

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно осуществлять потоковую передачу журналов из [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) в Azure Sentinel одним щелчком. Это подключение позволяет потоковую передачу оповещений из Cloud App Security в Azure Sentinel. 

## <a name="prerequisites"></a>Технические условия

- Пользователь с глобальным администратором или разрешения администратора безопасности

## <a name="connect-to-cloud-app-security"></a>Подключение к Cloud App Security

Если у вас уже есть Cloud App Security, убедитесь, что это [включена в сети](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Если Cloud App Security развертывается и приема данных, данные оповещения можно легко настроить потоковую передачу в Azure Sentinel.


1. В Azure Sentinel, выберите **сбора данных** и нажмите кнопку **Cloud App Security** плитку.

2. Щелкните **Подключить**.

3. Чтобы использовать соответствующей схемы в Log Analytics для оповещений Cloud App Security, поиск **SecurityAlert**.


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel Microsoft Cloud App Security. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
