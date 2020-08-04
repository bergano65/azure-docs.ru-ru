---
title: Новые возможности в Виртуальном рабочем столе Windows — Azure
description: Новые функции и обновления продуктов для Виртуального рабочего стола Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 9be1053600ab89a7879a04a7c08a44ddf3bc862a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291245"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Новые возможности в Виртуальном рабочем столе Windows

Виртуальный рабочий стол Windows регулярно обновляется. В этой статье содержатся следующие сведения:

- последние обновления;
- новые функции;
- усовершенствования имеющихся функций;
- Исправления ошибок

Эта статья обновляется ежемесячно. Регулярно посещайте эту ссылку, чтобы не пропустить новые обновления.

## <a name="june-2020"></a>Июнь 2020 г.

В прошлом месяце мы выпустили предварительную версию Виртуального рабочего стола Windows с интеграцией с Azure Resource Manager. Это обновление содержит множество интересных функций, о которых мы рады вам сообщить. Вот какие новые возможности появились в этой версии Виртуального рабочего стола Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Виртуальный рабочий стол Windows теперь интегрирован с Azure Resource Manager (предварительная версия)

Виртуальный рабочий стол Windows теперь интегрирован в Azure Resource Manager. В последнем обновлении все объекты Виртуальных рабочих столов Windows теперь являются ресурсами Azure Resource Manager. Это обновление также интегрировано с элементами управления доступом на основе ролей (RBAC) Azure. Сведения об Azure Resource Manager см. на [этой странице](../azure-resource-manager/management/overview.md).

Преимущества данного обновления:

- Виртуальный рабочий стол Windows теперь интегрирован с порталом Azure. Это означает, что вы можете управлять любыми данными непосредственно на портале, без PowerShell, веб-приложений или средств сторонних разработчиков. Чтобы приступить к работе, ознакомьтесь с нашим учебником по [созданию пула узлов с помощью портала Azure](create-host-pools-azure-marketplace.md).

- До выпуска этого обновления можно было публиковать только удаленные приложения RemoteApp и рабочие столы для отдельных пользователей. С помощью Azure Resource Manager теперь можно публиковать ресурсы в группах Azure Active Directory.

- В предыдущей версии Виртуального рабочего стола Windows имелись четыре встроенные роли администратора, которые можно было назначить клиенту или пулу узлов. Теперь эти роли доступны в [управлении доступом на основе ролей](../role-based-access-control/overview.md) Azure. Эти роли можно применять к каждому объекту Azure Resource Manager Виртуального рабочего стола Windows, что обеспечивает наличие комплексной модели расширенного делегирования.

- Это обновление устранило необходимость многократно запускать Azure Marketplace или шаблон GitHub для расширения пула узлов. Все, что нужно для расширения пула узлов, — перейти к пулу узлов на портале Azure и нажать кнопку **+ Add** (+ Добавить), чтобы развернуть дополнительные узлы сеансов.

- Развертывание пула узлов теперь полностью интегрировано с [Общей коллекцией образов Azure](../virtual-machines/windows/shared-image-galleries.md). Общая коллекция образов Azure — это отдельная служба Azure, в которой хранятся определения образов виртуальных машин, включая управление версиями образов. Глобальную репликацию также можно использовать для копирования и отправки образов в другие регионы Azure для локального развертывания.

- Функции мониторинга, которые выполняются с помощью PowerShell или веб-приложения службы диагностики, теперь перемещены в Log Analytics на портале Azure. Кроме того, теперь для вас доступны два варианта визуализации отчетов. Вы можете выполнять запросы Kusto и использовать Workbooks для создания визуальных отчетов.

- Больше не требуется выполнять согласие Azure Active Directory (Azure AD) для использования Виртуального рабочего стола Windows. В этом обновлении арендатор Azure AD в вашей подписке Azure аутентифицирует пользователей и предоставляет элементы управления RBAC для администраторов.


### <a name="powershell-support"></a>Поддержка PowerShell

В это обновление добавлены новые командлеты AzWvd для модуля Azure PowerShell Az. Этот новый модуль поддерживается в PowerShell Core, работающем на платформе .NET Core.

Чтобы установить модуль, следуйте инструкциям, приведенным на странице [Настройка модуля PowerShell для Виртуального рабочего стола Windows](powershell-module.md).

Список доступных команд можно также найти в справочнике по использованию [AzWvd в PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Дополнительные сведения о новых возможностях см. в [записи нашего блога](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Дополнительные шлюзы

Мы добавили новый кластер шлюзов в Южной Африке, чтобы снизить задержку подключения.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams в Виртуальном рабочем столе Windows (предварительная версия)

Мы внесли некоторые улучшения в Microsoft Teams для Виртуального рабочего стола Windows. Самое главное, что Виртуальный рабочий стол Windows теперь поддерживает перенаправление аудио и видео для вызовов. Перенаправление уменьшает задержку путем создания прямых путей между пользователями при вызове с помощью аудио или видео. Меньшее расстояние означает меньшее количество прыжков, что делает звук и видео более плавными.

Дополнительные сведения см. в [записи блога](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о планах на будущее на странице [Дорожная карта Microsoft 365: Виртуальный рабочий стол Windows](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Ознакомьтесь с этими статьями, чтобы узнать об обновлениях Виртуального рабочего стола Windows и службы удаленных рабочих столов:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
