---
title: Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory | Документация Майкрософт
description: Содержит список сообщений об ошибках пакета содержимого действий Azure Active Directory и инструкции по их исправлению.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807527"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Устранение ошибок пакетов содержимого, зарегистрированных в журналах действий Azure Active Directory 

|  |
|--|
|В настоящее время пакет содержимого Azure AD Power BI использует API-интерфейсы Azure AD Graph для получения данных от вашего клиента Azure AD. Таким образом, можно заметить некоторые несоответствия между данными, доступными в пакете содержимого и данными, полученными с помощью [API-интерфейсов Microsoft Graph для создания отчетов](concept-reporting-api.md). |
|  |

При работе с пакетом содержимого Power BI для Azure Active Directory (AAD) могут произойти приведенные ниже ошибки. 

- [Сбой обновления](troubleshoot-content-pack.md#refresh-failed) 
- [Не удалось обновить учетные данные источников данных](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importing of data is taking too long](#data-import-is-too-slow) (Импорт данных занимает слишком много времени) 

Данная статья содержит сведения о возможных причинах и способах устранения этих ошибок.
 
## <a name="refresh-failed"></a>"Сбой обновления" 
 
**Как отображается эта ошибка**: электронное сообщение от Power BI или состояние ошибки в журнале обновлений. 


| Причина: | Как устранить |
| ---   | ---        |
| Сбои обновления могут возникнуть, если учетные данные пользователей, подключающихся к пакету содержимого, были сброшены, но не обновлены в параметрах подключения пакета содержимого. | В Power BI найдите набор данных, соответствующий панели мониторинга журналов действий Azure Active Directory (**журналы действий Azure Active Directory**), выберите "Запланировать обновление" и введите учетные данные AAD. |
| Обновление может завершиться ошибкой из-за больших наборов данных. | В настоящее время пакета содержимого Azure AD с помощью Power BI может поддерживать только небольшие наборы данных (меньше 500,00 строк) из-за некоторых ограничений по времени ожидания в службе Power BI. При возникновении ошибки регулирования или при сбое обновления из-за проблемы с временем ожидания, возможно, вы пытаетесь получить большой набор данных. Сократите период времени, в запросе и повторите попытку.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>"Не удалось обновить учетные данные источников данных" 
 
**Как отображается эта ошибка**: в Power BI при подключении к пакету содержимого журналов действий Azure Active Directory. 

| Причина: | Как устранить |
| ---   | ---        |
| Подключающийся пользователь не является глобальным администратором, читателем сведений о безопасности или администратором безопасности. | Используйте учетную запись глобального администратора, читателя сведений о безопасности или администратора безопасности для доступа к пакетам содержимого. |
| Клиент не имеет лицензии Premium или по крайней мере одного пользователя с файлом лицензии Premium. | [Отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Импорт данных выполняется слишком медленно 
 
**Как отображается эта ошибка**: в Power BI после подключения к пакету содержимого процесс импорта данных приступает к подготовке панели мониторинга для журналов действий Azure Active Directory. Отобразится сообщение: **Импорт данных...** без каких-либо дальнейших изменений.  

| Причина: | Как устранить |
| ---   | ---        |
| В зависимости от размера клиента шаг может длиться от нескольких минут до получаса. | Если в течение часа сообщение не исчезнет и не отобразится панель мониторинга, [отправьте запрос в службу поддержки](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Дальнейшие действия

* [Установить пакет содержимого Power BI для отчетов Azure AD](quickstart-install-power-bi-content-pack.md).
* [Как использовать пакет содержимого Azure Active Directory Power BI](howto-power-bi-content-pack.md)
* [Как получить поддержку для Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
