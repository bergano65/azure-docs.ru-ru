---
title: Интеграция локальных приложений с Cloud App Security в Azure Active Directory | Документация Майкрософт
description: Настройка локального приложения в Azure Active Directory для работы с Microsoft Cloud App Security (MCAS). Использование управления условным доступом к приложениям MCAS для мониторинга сеансов и управления ими в режиме реального времени на основе политик условного доступа. Эти политики можно применять к локальным приложениям, использующим Application Proxy в Azure Active Directory (Azure AD).
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 3be75f2fe004939eff6bf6cc960759db69bfb93a
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215105"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Настройка мониторинга доступа к приложениям в режиме реального времени с помощью Microsoft Cloud App Security и Azure Active Directory
Настройте локальное приложение в Azure Active Directory (Azure AD) для использования Microsoft Cloud App Security (MCAS) в целях мониторинга в режиме реального времени. MCAS поддерживает функцию управления условным доступом к приложениям для мониторинга сеансов и управления ими в режиме реального времени на основе политик условного доступа. Эти политики можно применять к локальным приложениям, использующим Application Proxy в Azure Active Directory (Azure AD).

Ниже приведены некоторые примеры типов политик, которые можно создать с помощью MCAS.

- Блокировка или защита скачивания конфиденциальных документов на неуправляемых устройствах.
- Отслеживание случаев входа пользователей с высоким уровнем риска в приложения и регистрация их действий в ходе сеанса. На основе этой информации можно анализировать поведение пользователей, чтобы определять способ применения политик сеансов.
- Использование сертификатов клиентов или принципов соответствия устройств для блокировки доступа к определенным приложениям с неуправляемых устройств.
- Ограничение сеансов пользователей из некорпоративных сетей. Пользователям, обращающимся к приложениям из-за пределов корпоративной сети, можно предоставить ограниченные права доступа. Например, этот ограниченный доступ может блокировать скачивание конфиденциальных документов пользователем.

Дополнительные сведения см. в статье о [защите приложений с помощью управления условным доступом к приложениям для Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Требования

Лицензия

- Лицензия EMS E5 или 
- Azure Active Directory Premium P1 и автономная версия MCAS.

Локальное приложение

- В локальном приложении необходимо использовать ограниченное делегирование Kerberos (KCD).

Настройка Application Proxy

- Настройте Azure AD для использования Application Proxy, включая подготовку среды и установку соединителя Application Proxy. Инструкции см. в руководстве [Добавление локального приложения для удаленного доступа через Application Proxy в Azure Active Directory](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Добавление локального приложения в Azure AD

Добавьте локальное приложение в Azure AD. Краткие инструкции см. в разделе [Добавление локального приложения в Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). При добавлении приложения задайте два следующих параметра в колонке **Добавление локального приложения**.

- **Предварительная проверка подлинности**: введите **Azure Active Directory**.
- **Translate URLs in Application Body** (Преобразовывать URL-адреса в коде приложения): выберите **Yes** (Да).

Эти два параметра являются обязательными для работы приложения с MCAS.

## <a name="test-the-on-premises-application"></a>Тестирование локального приложения

После добавления приложения в Azure AD следуйте инструкциям в разделе [Тестирование приложения](application-proxy-add-on-premises-application.md#test-the-application) по добавлению пользователя для тестирования, а затем протестируйте процедуру входа. 

## <a name="deploy-conditional-access-app-control"></a>Развертывание управления условным доступом к приложениям

Чтобы настроить приложение с функцией управления условным доступом к приложениям, следуйте инструкциям в статье [Развертывание функции управления настройками условного доступа для приложений Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Тестирование управления условным доступом к приложениям

Чтобы протестировать развертывание приложений Azure AD с функцией управления условным доступом к приложениям, следуйте инструкциям в разделе [Тестирование развертывания](/cloud-app-security/proxy-deployment-aad#test-the-deployment).





