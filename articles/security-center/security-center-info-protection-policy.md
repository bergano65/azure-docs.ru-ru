---
title: Политика SQL Information Protection в центре безопасности Azure
description: Сведения о настройке политик защиты информации в центре безопасности Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: aa3492cb67a4ccd1c09a1f1cb55ddc4f2e00953d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318593"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Политика SQL Information Protection в центре безопасности Azure
 
[Механизм обнаружения и классификации данных](../azure-sql/database/data-discovery-and-classification-overview.md) для защиты информации SQL встроен в [базу данных SQL Azure](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL управляемый экземпляр](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)и [Azure синапсе Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Она предоставляет расширенные возможности для обнаружения, классификации, добавления меток и создания отчетов о конфиденциальных данных в базах данных.

Механизм классификации основан на двух основных конструкциях, которые составляют таксономию классификации:

- **Метки** — основные атрибуты классификации, используемые для определения уровня чувствительности данных, хранящихся в столбце. 
- **Типы сведений** обеспечивают дополнительную степень детализации для типа данных, хранящихся в столбце.

Параметры политики информационной защиты в центре безопасности предоставляют предопределенный набор меток и типов данных, которые используются по умолчанию для механизма классификации. Политику можно настроить в соответствии с потребностями вашей организации, как описано ниже.

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Страница, на которой показана политика SQL Information Protection":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Разделы справки получить доступ к политике SQL Information Protection?

Существует три способа доступа к политике информационной защиты.

- **(Рекомендуется)** На странице цены и параметры центра безопасности.
- Из рекомендации по безопасности "конфиденциальные данные в базах данных SQL должны классифицироваться".
- На странице обнаружения данных в БД SQL Azure.

Каждая из них показана на соответствующей вкладке ниже.



### <a name="from-security-centers-settings"></a>[**Из параметров центра безопасности**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Доступ к политике с помощью страницы цен и параметров центра безопасности <a name="sqlip-tenant"></a>

Чтобы настроить политику защиты информации для клиента Azure, необходимо иметь [права администратора на корневую группу управления клиента](security-center-management-groups.md). 

На странице **цены и настройки** центра безопасности выберите **SQL Information Protection**.

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Доступ к политике Information Protection SQL на странице &quot;цены и параметры&quot; центра безопасности Azure":::



### <a name="from-security-centers-recommendation"></a>[**Из рекомендации центра безопасности**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Доступ к политике из рекомендации центра безопасности <a name="sqlip-db"></a>

Используйте рекомендацию центра безопасности, "конфиденциальные данные в базах данных SQL должны классифицироваться", чтобы просмотреть страницу обнаружения и классификации данных для базы данных. Там также будут рассмотрены столбцы, содержащие сведения, которые мы рекомендуем классифицировать.

1. На странице **рекомендаций** центра безопасности найдите сведения о рекомендациях **в базах данных SQL, которые следует классифицировать**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Поиск рекомендации, предоставляющей доступ к политикам SQL Information Protection":::

1. На странице сведения об рекомендации выберите соответствующую базу данных на вкладках **работоспособная** или **неработоспособная** .

1. Откроется страница **классификация & обнаружения данных** . Нажмите кнопку **Настроить**. 

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Открытие политики SQL Information Protection из соответствующей рекомендации в центре безопасности Azure":::



### <a name="from-azure-sql"></a>[**Из Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Доступ к политике из Azure SQL <a name="sqlip-azuresql"></a>

1. В портал Azure откройте Azure SQL.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Открытие SQL Azure из портал Azure":::

1. Выберите любую базу данных.

1. В области **безопасности** меню откройте страницу **& классификация обнаружения данных** (1) и выберите **Настройка** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Открытие политики SQL Information Protection из Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Настройка типов сведений

Для управления и настройки типов данных:

1. Выберите **Управление типами данных**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Управление типами сведений для политики защиты информации":::

1. Чтобы добавить новый **тип сведений** , выберите в верхнем меню пункт **Создать тип сведений**. Для **типа сведений** можно задать имя, описание и строки шаблонов поиска. В строках шаблона поиска при необходимости можно использовать ключевые слова с использованием подстановочных знаков (с помощью символа "%"), которые использует механизм автоматического обнаружения для выявления конфиденциальных данных в базах данных на основе метаданных столбцов.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Настройка нового типа данных для политики защиты информации":::

1. Можно также настроить встроенные **типы сведений** путем добавления дополнительных строк шаблона поиска, отключения некоторых из существующих строк или изменения описания. Нельзя удалить встроенные **типы сведений** или изменить их имена. 
1. **Типы сведений** перечислены в порядке возрастания ранга обнаружения, это означает, что типы сведений в верхней части списка будут сопоставляться в первую очередь. Чтобы изменить порядок обнаружения типов сведений, перетащите их мышью в нужное место в таблице или используйте кнопки **вверх** и **вниз**. 
1. По завершении нажмите кнопку **ОК** .
1. После завершения настройки типов сведений не забудьте связать подходящие типы с соответствующими метками, нажав кнопку **Настройка** для определенной метки и добавив или удалив соответствующим образом типы сведений.
1. Чтобы применить изменения, выберите **сохранить** на странице Основные **метки** .
 

## <a name="exporting-and-importing-a-policy"></a>Экспорт и импорт политики

Вы можете скачать JSON-файл с определенными метками и типами сведений, изменить файл в любом редакторе, а затем импортировать обновленный файл. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Экспорт и импорт политики защиты информации":::

> [!NOTE]
> Для импорта файла политики потребуются разрешения уровня клиента. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Управление информационной защитой SQL с помощью Azure PowerShell

- [Get-азсклинформатионпротектионполици](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): получает действующую политику защиты данных SQL клиента.
- [Set-азсклинформатионпротектионполици](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): задает действующую политику защиты данных SQL клиента.
 

## <a name="next-steps"></a>Дальнейшие действия
 
Из этой статьи вы узнали об определении политики защиты информации SQL Information Protection в центре безопасности Azure. Дополнительные сведения об использовании SQL Information Protection для классификации и защиты конфиденциальных данных в базах данных SQL см. в разделе [Обнаружение и классификация данных в службе "База данных SQL Azure"](../azure-sql/database/data-discovery-and-classification-overview.md). 

Дополнительные сведения о политиках безопасности и безопасности данных в центре безопасности Azure см. в следующих статьях:
 
- [Настройка политик безопасности в центре безопасности Azure](tutorial-security-policy.md). Узнайте, как настроить политики безопасности для подписок и групп ресурсов Azure.
- [Безопасность данных в центре безопасности Azure](security-center-data-security.md): Узнайте, как центр безопасности управляет и защищает данные