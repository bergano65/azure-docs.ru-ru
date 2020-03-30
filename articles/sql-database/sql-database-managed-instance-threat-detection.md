---
title: Настройка Расширенная защита от угроз - управляемый экземпляр
description: Advanced Threat Protection обнаруживает аномальные действия базы данных, указывающие на потенциальные угрозы безопасности базы данных в управляемом экземпляре.
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
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822548"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Настройка расширенной защиты угроз в управляемом экземпляре базы данных Azure S'L

[Расширенная защита от угроз](sql-database-threat-detection-overview.md) для [управляемого экземпляра](sql-database-managed-instance-index.yml) обнаруживает аномальные действия, указывающие на необычные и потенциально опасные попытки получить доступ к базам данных или эксплуатировать их. Расширенная защита от угроз может определить **потенциальные инъекции S'L,** **Доступ из необычного местоположения или центра обработки данных,** **Доступ от незнакомого основного или потенциально вредного приложения,** а также **учетные данные Brute force S'L** - см. более подробную информацию в [расширенных предупреждениях о защите угроз.](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) является частью расширенного предложения [по безопасности данных](sql-database-advanced-data-security.md) (ADS), которое представляет собой единый пакет для передовых возможностей безопасности S'L. Доступ к расширенной защите от угроз можно получить через центральный портал SQL ADS. Там же ею можно управлять.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Настройка расширенной защиты от угроз на портале Azure

1. Запуск портала Azure на [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки управляемого экземпляра, который требуется защитить. На странице **«Настройки»** выберите **Расширенную безопасность данных.**
3. На странице конфигурации расширенной безопасности данных
   - Поверните **ON** Advanced Data Security.
   - Налаживайте **список электронных писем** для получения предупреждений безопасности при обнаружении аномальных действий базы данных.
   - Выберите **учетную запись хранения Azure** для сохранения записей аудита аномальных угроз.
   - Выберите **типы расширенной защиты от угроз,** которые вы хотели бы настроить. Подробнее о [предупреждениях расширенной защиты от угроз.](sql-database-threat-detection-overview.md)
4. Нажмите **«Сохранить»,** чтобы сохранить новую или обновленную политику расширенной безопасности данных.

   ![Расширенная защита от угроз](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Цены на скриншотах не всегда отражают текущую цену, и являются примером.

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о [расширенной защите от угроз.](sql-database-threat-detection-overview.md)
- Дополнительные сведения см. в статье [Использование Управляемого экземпляра базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- Подробнее о [расширенной защите от угроз для одной базы данных.](sql-database-threat-detection.md)
- Подробнее об [аудите управляемых экземпляров.](https://go.microsoft.com/fwlink/?linkid=869430)
- Узнайте больше о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
