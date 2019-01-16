---
title: Настройка обнаружения угроз — Управляемый экземпляр Базы данных SQL Azure | Документация Майкрософт
description: Система обнаружения угроз выявляет подозрительные действия в базе данных, указывающие на наличие потенциальных угроз для ее безопасности в Управляемом экземпляре.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: f87b7093a732639db8a53ebec68fcae8f29735f3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158675"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Настройка обнаружения угроз в Управляемом экземпляре Базы данных SQL Azure (предварительная версия)

[Система обнаружения угроз](sql-database-threat-detection-overview.md) SQL Azure выявляет аномальные действия, указывающие на нестандартные и потенциально вредоносные попытки получить доступ к базам данных или воспользоваться их уязвимостями в [Управляемом экземпляре Базы данных SQL Azure](sql-database-managed-instance-index.yml). Система обнаружения угроз может определить **потенциальную атаку путем внедрения кода SQL**, **доступ из необычного центра обработки данных или расположения**, **доступ из незнакомого субъекта или потенциально опасного приложения**, а также **подбор учетных данных SQL** (см. дополнительные сведения об [оповещениях системы обнаружения угроз](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)).

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Система обнаружения угроз](sql-database-threat-detection-overview.md) входит в состав предложения [Расширенная защита от угроз SQL](sql-advanced-threat-protection.md), которое представляет собой единый пакет расширенных средств обеспечения безопасности SQL. Доступ к системе обнаружения угроз можно получить через центральный портал SQL ATP. Там же ею можно управлять. За использование системы обнаружения угроз взимается плата в размере 15 долл. США в месяц для каждого Управляемого экземпляра. При этом первые 30 дней системой можно пользоваться бесплатно.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Настройка обнаружения угроз для управляемого экземпляра на портале Azure

1. Запустите портал Azure: [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки управляемого экземпляра, который требуется защитить. На странице **Параметры** выберите **Обнаружение угроз**.
3. На странице настройки обнаружения угроз сделайте следующее:
   - Переведите переключатель системы обнаружения угроз в положение **ВКЛ.** .
   - Настройте **список электронных адресов** для получения оповещений безопасности в случае обнаружения подозрительной активности в базе данных.
   - Выберите **учетную запись хранения Azure** для сохранения записей аудита аномальных угроз.
4. Щелкните **Сохранить**, чтобы сохранить новую или обновленную политику обнаружения угроз.

   ![обнаружение угроз](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [системе обнаружения угроз](sql-database-threat-detection-overview.md).
- См. дополнительные сведения об [Управляемых экземплярах](sql-database-managed-instance.md).
- См. дополнительные сведения об [обнаружении угроз в отдельной базе данных](sql-database-threat-detection.md).
- См. дополнительные сведения об [аудите Управляемых экземпляров](https://go.microsoft.com/fwlink/?linkid=869430).
- См. дополнительные сведения о [Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
