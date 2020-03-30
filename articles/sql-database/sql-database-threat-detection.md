---
title: Настройка службы "Расширенная защита от угроз"
description: Advanced Threat Protection обнаруживает аномальные действия базы данных, указывающие на потенциальные угрозы безопасности базы данных в единой базе данных или эластичной пуле.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822511"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Расширенная защита от угроз для отдельных или объединенных баз данных

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) для отдельных и объединенных баз данных обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или использовать их. Расширенная защита от угроз может определить **потенциальные инъекции S'L,** **Доступ из необычного местоположения или центра обработки данных,** **Доступ от незнакомого основного или потенциально вредного приложения,** а также **учетные данные Brute force S'L** - см. более подробную информацию в [расширенных предупреждениях о защите угроз.](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) является частью расширенного предложения [по безопасности данных](sql-database-advanced-data-security.md) (ADS), которое представляет собой единый пакет для передовых возможностей безопасности S'L. Доступ к расширенной защите от угроз можно получить через центральный портал SQL ADS. Там же ею можно управлять.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Настройка расширенной защиты от угроз на портале Azure

1. Запуск портала Azure на [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки сервера Базы данных SQL Azure, который требуется защитить. В параметрах безопасности выберите **Расширенная защита данных**.
3. Выполните следующие действия на странице конфигурации **Расширенная защита данных**.

   - Включите Расширенную защиту данных на сервере.
   - В **параметрах расширенной защиты от угроз** в текстовом поле **Кому отправлять оповещения** предоставьте список адресов электронной почты пользователей, которым будут отправляться оповещения системы безопасности об обнаружении аномальных действий в базе данных.
  
   ![Настройка расширенной защиты от угроз](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Цены на скриншотах не всегда отражают текущую цену, и являются примером.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Настройка Расширенной защиты от угроз с использованием PowerShell

Например, в скрипте см. [Налаживание аудита и расширенной защиты от угроз с помощью PowerShell.](scripts/sql-database-auditing-and-threat-detection-powershell.md)

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о [расширенной защите от угроз.](sql-database-threat-detection-overview.md)
- Подробнее о [расширенной защите от угроз в управляемом экземпляре.](sql-database-managed-instance-threat-detection.md)  
- Дополнительные сведения о [расширенной защите данных](sql-database-advanced-data-security.md).
- Дополнительные сведения об [аудите](sql-database-auditing.md).
- Подробнее о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Для получения более подробной информации о ценах на базе данных можно ознакомиться на [странице базы данных S'L](https://azure.microsoft.com/pricing/details/sql-database/)  
