---
title: Проверка нового решения Azure Stack | Документация Майкрософт
description: Узнайте, как проверить новое решение Azure Stack с помощью проверки как услуги.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 777609b89bc08cd61489d2c3a3669ec07ccbc372
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647020"
---
# <a name="validate-a-new-azure-stack-solution"></a>Проверка нового решения Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Узнайте, как использовать рабочий процесс **проверки решения** для сертификации новых решений Azure Stack.

Решение Azure Stack является спецификацией оборудования, которая была совместно согласована между корпорацией Майкрософт и партнером и которая соответствует сертификационным требованиям логотипа Windows Server. Решение должно быть повторно сертифицировано при изменении спецификации оборудования. При возникновении дополнительных вопросов о том, когда необходимо повторно сертифицировать решения, свяжитесь с командой по адресу [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

Чтобы сертифицировать решение, необходимо дважды запустить рабочий процесс проверки решений. Запустите его один раз для *минимально* поддерживаемой конфигурации. Запустите его второй раз для *максимально* поддерживаемой конфигурации. Корпорация Майкрософт сертифицирует решение, если обе конфигурации прошли все тесты.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Создание рабочего процесса проверки решений

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Щелкните **Start** (Начать) на плитке **Solution Validations** (Проверка решений).

    ![Плитка рабочего процесса проверки решений](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Выберите **конфигурацию решения**:
    - **Минимум** — для решения, настроенного с минимальным числом поддерживаемых узлов.
    - **Максимум** — для решения, настроенного с максимальным числом поддерживаемых узлов.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Сведения о проверке решения](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Параметры среды невозможно изменить после создания рабочего процесса.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Вы будете перенаправлены на страницу сводок по тестам.

## <a name="execute-solution-validation-tests"></a>Выполнение тестов проверки решений

На странице **сводки по тестам проверки решений** вы увидите список тестов, необходимых для завершения проверки.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Выполнение теста проверки решений](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)