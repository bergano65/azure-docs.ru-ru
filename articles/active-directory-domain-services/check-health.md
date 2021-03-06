---
title: Проверка работоспособности Azure Active Directory доменных служб | Документация Майкрософт
description: Узнайте, как проверить работоспособность управляемого домена Azure Active Directory доменных служб (Azure AD DS) и получить сведения о сообщениях о состоянии с помощью портал Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 501214f87a65c71436e262608f7e9b3471cc9775
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705410"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Проверка работоспособности управляемого домена Azure Active Directory доменных служб

Azure Active Directory доменных служб (Azure AD DS) выполняет некоторые фоновые задачи для поддержания работоспособности управляемого домена и обновления. Эти задачи включают создание резервных копий, применение обновлений для системы безопасности и синхронизацию данных из Azure AD. При возникновении проблем с управляемым доменом AD DS Azure эти задачи могут быть не выполнены. Для просмотра и устранения проблем можно проверить состояние работоспособности управляемого домена AD DS Azure с помощью портал Azure.

В этой статье показано, как просмотреть состояние работоспособности AD DS Azure и узнать, какие сведения или оповещения отображаются.

## <a name="view-the-health-status"></a>Просмотр состояния работоспособности

Состояние работоспособности управляемого домена AD DS Azure просматривается с помощью портал Azure. Можно просмотреть сведения о времени последней архивации и синхронизации с Azure AD, а также предупреждения, указывающие на проблему с работоспособностью управляемого домена. Чтобы просмотреть состояние работоспособности управляемого домена Azure AD DS, выполните следующие действия.

1. В портал Azure найдите и выберите **доменные службы Azure AD**.
1. Выберите управляемый домен Azure AD DS, например *aadds.contoso.com*.
1. В левой части окна ресурса AD DS Azure выберите **работоспособность**. На следующем примере снимка экрана показан работоспособный управляемый домен Azure AD DS и состояние последней резервной копии и синхронизации Azure AD:

    ![Обзор страницы работоспособности в портал Azure показывающей состояние служб домена Azure Active Directory](./media/check-health/health-page.png)

*Последняя оценка* метки времени на странице работоспособности показывает, когда последний раз был проверен управляемый домен Azure AD DS. Работоспособность управляемого домена AD DS Azure оценивается каждый час. Если вы вносите изменения в управляемый домен Azure AD DS, дождитесь следующего цикла оценки, чтобы просмотреть обновленное состояние работоспособности.

Состояние в правом верхнем углу указывает общую работоспособность управляемого домена Azure AD DS. Состояние факторов зависит от всех существующих оповещений в вашем домене. В следующей таблице описаны доступные индикаторы состояния.

| Status | Значок | Пояснение |
| --- | :----: | --- |
| Выполнение | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Управляемый домен Azure AD DS работает правильно и не имеет критических или предупреждающих оповещений. Возможно, в домене имеются информационные оповещения. |
| Требует внимания (предупреждение) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | В управляемом домене Azure AD DS нет критических оповещений, но есть одно или несколько предупреждений, которые следует устранить. |
| Требует внимания (критический) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | В управляемом домене Azure AD DS есть одно или несколько критических оповещений, которые необходимо устранить. У вас также может быть предупреждение и (или) информационные оповещения. |
| Развертывание | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Выполняется развертывание домена AD DS Azure. |

## <a name="understand-monitors-and-alerts"></a>Общие сведения о мониторах и оповещениях

Состояние работоспособности управляемого домена AD DS Azure показывает два типа мониторов информации и предупреждений. Мониторы показывают время выполнения основных фоновых задач. Оповещения предоставляют сведения или предложения по повышению стабильности управляемого домена.

### <a name="monitors"></a>Мониторы

Мониторы — это области управляемого домена AD DS Azure, которые регулярно проверяются. Если для управляемого домена Azure AD DS есть активные предупреждения, это может привести к тому, что один из мониторов сообщит о неполадке. В настоящее время доменные службы Azure AD отслеживают следующие области:

* Backup
* Синхронизация с Azure AD

#### <a name="backup-monitor"></a>Монитор резервного копирования

Монитор резервного копирования проверяет, успешно ли запущены автоматические регулярные резервные копии управляемого домена AD DS Azure. В следующей таблице приведены сведения о доступном состоянии монитора резервного копирования.

| Значение столбца сведений | Пояснение |
| --- | --- |
| Резервное копирование никогда не выполнялось | Это нормальное состояние для новых управляемых доменов Azure AD DS. Первая резервная копия должна создаваться 24 часа после развертывания управляемого домена Azure AD DS. Если это состояние сохраняется, [откройте запрос в службу поддержки Azure][azure-support]. |
| "Last backup was taken 1 to 14 days ago" (Последняя резервная копия была создана 1–14 дней назад) | Этот диапазон времени является ожидаемым состоянием для монитора резервного копирования. В этом периоде должно выполняться автоматическое регулярное резервное копирование. |
| "Last backup was taken more than 14 days ago" (Последняя резервная копия была создана более 14 дней назад) | Промежуток времени, превышающий две недели, указывает на наличие проблемы с автоматическим регулярным резервным копированием. Активные критические оповещения могут препятствовать резервному копированию управляемого домена AD DS Azure. Устраните все активные предупреждения для управляемого домена Azure AD DS. Если монитор резервного копирования не обновляет состояние, чтобы сообщить о последней резервной копии, [откройте запрос в службу поддержки Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Синхронизация с монитором Azure AD

Управляемый домен Azure AD DS регулярно синхронизируется с Azure Active Directory. Количество пользователей и объектов групп, а также число изменений, внесенных в каталог Azure AD с момента последней синхронизации, влияет на время, затрачиваемое на синхронизацию. Если управляемый домен AD DS Azure был последней синхронизации в течение трех дней назад, проверьте и устраните все активные оповещения. Если монитор синхронизации не обновляет состояние для отображения последней синхронизации, [откройте запрос в службу поддержки Azure][azure-support].

### <a name="alerts"></a>Оповещения

Оповещения создаются для проблем в управляемом домене Azure AD DS, которые должны быть решены для правильной работы службы. Каждое предупреждение описывает проблему и предоставляет URL-адрес, описывающий конкретные действия по устранению проблемы. Дополнительные сведения о возможных предупреждениях и их разрешениях см. в разделе [Устранение неполадок оповещений](troubleshoot-alerts.md).

Оповещения о состоянии работоспособности делятся на следующие уровни серьезности:

 * **Критические оповещения** — это проблемы, которые серьезно влияют на управляемый домен Azure AD DS. Эти оповещения следует устранить немедленно. Платформа Azure не может выполнять мониторинг, управление, исправление и синхронизацию управляемого домена, пока проблемы не будут устранены.
 * **Предупреждающие оповещения** сообщают о проблемах, которые могут повлиять на операции с управляемым доменом AD DS Azure в случае повторения ошибки. Эти оповещения также предлагают рекомендации по защите управляемого домена.
 * **Информационные оповещения** — это уведомления, которые не негативно влияют на управляемый домен Azure AD DS. Информационные оповещения позволяют понять, что происходит в управляемом домене.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об оповещениях, отображаемых на странице состояние работоспособности, см. в статье [разрешение оповещений в управляемом домене][troubleshoot-alerts] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
