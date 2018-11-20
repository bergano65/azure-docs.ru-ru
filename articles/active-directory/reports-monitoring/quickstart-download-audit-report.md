---
title: Краткое руководство по загрузке отчета по аудиту с помощью портала Azure | Документация Майкрософт
description: Узнайте, как загрузить отчет по аудиту с помощью портала Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3f4090f1724850b0263905a0593fc77cc6dbfd16
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620740"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Быстрое начало. Загрузка отчета по аудиту с помощью портала Azure

Из этого краткого руководства вы узнаете, как загрузить журналы аудита своего клиента за последние 24 часа.

## <a name="prerequisites"></a>Предварительные требования

Вам необходимы:

* Клиент Azure Active Directory. 
* Пользователь с ролью **администратора безопасности**, **читателя безопасности** или **глобального администратора** для этого клиента. Любой пользователь клиента, который имеет доступ к своим собственным журналам аудита.

## <a name="quickstart-download-an-audit-report"></a>Быстрое начало. загрузка отчета по аудиту

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **Azure Active Directory** в области навигации слева и с помощью **переключателя** выберите свой каталог Active Directory.
3. На панели мониторинга выберите **Azure Active Directory**, а затем выберите **Журналы аудита**. 
4. Выберите **Последние 24 часа** в раскрывающемся списке фильтра **Дата**, затем нажмите кнопку **Применить**, чтобы просмотреть журналы аудита за последние 24 часа. 
5. Нажмите кнопку **Загрузить**, чтобы загрузить CSV-файл, содержащий отфильтрованные записи. 

![Отчеты](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Дополнительная информация

* [Отчеты о действиях входа на портале Azure Active Directory](concept-sign-ins.md)
* [Хранение отчетов Azure Active Directory](reference-reports-data-retention.md)
* [Задержки в отчетах Azure Active Directory](reference-reports-latencies.md)
