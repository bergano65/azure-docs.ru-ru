---
title: Устранение неполадок расширения панели доступа Azure для IE (ru) Документы Майкрософт
description: Как применить групповую политику для развертывания надстройки Internet Explorer для работы с порталом «Мои приложения».
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723907"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Устранение проблем расширение панели доступа для Internet Explorer

Эта статья поможет устранить следующие проблемы.

* Не удается получить доступ к приложениям через портал «Мои приложения» при использовании Internet Explorer.
* Отображается сообщение «Установка программного обеспечения», хотя программное обеспечение уже установлено.

Если вы являетесь админ-аминтом, [см.](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>Запуск диагностического инструмента

Можно диагностировать проблемы с установкой с помощью расширения панели доступа, загрузив и запустив диагностический инструмент панели доступа. 

Для загрузки и установки диагностического инструмента:

1. [Выберите эту ссылку, чтобы загрузить диагностический инструмент.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Откройте файл и извлеките содержимое на компьютер.
1. Чтобы запустить инструмент, нажмите правой кнопкой мыши файл под названием *AccessPanelExtensionDiagnosticTool.js* и выберите **Открытый с** > **Microsoft Windows На основе скрипта host**.

    ![Открыть с помощью > Сервер сценариев на базе Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Просмотрите результаты диагностики, которые отображаются, и выберите **«Да»** для устранения проблем. В диалоговом окне **«Проверка результатов»** появляется информация о том, что делать, если расширение не работает.  
1. Прочитайте сообщение и выберите **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Убедитесь, что расширение панели доступа включено.

Чтобы убедиться, что вы включили расширение панели доступа в Internet Explorer:

1. В Internet Explorer выберите **значок Gear** в правом верхнем углу окна и выберите **варианты Интернета.**
1. Перейдите на вкладку **Программы** и выберите **дополнения Управления.**
1. Выберите **расширение панели доступа** в разделе **Корпорации Майкрософт** и выберите **Enable**.
1. Чтобы сохранить изменения, закройте все открытые окна браузера Internet Explorer. Изменение вступает в силу при следующем открытии Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Включить расширения для просмотра InPrivate

Для расширения для InPrivate Просмотр:

1. В Internet Explorer выберите **значок Gear** в правом верхнем углу окна и выберите **варианты Интернета.**
1. Перейдите на вкладку **Конфиденциальность** и убедитесь, что **отключить панели инструментов и расширений, когда InPrivate Просмотр начинает** сяпч окно ясно.
1. Чтобы сохранить изменения, закройте все открытые окна браузера Internet Explorer. Изменение вступает в силу при следующем открытии Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Удаление расширения панели доступа

Чтобы удалить расширение панели доступа с компьютера:

1. В панели управления поиск *для удаления*.
1. В результатах поиска выберите **Удалить программу.**

    ![Выберите опцию «Установить программу» из панели управления](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Из списка выберите **расширение панели доступа** и — **Uninstall**.

    ![Удаление расширения панели доступа](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Затем попробуйте установить расширение еще раз, чтобы проверить, удалось ли решить проблему.

Если вы столкнулись с проблемами, не устанавливающих расширение, вы также можете удалить его с помощью инструмента [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Связанные статьи

* [Доступ к приложениям и единый вход с помощью Azure Active Directory](what-is-single-sign-on.md)
* [Как развернуть расширение панели доступа для Internet Explorer с помощью групповой политики](deploy-access-panel-browser-extension.md)
