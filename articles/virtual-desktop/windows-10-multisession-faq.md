---
title: Многосессионная часто задаваемые вопросы для windows 10 Enterprise - Azure
description: Часто задаваемые вопросы и рекомендации по использованию windows 10 Enterprise многосессионно для Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637099"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Windows 10 Корпоративная с поддержкой нескольких сеансов: часто задаваемые вопросы

В этой статье часто задаются вопросы и разъясняются рекомендации для многосессионно-сессионных Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Что такое многосессионная Windows 10 Enterprise?

Windows 10 Enterprise multi-session, ранее известная как Windows 10 Enterprise для виртуальных настольных компьютеров (EVD), является новым удаленным рабочим и сектотом, который позволяет проводить несколько параллельных интерактивных сеансов. Ранее это мог сделать только Windows Server. Эта возможность дает пользователям знакомый опыт Windows 10, в то время как ИТ-специалисты могут воспользоваться преимуществами затрат многосессионно-сессионного и использовать существующее лицензирование Windows для пользователей вместо лицензий доступа к клиентам RDS (CALs). Для получения дополнительной информации о лицензиях и ценах, см [Windows Виртуальный настольный цены](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Сколько пользователей могут одновременно иметь интерактивную сессию на Windows 10 Enterprise многосессионно?

Сколько интерактивных сеансов, которые могут быть активны одновременно, зависит от аппаратных ресурсов вашей системы (vCPU, память, диск и vGPU), как пользователи используют свои приложения при входе в сессию, и насколько тяжела рабочая нагрузка вашей системы. Мы предлагаем вам проверить производительность вашей системы, чтобы понять, сколько пользователей вы можете иметь на Windows 10 Enterprise многосессии. Чтобы узнать больше, смотрите [Windows Virtual Desktop цены](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Почему мое приложение сообщает о многосессионном Windows 10 Enterprise как об операционной системе Сервера?

Windows 10 Enterprise multi-session — это виртуальное издание Windows 10 Enterprise. Одно из отличий заключается в том, что эта операционная система (ОС) сообщает [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) как имеющий значение 3, такое же значение, как Windows Server. Это свойство обеспечивает совместимость ОС с существующими инструментами управления RDSH, многосессионными приложениями RDSH и в основном низкоуровневыми оптимизациями производительности системы для сред RDSH. Некоторые установки приложений могут блокировать установку на Windows 10 в зависимости от того, обнаруживают ли они, что ProductType настроен на клиента. Если приложение не будет устанавливаться, обратитесь к поставщику приложений для получения обновленной версии. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Могу ли я запустить многосессионную windows 10 Enterprise в непредпомянене?

Многосессионная сессия Windows 10 Enterprise не может работать в предприимчивых производственных средах, так как она оптимизирована для службы Windows Virtual Desktop для Azure. Это противоречит лицензионном соглашению о запуске многосессионной windows 10 Enterprise за пределами Azure для производственных целей. Многосессионная сессия Windows 10 Enterprise не активируется против служб управления ключами (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Как настроить многосессионное изображение Windows 10 Enterprise для моей организации?

Вы можете запустить виртуальную машину (VM) в Azure с Windows 10 Windows 10 Enterprise многосессионно и настроить ее, установив LOB-приложения, sysprep/generalize, а затем создать изображение с помощью портала Azure.  
 
Чтобы начать работу, создайте VM в Azure с многосессионным Windows 10 Enterprise. Вместо того, чтобы запускать VM в Azure, вы можете скачать VHD напрямую. После этого вы сможете использовать загруженный VHD для создания нового поколения 1 VM на ПК с Windows 10 с включенным Hyper-V.

Настройте изображение под свои нужды, установив LOB-приложения и сиспреп изображение. Когда вы закончите настройку, загрузите изображение в Azure с VHD внутри. После этого получите windows Virtual Desktop из Azure Marketplace и используйте его для развертывания нового пула хоста с настраиваемым изображением.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Как управлять многосессионным Windows 10 Enterprise после развертывания?

Вы можете использовать любой поддерживаемый инструмент конфигурации, но мы рекомендуем версию Configuration Manager 1906, поскольку она поддерживает многосессию Windows 10 Enterprise. В настоящее время мы работаем над поддержкой Microsoft Intune.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Можно ли к мультисессионным мультисессионным каталогам Azure 10 быть примкнунным к Windows 10?

Многосессионная windows 10 Enterprise в настоящее время поддерживается как гибридная часть Azure AD. После того, как мультисессия Windows 10 Enterprise будет соединена с доменами, используйте существующий объект групповой политики для включения регистрации Azure AD. Для получения дополнительной информации смотрите в составе [гибридной azure Active Directory.](../active-directory/devices/hybrid-azuread-join-plan.md)
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Где я могу найти многосессионное изображение Windows 10 Enterprise?

Многосессионная версия Windows 10 Enterprise находится в галерее Azure. Чтобы найти его, перейдите на портал Azure и ищите Windows 10 Предприятие для виртуальных настольных компьютеров релиз. Для получения изображения, интегрированного с Office Pro Plus, перейдите на портал Azure и ищите Microsoft Windows 10 и Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Какое многосессионное изображение Windows 10 Enterprise следует использовать?

Галерея Azure имеет несколько релизов, в том числе Windows 10 Enterprise многосессионная, версия 1809, и Windows 10 Enterprise многосессионная, версия 1903. Мы рекомендуем использовать последнюю версию для повышения производительности и надежности.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Какие версии для многосессионных Windows 10 Enterprise поддерживаются?

Windows 10 Enterprise многосессионная, версии 1809 и позже поддерживаются и доступны в галерее Azure. Эти релизы следуют той же политике жизненного цикла поддержки, что и Windows 10 Enterprise, что означает, что весенний релиз поддерживается в течение 18 месяцев и осенний релиз в течение 30 месяцев.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Какое решение для управления профилями следует использовать для многосессионных Windows 10 Enterprise?

Мы рекомендуем использовать контейнеры профиля FSLogix при настройке Windows 10 Enterprise в непостоянных средах или других сценариях, которые нуждаются в централизованном профиле. FSLogix гарантирует доступность профиля пользователя для каждого сеанса пользователя. Мы также рекомендуем использовать контейнер профиля FSLogix для хранения профиля пользователя в любом канале SMB с соответствующими разрешениями, но при необходимости вы можете хранить профили пользователей в хранилище каблов страницы Azure. Пользователи Windows Virtual Desktop могут использовать FSLogix без каких-либо дополнительных затрат.
 
Для получения дополнительной информации о том, как настроить контейнер профиля FSLogix, [см.](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Какая лицензия нужна для доступа к многосессионной Windows 10 Enterprise?

Полный список применимых лицензий можно узнать из [окон, цены на Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Почему мои приложения исчезают после того, как я вырегистриваюсь?

Это происходит потому, что вы используете Windows 10 Enterprise многосессионное с решением управления профилем, как FSLogix. Решение администратора или профиля настроило систему для удаления профилей пользователей при выходе из системы. Эта конфигурация означает, что когда ваша система удаляет ваш профиль пользователя после выхода из системы, она также удаляет все приложения, установленные во время сеанса. Если вы хотите сохранить установленные приложения, вам необходимо попросить вашего админа предоставить эти приложения для всех пользователей в среде Windows Virtual Desktop.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Как убедиться, что приложения не исчезают при выходе пользователей?

Большинство виртуализированных сред настроены по умолчанию, чтобы предотвратить установку пользователями дополнительных приложений в свои профили. Если вы хотите убедиться, что приложение не исчезает, когда пользователь выходит из Windows Virtual Desktop, вы должны предоставить это приложение для всех профилей пользователей в вашей среде. Для получения дополнительной информации о подготовке приложений, ознакомьтесь с этими ресурсами:

- [Публикация встроенных приложений в Windows Virtual Desktop](publish-apps.md)
- [Пакет приложений DISM, обслуживающий параметры командной строки](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Дополнение-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Как убедиться, что пользователи не загружают и не устанавливают приложения из Microsoft Store?

Вы можете отключить приложение Microsoft Store, чтобы убедиться, что пользователи не загружаются дополнительные приложения за пределы приложений, которые вы уже подготовлены для них.

Чтобы отключить приложение Store:

1. Создайте новую групповую политику.
2. Выберите**компоненты административной** >  **конфигурации** > компьютера**Windows.**
3. Выберите **Магазин**.
4. Выберите **приложение Магазина**.
5. Выберите **Инвалиды,** затем выберите **OK**.
6. Нажмите кнопку **Применить**.
 
## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о Windows Virtual Desktop и Windows 10 Enterprise multi-session:

- Ознакомьте нашу [документацию по предварительному просмотру рабочего стола Windows Virtual Desktop](overview.md)
- Посетите наш [Windows Виртуальный настольный TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Настройка развертывания виртуального рабочего стола Windows с [помощью учебников Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
