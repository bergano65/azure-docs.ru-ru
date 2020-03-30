---
title: Приложения для прокси-приложений отогить от этого приложения - Активный каталог Azure (ru) Документы Майкрософт
description: Проблема дебага с приложениями Azure Active Directory (Azure AD) приложениями.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382073"
---
# <a name="debug-application-proxy-application-issues"></a>Устранение проблем с приложениями Application Proxy 

Эта статья помогает устранить проблемы с приложениями Azure Active Directory (Azure AD) приложениями для приложений. Если вы используете службу Application Proxy для удаленного доступа к предприимжению веб-приложений, но у вас возникли проблемы с подключением к приложению, используйте эту диаграмму для отладки проблем с приложениями. 

## <a name="before-you-begin"></a>Перед началом

При устранении проблем с прокси-сервером приложений мы рекомендуем вам начать с разъемов. Во-первых, следуйте потоку устранения неполадок в [debug Application Proxy Connector вопросы,](application-proxy-debug-connectors.md) чтобы убедиться, что подключение прокси-приложений настроены правильно. Если у вас все еще возникают проблемы, вернитесь к этой статье, чтобы устранить неполадки в приложении.  

Для получения дополнительной информации о приложении Прокси, см.:

- [Удаленный доступ к корпоративным приложениям через приложение Proxy](application-proxy.md)
- [Разъемы прокси-серверов приложения](application-proxy-connectors.md)
- [Установка и регистрация соединителя](application-proxy-add-on-premises-application.md)
- [Устранение неполадок и сообщения об ошибках прокси приложения](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flowchart для проблем с приложениями

Эта диаграмма потока просматривает шаги для отладки некоторых наиболее распространенных проблем с подключением к приложению. Для получения подробной информации о каждом шаге, см таблица следующие flowchart.

![Flowchart показывает шаги для отладки приложения](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Действие | Описание | 
|---------|---------|---------|
|1 | Откройте браузер, получите доступ к приложению и введите свои учетные данные | Попробуйте использовать свои учетные данные, чтобы войти в приложение, и проверить любые ошибки, связанные с пользователем, как [это корпоративное приложение не может быть доступна.](application-proxy-sign-in-bad-gateway-timeout-error.md) |
|2 | Проверка назначения пользователя в приложение | Убедитесь, что ваша учетная запись пользователя имеет разрешение на доступ к приложению из нутри корпоративной сети, а затем протестируйте вход в приложение, выяснив шаги в [тестовом приложении.](application-proxy-add-on-premises-application.md#test-the-application) Если проблемы, связанные с ввозами, сохраняются, [см. Как устранить ошибки в всходе.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)  |
|3 | Откройте браузер и попытайтесь получить доступ к приложению | Если ошибка появляется немедленно, проверьте, что прокси-сервер приложения настроен правильно. Для получения подробной информации [Troubleshoot Application Proxy problems and error messages](application-proxy-troubleshoot.md)о конкретных сообщениях об ошибках см.  |
|4 | Проверьте настройку пользовательского домена или устранение ошибки | Если страница не отображается вообще, убедитесь, что пользовательский домен настроен правильно, просмотрев [работу с пользовательскими доменами.](application-proxy-configure-custom-domain.md)<br></br>Если страница не загружается и появляется сообщение об ошибке, устранение ошибки, ссылаясь на [проблемы Troubleshoot Application Proxy и сообщения об ошибках.](application-proxy-troubleshoot.md) <br></br>Если для появления сообщения об ошибке требуется более 20 секунд, может возникнуть проблема с подключением. Перейдите к статье [debug Application Proxy.](application-proxy-debug-connectors.md)  |
|5 | Если проблемы сохраняются, перейдите к отладке разъема | Между прокси-сервером и разъемом или между разъемом и задним концом может возникнуть проблема подключения. Перейдите к статье [debug Application Proxy.](application-proxy-debug-connectors.md) |
|6 | Опубликовать все ресурсы, проверить инструменты разработчика браузера и исправить ссылки | Убедитесь, что путь публикации включает в себя все необходимые изображения, скрипты и листы стилей для вашего приложения. Для получения подробной информации смотрите [в приложении «Дополнительная информация» в Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) <br></br>Используйте инструменты разработчика браузера (инструменты F12 в Internet Explorer или Microsoft Edge) и проверяйте наличие проблем с публикацией, описанных на [странице приложения, неправильно.](application-proxy-page-appearance-broken-problem.md) <br></br>Просмотр вариантов разрешения сломанных ссылок в [ссылках на странице не работает.](application-proxy-page-links-broken-problem.md) |
|7 | Проверка на наличие задержки сети | Если страница загружается медленно, узнайте о способах минимизации задержки сети в [рассмотрении для уменьшения задержки.](application-proxy-network-topology.md#considerations-for-reducing-latency) | 
|8 | Посмотреть дополнительную справку по устранению неполадок | Если проблемы сохраняются, найдите дополнительные статьи для устранения неполадок в [документации по устранению неполадок Application Proxy.](application-proxy-troubleshoot.md) |

## <a name="next-steps"></a>Дальнейшие действия


* [Публикация приложений в отдельных сетях и расположениях с помощью групп соединителей](application-proxy-connector-groups.md)
* [Работа с имеющимися локальными прокси-серверами](application-proxy-configure-connectors-with-proxy-servers.md)
* [Устранение неполадок и сообщения об ошибках прокси приложения](application-proxy-troubleshoot.md)
* [Автоматическая установка соединителя прокси приложения Azure AD](application-proxy-register-connector-powershell.md)
