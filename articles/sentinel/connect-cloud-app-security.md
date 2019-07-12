---
title: Подключайте данные из Cloud App Security предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данных Cloud App Security.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 4e75c9003103e267e864a98c7ee5c1bef2176bae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612409"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Подключайте данные из Microsoft Cloud App Security 

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Можно осуществлять потоковую передачу журналов из [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) в Azure Sentinel одним щелчком. Это подключение позволяет потоковую передачу оповещений из Cloud App Security в Azure Sentinel. 

## <a name="prerequisites"></a>предварительные требования

- Пользователь с глобальным администратором или разрешения администратора безопасности

## <a name="connect-to-cloud-app-security"></a>Подключение к Cloud App Security

Если у вас уже есть Cloud App Security, убедитесь, что это [включена в сети](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Если Cloud App Security развертывается и приема данных, данные оповещения можно легко настроить потоковую передачу в Azure Sentinel.


1. В Azure Sentinel, выберите **соединители данных** и нажмите кнопку **Cloud App Security** плитку.

2. Щелкните **Подключить**.

3. Чтобы использовать соответствующей схемы в Log Analytics для оповещений Cloud App Security, поиск **SecurityAlert**.


## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключиться к Azure Sentinel Microsoft Cloud App Security. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
