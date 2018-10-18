---
title: Краткое руководство по загрузке отчета по входу в систему с помощью портала Azure | Документация Майкрософт
description: Узнайте, как загрузить отчет по входу в систему с помощью портала Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 334649dbe315cb54ba95a29f4c69441fcd2c3417
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46362975"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Краткое руководство. Загрузка отчета по входу в систему с помощью портала Azure | Документация Майкрософт

Из этого краткого руководства вы узнаете, как загрузить данные о входах своего клиента за последние 24 часа.

## <a name="prerequisites"></a>Предварительные требования

Вам необходимы:

* клиент Azure Active Directory с лицензией уровня Premium для просмотра отчета о действиях входа; 
* пользователь с ролью администратора безопасности, читатель безопасности, читатель отчетов или глобальный администратор для этого клиента; а также любой пользователь клиента, который имеет доступ к своим собственным данным для входа в систему.

## <a name="quickstart-download-a-sign-in-report"></a>Краткое руководство. Загрузка отчета по входу в систему

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **Azure Active Directory** в области навигации слева и с помощью **переключателя** выберите свой каталог Active Directory.
3. На панели мониторинга выберите **Azure Active Directory**, а затем выберите **Входы в систему**. 
4. Выберите **Последние 24 часа** в раскрывающемся списке фильтра **Дата**, затем нажмите кнопку **Применить**, чтобы просмотреть входы в систему за последние 24 часа. 
5. Нажмите кнопку **Загрузить**, чтобы загрузить CSV-файл, содержащий отфильтрованные записи. 

![Отчеты](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Дополнительная информация

* [Отчеты о действиях входа на портале Azure Active Directory](concept-sign-ins.md)
* [Хранение отчетов Azure Active Directory](reference-reports-data-retention.md)
* [Задержки в отчетах Azure Active Directory](reference-reports-latencies.md)
