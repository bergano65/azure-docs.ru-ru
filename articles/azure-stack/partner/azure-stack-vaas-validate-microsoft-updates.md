---
title: Проверка обновлений программного обеспечения корпорации Майкрософт с помощью проверки как услуги Azure Stack | Документация Майкрософт
description: Узнайте, как проверять обновления программного обеспечения корпорации Майкрософт с помощью проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: bcb56789b2781cd1d081af8c112222a1f1269a74
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304896"
---
# <a name="validate-software-updates-from-microsoft"></a>Проверка обновлений программного обеспечения от корпорации Майкрософт

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Корпорация Майкрософт периодически выпускает обновления для программного обеспечения Azure Stack. Эти обновления предоставляются партнерам по совместной разработке Azure Stack. Обновления предоставляются до того, как они станут общедоступными. Вы можете проверить наличие обновлений для своего решения и отправить отзыв корпорации Майкрософт.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Применение ежемесячного обновления

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Создание рабочего процесса

Для проверки обновления используется тот же рабочий процесс, что и для **проверки решений**.

## <a name="run-tests"></a>Выполнение тестов

1. Для проверки обновления используется тот же рабочий процесс, что и для **проверки решений**. 

2. Следуйте инструкциям из раздела [о выполнении тестов для проверки решения](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Выберите следующие тесты:
    - Monthly Azure Stack Update Verification;
    - Cloud Simulation Engine.

Для проверок обновления не нужно отправлять запрос на подписывание пакета.

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)