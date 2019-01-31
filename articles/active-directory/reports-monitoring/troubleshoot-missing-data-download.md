---
title: 'Устранение неполадок: отсутствие данных в скачанных журналах действий Azure Active Directory | Документация Майкрософт'
description: В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в скачанных журналах действий.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3cf6296a5cf6bdf8697344595c0ead0908659c3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197472"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Я не могу найти данные в скачанном журнале действий Azure Active Directory

## <a name="symptoms"></a>Проблемы

В скачанном журнале действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Причина:

К журналам действий, скачиваемым на портале Azure, применяется ограничение в 5000 последних записей, отсортированных в хронологическом порядке (начиная с самых новых). 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](concept-reporting-api.md), чтобы извлечь до миллиона записей. Рекомендуем [настроить расписание выполнения скрипта](tutorial-signin-logs-download-script.md), который вызывает API отчетов для последовательного получения записей за определенный период (например, ежедневно или еженедельно). 

## <a name="next-steps"></a>Дополнительная информация

* [Вопросы и ответы об отчетах Azure Active Directory](reports-faq.md)

