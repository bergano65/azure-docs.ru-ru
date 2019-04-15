---
title: 'Устранение неполадок: отсутствие данных в скачанных журналах действий Azure Active Directory | Документация Майкрософт'
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
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434846"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Я не могу найти данные в скачанных журналах действий Azure Active Directory

## <a name="symptoms"></a>Проблемы

В скачанных журналах действий (аудита или входа) нет всех записей за выбранный период времени. Почему? 

 ![Отчеты](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Причина:

При загрузке журналы действий на портале Azure, применяется ограничение в 250 000 записей, отсортированных по самых новых. 

## <a name="resolution"></a>Способы устранения:

Вы можете в любой момент использовать [интерфейсы API отчетов Azure AD](concept-reporting-api.md), чтобы извлечь до миллиона записей.

## <a name="next-steps"></a>Дальнейшие действия

* [Вопросы и ответы об отчетах Azure Active Directory](reports-faq.md)

