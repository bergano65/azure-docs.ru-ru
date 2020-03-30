---
title: Разъемы прокси-серверов приложения отлажаются - Активный каталог Azure (ru) Документы Майкрософт
description: Проблема отогивание с разъемами прокси-серверов приложений Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311853"
---
# <a name="debug-application-proxy-connector-issues"></a>Устранение проблем с соединителями Application Proxy 

Эта статья помогает выснять проблемы с подключением к прокси-серверам Azure Active Directory (Azure AD). Если вы используете службу Application Proxy для удаленного доступа к предприимжению веб-приложений, но у вас возникли проблемы с подключением к приложению, используйте эту диаграмму для отладки проблем с разъемом. 

## <a name="before-you-begin"></a>Перед началом

Эта статья предполагает, что вы установили разъем прокси-сервер приложения и возникли проблемы. При устранении проблем с прокси-сервером приложения мы рекомендуем вам начать с этого потока устранения неполадок, чтобы определить, правильно ли настроены разъемы прокси-серверов приложения. Если у вас все еще возникают проблемы с подключением к приложению, следуйте потоку устранения неполадок в [вопросах приложения Debug Application Proxy.](application-proxy-debug-apps.md)  


Для получения дополнительной информации о приложении Прокси и с помощью его разъемы, см.:

- [Удаленный доступ к корпоративным приложениям через приложение Proxy](application-proxy.md)
- [Разъемы прокси-серверов приложения](application-proxy-connectors.md)
- [Установка и регистрация соединителя](application-proxy-add-on-premises-application.md)
- [Устранение неполадок и сообщения об ошибках прокси приложения](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flowchart для проблем с разъемом

Эта диаграмма потока просматривает шаги для отладки некоторых наиболее распространенных проблем с разъемом. Для получения подробной информации о каждом шаге, см таблица следующие flowchart.

![Flowchart показывает шаги для отладки разъема](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Действие | Описание | 
|---------|---------|---------|
|1 | Найти группу разъема, назначенную приложению | Вы, вероятно, разъем установлен на нескольких серверах, и в этом случае разъемы должны быть [назначены для разъема групп.](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups) Дополнительные сведения о группах соединителей см. в статье [Публикация приложений в отдельных сетях и расположениях с помощью групп соединителей](application-proxy-connector-groups.md). |
|2 | Установите разъем и назначить группу | Если у вас нет установленного разъема, [см.](application-proxy-add-on-premises-application.md#install-and-register-a-connector)<br></br> Если у вас возникли проблемы с установкой разъема, [см.](application-proxy-connector-installation-problem.md)<br></br> Если разъем не назначен группе, [просийте, чтобы разъем был для группы.](application-proxy-connector-groups.md#create-connector-groups)<br></br>Если приложение не назначено группе разъемов, [см.](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)|
|3 | Выполнить тест порта на сервере разъема | На сервере разъема запустите портовый тест с помощью [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) или другого инструмента тестирования порта, чтобы проверить, открыты ли порты 443 и 80.|
|4 | Настройка доменов и портов | [Убедитесь, что ваши домены и порты настроены правильно](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Для правильной работы разъема существуют определенные порты, которые должны быть открыты, и URL-адреса, к которым должен иметь доступ сервер. |
|5 | Проверьте, используется ли прокси-сервер бэк-энда | Проверьте, используют ли разъемы серверы бэк-энда или обходят их в обход. Для получения подробной информации смотрите [проблемы с подключением к серверу Troubleshoot и проблемы с подключением к сервису.](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues) |
|6 | Обновление разъема и обновления для использования бэк-энда прокси | Если используется прокси-сервер бэк-энда, необходимо убедиться, что разъем использует тот же прокси. Подробную информацию о устранении неполадок и настройке разъемов для работы с прокси-серверами можно найти в [Work с существующими прокси-серверами.](application-proxy-configure-connectors-with-proxy-servers.md) |
|7 | Загрузите внутренний URL приложения на сервер разъема | На сервере разъема загрузите внутренний URL-адрес приложения. |
|8 | Проверка внутреннего подключения к сети | Во внутренней сети есть проблема подключения, которую этот поток отладки не может диагностировать. Приложение должно быть доступно внутренне для работы разъемов. Вы можете включить и просмотреть журналы событий разъема, описанные в [разъемах Application Proxy.](application-proxy-connectors.md#under-the-hood) |
|9 | Удлините значение тайм-аута на задней части | В **дополнительных настройках** для приложения измените настройку **тайм-аута backend** на **Long.** Добавьте [в Azure AD приложение](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)для предварительного просмотра. |
|10 | Если проблемы сохраняются, нацеливайте конкретные проблемы потока, просматривайте приложения и потоки отладки SSO | Используйте [приложение Приложение Приложение Debug Proxy, проблемы](application-proxy-debug-apps.md) с устранением неполадок. |

## <a name="next-steps"></a>Дальнейшие действия


* [Публикация приложений в отдельных сетях и расположениях с помощью групп соединителей](application-proxy-connector-groups.md)
* [Работа с имеющимися локальными прокси-серверами](application-proxy-configure-connectors-with-proxy-servers.md)
* [Устранение неполадок и сообщения об ошибках прокси приложения](application-proxy-troubleshoot.md)
* [Автоматическая установка соединителя прокси приложения Azure AD](application-proxy-register-connector-powershell.md)
