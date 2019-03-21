---
title: Анализе пользователей с помощью analytics пользователя в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о проверке пользователей с помощью analytics пользователя в Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246327"
---
# <a name="investigate-users-with-user-analytics"></a>Анализе пользователей с помощью analytics пользователя

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Пользователи являются сущностями, к которым вы хотите обратить особое внимание. Чтобы помочь вам получить ценную информацию пользователей, полностью Azure Sentinel интегрируется с Azure Advanced Threat Protection. Эта интеграция позволяет анализировать поведение пользователей и определять их приоритеты пользователей, которые следует обратить внимание во-первых, на основе оповещения и шаблонов подозрительных действий через Azure Sentinel и Microsoft 365.

Azure Sentinel обогащает данные пользователей с помощью аналитики из Microsoft 365, чтобы включить анализ комплексное пользователей аналитики и риска для всех пользователей в Azure Active Directory. 

## <a name="how-it-works"></a>Принцип работы

1. На основе безопасности обнаружена аналитической правила, если точное совпадение, Azure Sentinel отправляет оповещения Azure ATP.
1. Azure ATP проверяет, какие пользовательские сущности связаны с оповещения и вычисляет приоритет исследования для этих пользователей.
3. Azure ATP. затем повторно вычисляет оценку пользователей после его включают данные из правил аналитики для Azure Sentinel.


## <a name="prerequisites"></a>Технические условия

- Лицензия Azure Advanced Threat Protection 
- Чтобы включить эту функцию, вам потребуется разрешения глобального администратора в клиенте, в которой установлен Azure Sentinel

> [!NOTE]
> - Для каждого клиента Azure ATP можно подключить один экземпляр Azure Sentinel.
> - Аналитика пользователей в настоящее время доступна только для пользователей Azure Active Directory. 

## <a name="enable-user-analytics"></a>Включить аналитику пользователей

1. Чтобы включить аналитику пользователей в Azure Sentinel, в на портале, выберите **аналитику пользователей** и нажмите кнопку **включить**. Это отправляет сведения о рабочей области Azure ATP.

1. После этого вы перейдете Azure ATP. В разделе **модули безопасности** в **Microsoft Azure Sentinel** щелкните **+ плюс** для добавления и затем выберите рабочую область. 
1. Щелкните **Подключить**.

## <a name="investigate-a-user"></a>Исследовать пользователя

1. В меню Azure Sentinel в **аналитику пользователей**, просмотрите список пользователей в соответствии с их приоритетом расследования. Оценка основана на Azure Sentinel и Microsoft 365 оповещения.

2. Найдите пользователя, которое необходимо изучить. Выберите пользователя, чтобы перейти на страницу пользователя. Просмотрите действия пользователя и оповещения, со временем на временной шкале. Вы увидите все действия с Microsoft 365 и Azure Sentinel. На странице пользователя можно также открыть путем детализации пользователей из случаев.
      
    ![Пользователи](./media/user-analytics/user-investigation.png)

 
3. Чтобы это работало правильно, необходимо правильно настроить [настраиваемого правила генерации оповещений](tutorial-detect-threats.md) для сопоставления идентификаторов нужному пользователю **учетной записи** сущности.

    - Для событий Windows, сопоставьте **учетной записи** для **SID**
    - Сопоставление данных Azure AD (которую можно найти в многие журналы, включая действия Azure) или данных в Office 365, **учетной записи** свойства **GUID**, или **имя участника-пользователя**. 

   ![Запрос](./media/user-analytics/query-window.png)



Например:  
> [!NOTE]
> В журналах действий Azure действия вызывающего объекта сущности включает в себя имя участника-пользователя.

1. Этот запрос осуществляет поиск Создание аномальных количества ресурсов или операции развертывания в журнале действий Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. В настраиваемого правила генерации оповещений, сопоставить **учетной записи** свойства **вызывающий объект**.
   
   ![Запрос](./media/user-analytics/query-window.png)

3. Проверьте, пользователь в окне исследования пользователя. Дополнительные сведения о об анализе пользователей, см. в разделе [руководства: Исследовать пользователя](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключиться к Azure Sentinel поставщику аналитики угроз. Узнать больше о Azure Sentinel, см. в следующих статьях.

- Чтобы приступить к работе с Azure Sentinel, вам потребуется подписка на Microsoft Azure. Если у вас нет подписки, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- Узнайте, как [встроенных данных для Azure Sentinel](quickstart-onboard.md), и [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
