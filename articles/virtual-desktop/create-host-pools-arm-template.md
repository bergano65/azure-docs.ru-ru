---
title: Создание пула узла Апробация виртуального рабочего стола Windows с помощью шаблона Azure Resource Manager — Azure
description: Как создать кластер узла в Апробация виртуального рабочего стола Windows с помощью шаблона Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 262ec35a8a177652dff12bccb3b5435cb5856d81
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401413"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Создание пула узлов с помощью шаблона Azure Resource Manager

Пулы узлов представляют собой коллекцию один или несколько идентичных виртуальных машин в среде клиента Апробация виртуального рабочего стола Windows. Каждый пул узлов может содержать группы приложений, пользователи могут взаимодействовать, как если бы на рабочем столе физического.

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

Во-первых, [Загрузите и импортируйте модуль PowerShell для виртуальных рабочих столов Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) для использования в сеансе PowerShell, если у вас еще нет.

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

Имя участника-пользователя должно соответствовать удостоверение пользователя в Azure Active Directory (например, user1@contoso.com). Если вы хотите добавить нескольких пользователей, необходимо запустить этот командлет для каждого пользователя.

После выполнения этих действий, пользователи, добавленные в группы приложений рабочего стола можно войти в виртуальный рабочий стол Windows с помощью поддерживаемых клиентов удаленного рабочего стола и см. в разделе ресурсов для настольных компьютеров сеанса.
