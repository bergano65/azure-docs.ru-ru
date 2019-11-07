---
title: Настройка расширенной защиты от угроз для управляемого экземпляра базы данных SQL Azure
description: Расширенная защита от угроз обнаруживает аномальные действия базы данных, указывающие потенциальные угрозы безопасности для базы данных в управляемом экземпляре.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 8e419031c34a693bac7ab9f9825c24576e80ba14
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687884"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Настройка расширенной защиты от угроз в управляемом экземпляре базы данных SQL Azure

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) для [управляемого экземпляра](sql-database-managed-instance-index.yml) определяет аномальные действия, указывающие на необычные и потенциально опасные попытки доступа к базам данных или их использования. Расширенная защита от угроз позволяет определить **потенциальное внедрение кода SQL**, **доступ из необычного расположения или центра обработки данных**, **доступ из незнакомого основного или потенциально опасного приложения** и **учетные данные SQL с методом подбора** — дополнительные сведения см [в этой статье](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) является частью предложения [расширенной системы безопасности данных](sql-database-advanced-data-security.md) (ADS), которое является единым пакетом для расширенных возможностей обеспечения безопасности SQL. Расширенную защиту от угроз можно получить и управлять с помощью центрального портала SQL AD.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Настройка расширенной защиты от угроз в портал Azure

1. Запустите портал Azure: [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки управляемого экземпляра, который требуется защитить. На странице **Параметры** выберите пункт **Расширенная защита данных**.
3. На странице настройки дополнительной защиты данных
   - Включите **расширенную** защиту данных.
   - Настройте **список электронных адресов** для получения оповещений безопасности в случае обнаружения подозрительной активности в базе данных.
   - Выберите **учетную запись хранения Azure** для сохранения записей аудита аномальных угроз.
   - Выберите **типы расширенной защиты от угроз** , которые вы хотите настроить. Дополнительные сведения об [оповещениях Advanced Threat protection](sql-database-threat-detection-overview.md).
4. Нажмите кнопку **сохранить** , чтобы сохранить новую или обновленную расширенную политику безопасности данных.

   ![Расширенная защита от угроз](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Цены на снимках экрана не всегда соответствуют текущей цене и являются примером.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [Advanced Threat protection](sql-database-threat-detection-overview.md).
- Дополнительные сведения см. в статье [Использование Управляемого экземпляра базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- Дополнительные сведения о [Advanced Threat Protection для отдельной базы данных](sql-database-threat-detection.md).
- Дополнительные сведения см. в статье [Приступая к аудиту управляемого экземпляра Базы данных SQL Azure](https://go.microsoft.com/fwlink/?linkid=869430).
- Дополнительные сведения см. в статье [Что такое Центр безопасности Azure?](https://docs.microsoft.com/azure/security-center/security-center-intro).
