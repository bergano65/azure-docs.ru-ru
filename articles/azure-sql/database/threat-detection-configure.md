---
title: Настройка Расширенной защиты от угроз
description: Расширенная защита от угроз обнаруживает аномальные действия базы данных, указывающие потенциальные угрозы безопасности для базы данных в базе данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321552"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Настройка расширенной защиты от угроз для базы данных SQL Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Расширенная защита от угроз](threat-detection-overview.md) для базы данных SQL Azure обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования. Расширенная защита от угроз позволяет определить **потенциальное внедрение кода SQL**, **получить доступ из необычного расположения или центра обработки данных**, **получить доступ из незнакомого основного или потенциально опасного приложения**и **выполнить подбор учетных данных SQL** [. Дополнительные](threat-detection-overview.md#alerts)сведения см.

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](threat-detection-overview.md#explore-detection-of-a-suspicious-event) или [портала Azure](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Расширенная защита от угроз](threat-detection-overview.md) входит в состав [расширенного предложения по обеспечению безопасности данных](advanced-data-security.md) , который является единым пакетом для расширенных возможностей обеспечения безопасности SQL. Расширенную защиту от угроз можно получить и управлять с помощью центрального портала расширенной защиты данных SQL.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Настройка расширенной защиты от угроз в портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите на страницу конфигурации сервера, который требуется защитить. В параметрах безопасности выберите **Расширенная защита данных**.
3. Выполните следующие действия на странице конфигурации **Расширенная защита данных**.

   - Включите Расширенную защиту данных на сервере.
   - В **параметрах расширенной защиты от угроз** в текстовом поле **Кому отправлять оповещения** предоставьте список адресов электронной почты пользователей, которым будут отправляться оповещения системы безопасности об обнаружении аномальных действий в базе данных.
  
   ![Настройка расширенной защиты от угроз](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Настройка Расширенной защиты от угроз с использованием PowerShell

Пример сценария см. в статье [Настройка аудита и расширенная защита от угроз с помощью PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [Advanced Threat protection](threat-detection-overview.md).
- Дополнительные сведения о [расширенной защите от угроз в SQL управляемый экземпляр](../managed-instance/threat-detection-configure.md).  
- Дополнительные сведения о [расширенной защите данных](advanced-data-security.md).
- Дополнительные сведения об [аудите](../../azure-sql/database/auditing-overview.md).
- Дополнительные сведения о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Дополнительные сведения о ценах см. на [странице цен на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/) .  
