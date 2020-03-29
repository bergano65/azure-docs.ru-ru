---
title: Персональный пул для настольных компьютеров Windows Azure Resource Manager - Azure
description: Как создать пул хоста в Windows Virtual Desktop с шаблоном Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292328"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Создание пула узлов с помощью шаблона Azure Resource Manager

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Каждый пул узлов может содержать группы приложений, с которыми пользователи могут взаимодействовать, как с физическим рабочим столом.

Следуйте инструкциям этого раздела по созданию пула хостов для наемного клиента Windows Virtual Desktop с шаблоном Azure Resource Manager, предоставленным корпорацией Майкрософт. В этой статье будет усваивательство пула в Windows Virtual Desktop, создать группу ресурсов с виртуальными вам в подписке Azure, присоединиться к этим виртуальным вам в домен aD и зарегистрировать виртуальные компьютеры с Помощью Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Предварительные требования для запуска шаблона Azure Resource Manager

Убедитесь, что перед запуском шаблона Azure Resource Manager выможете узнать следующие вещи:

- Где источник изображения, который вы хотите использовать. Это из галереи Azure или это обычай?
- Ваш домен соединяет учетные данные.
- Ваши учетные данные виртуального рабочего стола Windows.

При создании пула хостов Windows Virtual Desktop с шаблоном Azure Resource Manager можно создать виртуальную машину из галереи Azure, управляемое изображение или неуправляемое изображение. Чтобы узнать больше о том, как создавать VM-изображения, [Create a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md)см. [Prepare a Windows VHD or VHDX to upload to Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Выполнить шаблон менеджера ресурсов Azure для подготовки нового пула хоста

Для начала перейдите на [этот URL-адрес GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)

### <a name="deploy-the-template-to-azure"></a>Развертывание шаблона в Azure

Если вы развертываете в подписке Enterprise, прокрутите вниз и выберите **Развертывание в Azure,** а затем пропустите заранее заполнить параметры на основе источника изображения.

Если вы развертываете в подписке поставщика облачных решений, выполните следующие действия для развертывания в Azure:

1. Прокрутите страницу вниз и щелкните правой кнопкой мыши **Развертывание в Azure**, а затем выберите **Copy Link Location** (Скопировать ссылку).
2. Откройте текстовый редактор, например Блокнот, и вставьте в него ссылку.
3. Сразу послеhttps://portal.azure.com/« и перед хэштегом (я) введите знак (кв) с последующим доменным именем арендатора. Вот пример формата, который вы `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`должны использовать: .
4. Войдите на портал Azure как пользователь с правами администратора или участника подписки поставщика облачных решений.
5. Вставьте скопированную в текстовый редактор ссылку в адресную строку.

Для руководства о том, какие параметры следует ввести [Readme file](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)для вашего сценария, см. Readme всегда обновляется с последними изменениями.

## <a name="assign-users-to-the-desktop-application-group"></a>Назначить пользователей в группу настольных приложений

После завершения работы шаблона GitHub Azure Resource Manager назначьте доступ к пользователю перед началом тестирования настольных компьютеров полного сеанса на виртуальных компьютерах.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/) для использования в сеансе PowerShell (если вы еще это не сделали).

Чтобы назначить пользователей в группу настольных приложений, откройте окно PowerShell и запустите этот cmdlet для вхлых в среду Windows Virtual Desktop:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

После этого добавьте пользователей в группу настольных приложений с помощью этого cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

UPN пользователя должен соответствовать идентификации пользователя в Active Directory Azure (например, user1@contoso.com). Если вы хотите назначить группу нескольким пользователям, запустите этот командлет для каждого из них.

Когда вы выполните эти действия, пользователи, которым назначена группа классических приложений, смогут войти в Виртуальный рабочий стол Windows с помощью поддерживаемых клиентов удаленного рабочего стола, чтобы просматривать ресурсы для рабочего стола сеанса.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](../security-center/security-center-just-in-time.md).