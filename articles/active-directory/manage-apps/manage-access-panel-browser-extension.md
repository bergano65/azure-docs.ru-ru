---
title: Устранение неполадок с расширением панели доступа Azure для IE | Документация Майкрософт
description: Как применить групповую политику для развертывания надстройки Internet Explorer для работы с порталом «Мои приложения».
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763267"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Устранение неполадок расширения панели доступа для Internet Explorer

Эта статья поможет устранить следующие проблемы.

* Не удается получить доступ к приложениям через портал «Мои приложения» при использовании Internet Explorer.
* Отображается сообщение «Установка программного обеспечения», хотя программное обеспечение уже установлено.

Если вы являетесь администратором, см. статью [Развертывание расширения панели доступа для Internet Explorer с помощью групповая политика](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Запуск средства диагностики

Проблемы с установкой расширения панели доступа можно диагностировать, загрузив и запустив средство диагностики панели доступа. 

Чтобы скачать и установить средство диагностики, выполните следующие действия.

1. [Щелкните эту ссылку, чтобы скачать средство диагностики.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Откройте файл и извлеките содержимое на свой компьютер.
1. Чтобы запустить средство, щелкните правой кнопкой мыши файл с именем *AccessPanelExtensionDiagnosticTool.js* и выберите пункт **Открыть с помощью**  >  **сервера сценариев на основе Microsoft Windows**.

    ![Открыть с помощью > Сервер сценариев на базе Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Проверьте отображаемые результаты диагностики и нажмите кнопку **Да** , чтобы устранить неполадки. Откроется диалоговое окно **результаты проверки** со сведениями о том, что делать, если расширение не работает.  
1. Прочтите сообщение и нажмите кнопку **ОК**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Убедитесь, что расширение панели доступа включено.

Чтобы проверить, включено ли расширение панели доступа в Internet Explorer, выполните следующие действия.

1. В Internet Explorer щелкните **значок шестеренки** в правом верхнем углу окна и выберите **Свойства браузера**.
1. Перейдите на вкладку **программы** и выберите пункт **Управление надстройками**.
1. Выберите **расширение панели доступа** в разделе " **Корпорация Майкрософт** " и нажмите кнопку **включить**.
1. Чтобы сохранить изменения, закройте все открытые окна браузера Internet Explorer. Изменение вступает в силу при следующем открытии Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Включить расширения для обзора InPrivate

Включение расширений для обзора InPrivate:

1. В Internet Explorer щелкните **значок шестеренки** в правом верхнем углу окна и выберите **Свойства браузера**.
1. Перейдите на вкладку **Конфиденциальность** и убедитесь, что флажок **отключить панели инструментов и расширения при просмотре InPrivate** снят.
1. Чтобы сохранить изменения, закройте все открытые окна браузера Internet Explorer. Изменение вступает в силу при следующем открытии Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Удаление расширения панели доступа

Чтобы удалить расширение панели доступа с компьютера, выполните следующие действия.

1. В панели управления выполните поиск по запросу *Удалить*.
1. В результатах поиска выберите **удалить программу**.

    ![Выберите параметр Удалить программу с помощью панели управления.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. В списке выберите **расширение панели доступа** и нажмите кнопку **Удалить**.

    ![Удаление расширения панели доступа](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Затем попробуйте установить расширение еще раз, чтобы проверить, удалось ли решить проблему.

Если при удалении расширения возникают проблемы, его также можно удалить с помощью средства [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Похожие статьи

* [Доступ к приложениям и единый вход с помощью Azure Active Directory](what-is-single-sign-on.md)
* [Развертывание расширения панели доступа для Internet Explorer с помощью групповая политика](deploy-access-panel-browser-extension.md)
