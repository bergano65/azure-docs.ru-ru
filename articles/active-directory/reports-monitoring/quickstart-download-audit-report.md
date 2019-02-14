---
title: Краткое руководство по загрузке отчета по аудиту с помощью портала Azure | Документация Майкрософт
description: Узнайте, как загрузить отчет по аудиту с помощью портала Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d702810ae7de03a14e50f56c2b8e819c31f3ea9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192716"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Краткое руководство. Скачивание отчета об аудите с помощью портала Azure

Из этого краткого руководства вы узнаете, как загрузить журналы аудита своего клиента за последние 24 часа. С портала Azure можно скачать до 5000 записей. Записи отсортированы по убыванию даты и времени по умолчанию, и вы получите 5000 последних записей. 

## <a name="prerequisites"></a>Предварительные требования

Вам необходимы:

* Клиент Azure Active Directory. 
* Пользователь с ролью **администратора безопасности**, **читателя безопасности** или **глобального администратора** для этого клиента. Любой пользователь клиента, который имеет доступ к своим собственным журналам аудита.

## <a name="quickstart-download-an-audit-report"></a>Краткое руководство. Скачивание отчета об аудите

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
