---
title: Устранение неполадок с расширением панели доступа Azure для Internet Explorer | Документация Майкрософт
description: Как применить групповую политику для развертывания надстройки Internet Explorer для работы с порталом «Мои приложения».
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565033"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Устранение неполадок с расширением панели доступа для Internet Explorer

Эта статья поможет устранить следующие проблемы.

* Не удается получить доступ к приложениям через портал «Мои приложения» при использовании Internet Explorer.
* Отображается сообщение «Установка программного обеспечения», хотя программное обеспечение уже установлено.

Если вы являетесь администратором, см. в разделе [развертывание расширения панели доступа для Internet Explorer с помощью групповой политики](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Запуск средства диагностики

Вы сможете диагностировать неполадки установки с расширением панели доступа, загрузив и запустив средство диагностики панели доступа. 

Чтобы скачать и установить средство диагностики:

1. [Щелкните эту ссылку, чтобы загрузить средство диагностики.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Откройте файл и извлеките его содержимое на компьютер.
   
3. Чтобы запустить средство, щелкните правой кнопкой мыши файл с именем *AccessPanelExtensionDiagnosticTool.js* и выберите **открыть с помощью** > **сервера на основе сценариев Microsoft Windows** .
   
    ![Открыть с помощью > Сервер сценариев на базе Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Просмотрите результаты диагностики, которые появляются и выберите **Да** устранить проблемы. **Проверьте результаты** откроется диалоговое окно с информацией о том, что делать, если расширение не работает.  

5. Прочитайте сообщение и выберите **ОК**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Убедитесь, что расширение панели доступа включено.

Чтобы убедиться, что вы включили расширение панели доступа в Internet Explorer:

1. В Internet Explorer, выберите **значок шестеренки** в правом верхнем углу окна и выберите **обозревателя**.
   
2. Перейдите к **программы** и выберите **Управление надстройками**.
   
3. Выберите **расширение панели доступа** в **Корпорация Майкрософт** и выберите **включить**.
   
4. Чтобы сохранить изменения, закройте все окна браузера Internet Explorer открыта. Изменения вступят в силу при следующем открытии Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Включение расширений для просмотра InPrivate

Чтобы включить расширения для просмотра InPrivate:

1. В Internet Explorer, выберите **значок шестеренки** в правом верхнем углу окна и выберите **обозревателя**.
   
2. Перейдите к **конфиденциальности** и убедитесь, что **отключить панели инструментов и расширения при запуске просмотра InPrivate** флажок снят.
   
3.  Чтобы сохранить изменения, закройте все окна браузера Internet Explorer открыта. Изменения вступят в силу при следующем открытии Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Удаление расширения панели доступа

Удаление расширения панели доступа с компьютера.

1. В панели управления найдите *удаление*. 

2. В результатах поиска выберите **удаление программы**.
   
    ![Поиск программы удаления.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. В списке, выберите **расширение панели доступа** и выберите **удаления**.

    ![Удалите расширение панели доступа.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Затем попробуйте установить расширение еще раз, чтобы проверить, удалось ли решить проблему.

Если возникли проблемы при удалении расширения можно также удалить его с помощью [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) средство.

## <a name="related-articles"></a>Связанные статьи
* [Доступ к приложениям и единый вход с помощью Azure Active Directory](what-is-single-sign-on.md)
* [Развертывание расширения панели доступа для Internet Explorer с помощью групповой политики](deploy-access-panel-browser-extension.md)

