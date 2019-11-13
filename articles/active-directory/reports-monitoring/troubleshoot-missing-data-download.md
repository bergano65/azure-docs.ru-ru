---
title: 'Устранение неполадок: отсутствующие данные в скачанных журналах действий | Документация Майкрософт'
description: В этой статье описаны способы устранения неполадок, связанных с отсутствием данных в скачанных журналах действий.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007722"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Я не могу найти данные в скачанных журналах действий Azure Active Directory

## <a name="symptoms"></a>Симптомы

В скачанных журналах действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Причина:

При скачивании журналов действий в портал Azure мы ограничивают масштабирование до 250 000 записей, отсортированных по последним первым. 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](concept-reporting-api.md), чтобы извлечь до миллиона записей.

## <a name="next-steps"></a>Дополнительная информация

* [Вопросы и ответы об отчетах Azure Active Directory](reports-faq.md)

