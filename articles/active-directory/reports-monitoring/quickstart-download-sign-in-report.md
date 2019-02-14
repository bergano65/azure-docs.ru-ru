---
title: Краткое руководство по загрузке отчета по входу в систему с помощью портала Azure | Документация Майкрософт
description: Узнайте, как загрузить отчет по входу в систему с помощью портала Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
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
ms.openlocfilehash: eeb5a06aa4eb21ba374344623f9f96ce92a59ff5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168823"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Краткое руководство. Загрузка отчета по входу в систему с помощью портала Azure

Из этого краткого руководства вы узнаете, как загрузить данные о входах своего клиента за последние 24 часа. С портала Azure можно скачать до 5000 записей. Записи отсортированы по убыванию даты и времени по умолчанию, и вы получите 5000 последних записей. 

## <a name="prerequisites"></a>Предварительные требования

Вам необходимы:

* клиент Azure Active Directory с лицензией уровня Premium для просмотра отчета о действиях входа; Чтобы обновить выпуск Azure Active Directory, ознакомьтесь со статьей [Регистрация для работы с выпусками Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md). Обратите внимание, если до обновления данные о действиях отсутствовали, данные отобразятся в отчетах через несколько дней после обновления до лицензии Premium.
* пользователь с ролью **администратора безопасности**, **читателя безопасности**, **читателя отчетов** или **глобального администратора** для этого клиента; а также любой пользователь клиента, который имеет доступ к своим собственным данным для входа в систему.

## <a name="quickstart-download-a-sign-in-report"></a>Краткое руководство. Скачивание отчета об операциях входа в систему

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
