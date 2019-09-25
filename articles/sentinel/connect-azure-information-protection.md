---
title: Подключение Azure Information Protection данных к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Azure Information Protection в Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: cabailey
ms.openlocfilehash: a2760b53dbb9776501cb5e58c681045743471166
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261822"
---
# <a name="connect-data-from-azure-information-protection"></a>Подключение данных из Azure Information Protection

> [!IMPORTANT]
> Соединитель данных Azure Information Protection в Azure Sentinel в настоящее время находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете выполнить потоковую передачу данных журнала из [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) в Azure Sentinel, настроив соединитель Azure Information Protection данных. Azure Information Protection помогает контролировать и защищать конфиденциальные данные независимо от того, хранятся ли они в облаке или в локальной среде.

Если служба [Central Reporting для Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) уже настроена таким образом, чтобы данные журнала из этой службы хранились в той же log Analytics рабочей области, что и в настоящее время выбрано для Azure Sentinel, можно пропустить настройку Этот соединитель данных. Данные журнала из Azure Information Protection уже доступны для Azure Sentinel.

Однако если данные журнала из Azure Information Protection будут находиться в другой рабочей области Log Analytics, отличной от выбранной для Azure Sentinel, выполните одно из следующих действий.

- Измените рабочую область, выбранную в Azure Sentinel.

- Измените рабочую область для Azure Information Protection, что можно сделать, настроив этот соединитель данных.
    
    При изменении рабочей области новые данные отчетов для Azure Information Protection будут храниться в рабочей области, используемой для Sentinel Azure, а исторические данные недоступны для Azure Sentinel. Кроме того, если предыдущая рабочая область настроена для пользовательских запросов, предупреждений или интерфейсов API, их необходимо перенастроить для рабочей области "Sentinel" Azure, если вы хотите использовать их для Azure Information Protection. Для клиентов и служб, использующих Azure Information Protection, перенастройка не требуется.

## <a name="prerequisites"></a>Предварительные требования

- Одна из следующих ролей администратора Azure AD для клиента: 
    - Администратор Azure Information Protection
    - Администратор безопасности
    - Администратор соответствия требованиям
    - Администратор данных соответствия требованиям
    - Глобальный администратор
    
    > [!NOTE]
    > Нельзя использовать роль администратора Azure Information Protection, если клиент находится на [единой платформе меток](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
    
    Эти роли администратора необходимы только для настройки соединителя Azure Information Protection и не требуются при подключении Sentinel Azure к Azure Information Protection.

- Разрешения на чтение и запись в Log Analytics рабочей области, используемой для меток Azure Sentinel и Azure Information Protection.

- Azure Information Protection был добавлен в портал Azure. Если вам нужна помощь с этим шагом, см. статью [добавление Azure Information Protection в портал Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Подключение к Azure Information Protection

Используйте следующие инструкции, если вы не настроили рабочую область Log Analytics для Azure Information Protection или вам нужно изменить рабочую область, в которой хранятся данные журнала Azure Information Protection.

1. В Azure Sentinel выберите **соединители данных**, а затем **Azure Information Protection (Предварительная версия)** .

2. Выберите **открыть страницу соединителя**.

3. В колонке **Настройка аналитики (Предварительная версия)** выберите рабочую область, которую вы используете в Azure Sentinel. Если выбрать другую рабочую область, данные отчетов из Azure Information Protection будут недоступны для Azure Sentinel.

4. Выбрав рабочую область, нажмите кнопку **ОК** , после чего **состояние** соединителя должно измениться на **подключено**.

5. Данные отчетов из Azure Information Protection хранятся в таблице **InformationProtectionLogs_CL** в выбранной рабочей области. 
    
    Чтобы использовать соответствующую схему в Azure Monitor для этих данных отчетов, выполните поиск по запросу **информатионпротектионевентс**. Сведения об этих функциях событий см. в разделе [Справочник по понятным схемам функций событий](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) статьи Azure Information Protection.

## <a name="next-steps"></a>Следующие шаги

В этом документе вы узнали, как подключить Azure Information Protection к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
