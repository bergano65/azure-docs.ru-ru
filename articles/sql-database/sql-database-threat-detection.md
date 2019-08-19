---
title: Расширенная защита от угроз для Базы данных SQL Azure | Документация Майкрософт
description: Расширенная защита от угроз обнаруживает аномальные действия базы данных, указывающие потенциальные угрозы безопасности для базы данных в одной базе данных или эластичном пуле.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 755a3b391cb7b4909169b034cc8d89892ec2ed05
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816542"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Расширенная защита от угроз базы данных SQL Azure для отдельных баз данных или в составе пула

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) для одной базы данных и в составе пула определяет аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования. Расширенная защита от угроз позволяет определить **потенциальное внедрение кода SQL**, **доступ из необычного расположения или центра обработки данных**, **доступ из незнакомого основного или потенциально опасного приложения** и **учетные данные SQL с методом подбора** — дополнительные сведения см [в этой статье](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) является частью предложения [расширенной системы безопасности данных](sql-database-advanced-data-security.md) (ADS), которое является единым пакетом для расширенных возможностей обеспечения безопасности SQL. Расширенную защиту от угроз можно получить и управлять с помощью центрального портала SQL AD.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Настройка расширенной защиты от угроз в портал Azure

1. Запустите портал Azure: [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки сервера Базы данных SQL Azure, который требуется защитить. В параметрах безопасности выберите **Расширенная защита данных**.
3. Выполните следующие действия на странице конфигурации **Расширенная защита данных**.

   - Включите Расширенную защиту данных на сервере.
   - В **параметрах расширенной защиты от угроз** в текстовом поле **Кому отправлять оповещения** предоставьте список адресов электронной почты пользователей, которым будут отправляться оповещения системы безопасности об обнаружении аномальных действий в базе данных.
  
   ![Настройка расширенной защиты от угроз](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Цены на снимках экрана не всегда соответствуют текущей цене и являются примером.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Настройка расширенной защиты от угроз с помощью PowerShell

Пример сценария см. в статье [Настройка аудита и расширенная защита от угроз с помощью PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [Advanced Threat protection](sql-database-threat-detection-overview.md).
- Дополнительные сведения о [Advanced Threat Protection в управляемом экземпляре](sql-database-managed-instance-threat-detection.md).  
- Дополнительные сведения о [расширенной защите данных](sql-database-advanced-data-security.md).
- Дополнительные сведения об [аудите](sql-database-auditing.md).
- Дополнительные сведения о [Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
- Дополнительные сведения о [ценах на Базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).  
