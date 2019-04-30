---
title: Создание пула узла Апробация виртуального рабочего стола Windows с помощью шаблона Azure Resource Manager — Azure
description: Как создать кластер узла в Апробация виртуального рабочего стола Windows с помощью шаблона Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870563"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Создание пула узлов с помощью шаблона Azure Resource Manager

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента предварительной версии Виртуального рабочего стола Windows. Каждый пул узлов может содержать группы приложений, с которыми пользователи могут взаимодействовать, как с физическим рабочим столом.

Следуйте инструкциям в этом разделе, чтобы создать пул узлов для клиента виртуальный рабочий стол Windows с помощью шаблона Azure Resource Manager, предоставляемые корпорацией Майкрософт. В этой статье о том, как создать пул узла в виртуальный рабочий стол Windows, создайте группу ресурсов с виртуальными машинами в подписке Azure, присоединение этих виртуальных машин к домену AD и зарегистрировать виртуальные машины с помощью виртуального рабочего стола Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Что необходимо для выполнения шаблона Azure Resource Manager

Убедитесь, что перед запуском шаблона Azure Resource Manager, вы знаете следующее:

- Где находится источник изображения, который вы хотите использовать. Его из коллекции Azure или это пользовательские?
- Your domain join credentials.
- Учетные данные виртуальному рабочему столу Windows.

При создании пула узла виртуального рабочего стола Windows с помощью шаблона Azure Resource Manager, можно создать виртуальную машину из коллекции Azure, образ управляемого или неуправляемого образа. Дополнительные сведения о создании образов виртуальных Машин, см. в разделе [Подготовка к отправке в Azure виртуального жесткого диска Windows или VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) и [Создание управляемого образа универсальной виртуальной машины в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Запустите шаблон Azure Resource Manager для подготовки пула узла

Чтобы начать, перейдите к [этот URL-адрес GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Развертывание шаблона в Azure

Если вы развертываете в подписке Enterprise, прокрутите вниз и выберите **развертывание в Azure**, затем skip вперед, заполните параметры на основе вашего образа источника.

Если вы развертываете в подписке поставщика облачных решений, выполните следующие действия для развертывания в Azure.

1. Прокрутите вниз и щелкните правой кнопкой мыши **развертывание в Azure**, а затем выберите **Копировать URL-адрес**.
2. Откройте текстовый редактор, например Блокнот и вставьте link.
3. Сразу же после "https://portal.azure.com/" и перед хештега (#) введите знак (@) и имя домена клиента. Ниже приведен пример формата, следует использовать: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Войдите на портал Azure как пользователь с правами администратора или участника для подписки поставщика облачных решений.
5. Вставьте ссылку, скопированный в текстовый редактор в адресную строку.

Рекомендации, о какие параметры следует ввести для вашего сценария, см. в разделе виртуального рабочего стола Windows [файл Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Файл сведений всегда обновляется с последними изменениями.

## <a name="assign-users-to-the-desktop-application-group"></a>Назначить пользователей к группе настольных приложений

По завершении выполнения шаблона GitHub Azure Resource Manager для назначения доступа пользователя перед началом тестирования полный сеансовых рабочих столов на виртуальных машинах.

Сначала [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell (если вы еще это не сделали).

Чтобы назначить пользователей к группе настольных приложений, откройте окно PowerShell и выполните этот командлет для входа в среде виртуального рабочего стола Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Затем задайте контекст в группу клиента, указанный в шаблоне Azure Resource Manager с помощью этого командлета:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

После этого добавьте пользователей в группу настольного приложения с помощью этого командлета:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Имя участника-пользователя должно соответствовать удостоверению пользователя в Azure Active Directory (например, user1@contoso.com). Если вы хотите назначить группу нескольким пользователям, запустите этот командлет для каждого из них.

Когда вы выполните эти действия, пользователи, которым назначена группа классических приложений, смогут войти в Виртуальный рабочий стол Windows с помощью поддерживаемых клиентов удаленного рабочего стола, чтобы просматривать ресурсы для рабочего стола сеанса.

>[!IMPORTANT]
>Чтобы усилить защиту среды Виртуального рабочего стола Windows в Azure, мы рекомендуем не открывать входящий порт 3389 для виртуальных машин. Виртуальный рабочий стол Windows не требует открытия входящего порта 3389, чтобы пользователи могли получить доступ к виртуальным машинам пула узла. Если вам все же нужно открыть порт 3389 для устранения неполадок, мы рекомендуем использовать[JIT-доступ к виртуальным машинам](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).