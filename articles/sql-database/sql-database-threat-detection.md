---
title: Обнаружение угроз для базы данных SQL Azure | Документация Майкрософт
description: Система обнаружения угроз выявляет подозрительные действия в базе данных, указывающие на наличие потенциальных угроз для ее безопасности в отдельной базе данных или эластичном пуле.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/25/2018
ms.openlocfilehash: 91b5fa521b1513f86bb9112138cf10fdb31badca
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997313"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Оповещение об обнаружении угроз для отдельной базы данных SQL Azure

[Система обнаружения угроз](sql-database-threat-detection-overview.md) SQL Azure выявляет аномальные действия, указывающие на нестандартные и потенциально вредоносные попытки получить доступ к базам данных или воспользоваться их уязвимостями в отдельных [базах данных SQL](sql-database-technical-overview.md). Система обнаружения угроз может определить **потенциальную атаку путем внедрения кода SQL**, **доступ из необычного центра обработки данных или расположения**, **доступ из незнакомого субъекта или потенциально опасного приложения**, а также **подбор учетных данных SQL** (см. дополнительные сведения об [оповещениях системы обнаружения угроз](sql-database-threat-detection.md#azure-sql-database-threat-detection-alerts)).

Вы можете получать уведомления об обнаруженных угрозах с помощью [уведомлений по электронной почте](sql-database-threat-detection.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) или [портала Azure](sql-database-threat-detection.md#explore-threat-detection-alerts-for-your-database-in-the-Azure-portal)

[Система обнаружения угроз](sql-database-threat-detection-overview.md) входит в состав предложения [Расширенная защита от угроз SQL](sql-advanced-threat-protection.md), которое представляет собой единый пакет расширенных средств обеспечения безопасности SQL. Доступ к системе обнаружения угроз можно получить через центральный портал SQL ATP. Там же ею можно управлять. За использование системы обнаружения угроз взимается плата в размере 15 долл. США в месяц для каждого логического сервера. При этом первые 30 дней системой можно пользоваться бесплатно.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Настройка обнаружения угроз для базы данных с помощью портала Azure
1. Запустите портал Azure: [https://portal.azure.com](https://portal.azure.com).
2. Перейдите на страницу настройки сервера базы данных SQL Azure, который требуется защитить. В параметрах безопасности выберите **Advanced Threat Protection**.
3. На странице конфигурации **Advanced Threat Protection**, сделайте следующее:

   - Включите расширенную защиту от угроз на сервере.
   - В **параметрах обнаружения угроз** в текстовом поле **Кому отправлять оповещения** предоставляется список адресов электронной почты пользователей, которым будут отправляться оповещения системы безопасности об обнаружении аномальных действий в базе данных.
  
   ![Настройка обнаружения угроз](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Настройка обнаружения угроз с помощью PowerShell

Пример сценария см. в статье [Настройка аудита и обнаружения угроз для базы данных SQL с помощью PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [системе обнаружения угроз](sql-database-threat-detection-overview.md).
* См. дополнительные сведения об [обнаружении угроз в Управляемом экземпляре](sql-database-managed-instance-threat-detection.md).  
* Дополнительные сведения о службе "Расширенная защита от угроз SQL" см. в [этой статье](sql-advanced-threat-protection.md). 
* Узнайте больше об [аудите базы данных SQL Azure](sql-database-auditing.md).
* Узнайте больше о [центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
* Дополнительные сведения о ценах см. на [странице цен на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).  
