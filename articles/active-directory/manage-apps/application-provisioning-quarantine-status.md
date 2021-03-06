---
title: Состояние подготовки приложений в карантине | Документация Майкрософт
description: Когда вы настроили приложение для автоматической подготовки пользователей, Узнайте, что такое состояние подготовки карантина, и как его очистить.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3ad689fb57c51d0deb698a723b93e6175bdbb5c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882880"
---
# <a name="application-provisioning-in-quarantine-status"></a>Подготовка приложений в карантинном состоянии

Служба подготовки Azure AD отслеживает работоспособность конфигурации и помещает неработоспособные приложения в состояние "карантина". Если большинство или все вызовы, выполненные для целевой системы, постоянно завершаются сбоем из-за ошибки, например недопустимых учетных данных администратора, задание подготовки помечается как помещенное в карантин.

В карантине частота добавочных циклов постепенно сокращается до одного раза в день. Задание подготовки удаляется из карантина после устранения всех ошибок и начала следующего цикла синхронизации. Если задание подготовки остается в карантине более четырех недель, задание подготовки отключается (остановка выполняется).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Разделы справки понять, находится ли мое приложение в карантине?

Существует три способа проверить, находится ли приложение в карантине:
  
- В портал Azure перейдите к **Azure Active Directory** > **корпоративные приложения** > &lt;*имя приложения*&gt; > **подготовки** и прокрутите индикатор выполнения внизу.  

  ![Строка состояния подготовки, показывающая состояние карантина](media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Используйте Microsoft Graph запрос [Get синчронизатионжоб](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) , чтобы программно получить состояние задания подготовки:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Проверьте свой адрес электронной почты. Когда приложение помещается в карантин, отправляется одноразовое уведомление по электронной почте. При изменении причины карантина отправляется обновленное сообщение электронной почты с новой причиной помещения в карантин. Если вы не видите сообщение электронной почты, сделайте следующее:

  - Убедитесь, что в конфигурации подготовки для приложения указан допустимый **адрес электронной почты для уведомлений** .
  - Убедитесь, что в папке "Входящие" электронной почты для уведомлений нет фильтрации нежелательной почты.
  - Убедитесь, что вы не отменили подписывание сообщений электронной почты.

## <a name="why-is-my-application-in-quarantine"></a>Почему мое приложение находится в карантине?

Запрос Microsoft Graph для получения состояния задания подготовки показывает следующую причину для карантина:

- `EncounteredQuarantineException` указывает, что были предоставлены недопустимые учетные данные. Службе подготовки не удается установить соединение между исходной системой и целевой системой.

- `EncounteredEscrowProportionThreshold` указывает, что подготовка превысила депонированный порог. Это состояние возникает при сбое более 60% событий подготовки.

- `QuarantineOnDemand` означает, что мы обнаружили проблемы с приложением и вручную настроили его в карантин.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Разделы справки получить мое приложение из карантина?

Сначала устраните проблему, которая привела к помещению приложения в карантин.

- Проверьте параметры подготовки приложения, чтобы убедиться, что [введены действительные учетные данные администратора](configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD должна иметь возможность установить отношение доверия с целевым приложением. Убедитесь, что введены допустимые учетные данные, а учетная запись имеет необходимые разрешения.

- Просмотрите [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , чтобы исследовать ошибки, приводящие к возникновению ошибок в карантине, и устраните ошибку. Чтобы получить доступ к журналам подготовки в портал Azure, перейдите в раздел **действия** **Azure Active Directory** &gt; **корпоративные приложения** &gt; **Подготовка журналов (Предварительная версия)** .

После устранения проблемы перезапустите задание подготовки. Некоторые изменения параметров подготовки приложения, такие как сопоставление атрибутов или фильтры области, автоматически перезапускают подготовку. В индикаторе выполнения на странице **подготовки** приложения отображается время последнего запуска подготовки. Если необходимо вручную перезапустить задание подготовки, используйте один из следующих методов.  

- Используйте портал Azure, чтобы перезапустить задание подготовки. На странице **Подготовка** приложения в разделе **Параметры**выберите **очистить состояние и перезапустить синхронизацию** и задайте для параметра **состояние подготовки** значение **вкл**. Это действие полностью перезапускает службу подготовки, которая может занять некоторое время. Будет снова выполнен полный начальный цикл, который очищает депонирование, удаляет приложение из карантина и очищает все водяные знаки.

- Используйте Microsoft Graph, чтобы [перезапустить задание подготовки](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Вы получите полный контроль над перезапусками. Вы можете снять флажки (чтобы перезапустить счетчик депонирования, который начисляется в сторону состояния карантина), Очистить карантин (чтобы удалить приложение из карантина) или снять пределы. Используйте следующий запрос:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`