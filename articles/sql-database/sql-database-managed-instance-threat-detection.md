---
title: Настройка обнаружения угроз в управляемом экземпляре Базы данных SQL Azure | Документация Майкрософт
description: Система обнаружения угроз выявляет подозрительные действия в базе данных, указывающие на наличие потенциальных угроз для ее безопасности в управляемом экземпляре.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567291"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Настройка обнаружения угроз в управляемом экземпляре Базы данных SQL Azure (предварительная версия)

[Система обнаружения угроз](sql-database-threat-detection-overview.md) для [управляемого экземпляра](sql-database-managed-instance-index.yml) обнаруживает аномальные действия, указывающие на нестандартные и потенциально вредоносные попытки получить доступ к базам данных или воспользоваться их уязвимостями. Система обнаружения угроз может определить **потенциальную атаку путем внедрения кода SQL**, **доступ из необычного центра обработки данных или расположения**, **доступ из незнакомого субъекта или потенциально опасного приложения**, а также **подбор учетных данных SQL** (см. дополнительные сведения об [оповещениях системы обнаружения угроз](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)).

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Система обнаружения угроз](sql-database-threat-detection-overview.md) входит в состав предложения [Расширенная защита данных](sql-database-advanced-data-security.md) (ADS), которое представляет собой единый пакет расширенных возможностей безопасности SQL. Доступ к системе обнаружения угроз можно получить через центральный портал SQL ADS. Там же ею можно управлять. За использование системы обнаружения угроз взимается плата в размере 15 долл. США в месяц за каждый управляемый экземпляр. При этом первые 30 дней системой можно пользоваться бесплатно.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Настройка обнаружения угроз для управляемого экземпляра на портале Azure

1. Запустите портал Azure: [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки управляемого экземпляра, который требуется защитить. На странице **Параметры** выберите **Обнаружение угроз**.
3. На странице настройки обнаружения угроз сделайте следующее:
   - Переведите переключатель системы обнаружения угроз в положение **ВКЛ.** .
   - Настройте **список электронных адресов** для получения оповещений безопасности в случае обнаружения подозрительной активности в базе данных.
   - Выберите **учетную запись хранения Azure** для сохранения записей аудита аномальных угроз.
4. Щелкните **Сохранить**, чтобы сохранить новую или обновленную политику обнаружения угроз.

   ![обнаружение угроз](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [системе обнаружения угроз](sql-database-threat-detection-overview.md).
- Дополнительные сведения см. в статье [Использование Управляемого экземпляра базы данных SQL с виртуальными сетями и почти полной совместимостью](sql-database-managed-instance.md).
- См. дополнительные сведения об [обнаружении угроз в отдельной базе данных](sql-database-threat-detection.md).
- Дополнительные сведения см. в статье [Приступая к аудиту управляемого экземпляра Базы данных SQL Azure](https://go.microsoft.com/fwlink/?linkid=869430).
- Дополнительные сведения см. в статье [Что такое Центр безопасности Azure?](https://docs.microsoft.com/azure/security-center/security-center-intro).
