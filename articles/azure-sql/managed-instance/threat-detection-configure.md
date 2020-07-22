---
title: Настройка Расширенной защиты от угроз
titleSuffix: Azure SQL Managed Instance
description: Расширенная защита от угроз обнаруживает аномальные действия базы данных, указывающие потенциальные угрозы безопасности для базы данных в Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686339"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Настройка расширенной защиты от угроз в Azure SQL Управляемый экземпляр
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Расширенная защита от угроз](../database/threat-detection-overview.md) для [управляемый экземпляр Azure SQL](sql-managed-instance-paas-overview.md) обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования. Расширенная защита от угроз позволяет определить **потенциальное внедрение кода SQL**, **получить доступ из необычного расположения или центра обработки данных**, **получить доступ из незнакомого основного или потенциально опасного приложения**и **выполнить подбор учетных данных SQL** [. Дополнительные](../database/threat-detection-overview.md#alerts)сведения см.

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) или [портала Azure](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Расширенная защита от угроз](../database/threat-detection-overview.md) входит в состав [расширенного предложения по обеспечению безопасности данных](../database/advanced-data-security.md) , который является единым пакетом для расширенных возможностей обеспечения безопасности SQL. Доступ к расширенной защите от угроз можно получить через центральный портал SQL ADS. Там же ею можно управлять.

##  <a name="azure-portal"></a>Портал Azure

1. Войдите в [портал Azure](https://portal.azure.com). 
2. Перейдите на страницу конфигурации экземпляра SQL Управляемый экземпляр, который требуется защитить. На странице **Параметры** выберите пункт **Расширенная защита данных**.
3. На странице настройки дополнительной защиты данных
   - Включите **расширенную** защиту данных.
   - Настройте **список сообщений электронной почты** для получения оповещений системы безопасности при обнаружении аномальных действий базы данных.
   - Выберите **учетную запись хранения Azure** для сохранения записей аудита аномальных угроз.
   - Выберите **типы расширенной защиты от угроз** , которые вы хотите настроить. Дополнительные сведения об [оповещениях Advanced Threat protection](../database/threat-detection-overview.md).
4. Нажмите кнопку **сохранить** , чтобы сохранить новую или обновленную расширенную политику безопасности данных.

   ![Расширенная защита от угроз](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [Advanced Threat protection](../database/threat-detection-overview.md).
- Дополнительные сведения об управляемых экземплярах см. [в статье что такое управляемый экземпляр Azure SQL](sql-managed-instance-paas-overview.md).
- Дополнительные сведения о [расширенной защите от угроз для базы данных SQL Azure](../database/threat-detection-configure.md).
- Дополнительные сведения об [аудите управляемый экземпляр SQL](https://go.microsoft.com/fwlink/?linkid=869430).
- Дополнительные сведения о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
