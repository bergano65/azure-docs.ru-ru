---
title: Используйте шаблон, чтобы присоединиться к Windows VM к Azure AD DS Документы Майкрософт
description: Узнайте, как использовать шаблоны azure Resource Manager для присоединения к новому или существующему Windows Server VM к домену с управлением ресурсами Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: d2108b4c6b81675e2df6789d412dbd7d36f58a4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655109"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Присоединиться к виртуальной машине Windows Server к домену с управлением активным идоменом Azure Active Directory Services с помощью шаблона диспетчера ресурсов

Для автоматизации развертывания и конфигурации виртуальных машин Azure (VM) можно использовать шаблон управления ресурсами. Эти шаблоны позволяют создавать последовательные развертывания каждый раз. Расширения также могут быть включены в шаблоны для автоматической настройки VM как части развертывания. Одно полезное расширение соединяет вс-множец с доменом, который можно использовать с помощью доменов Azure Active Directory Domain Services (Azure AD DS), управляемых доменами.

В этой статье показано, как создать и присоединиться к Windows Server VM к домену Azure AD DS, управляемому dS с использованием шаблонов Resource Manager. Вы также узнаете, как присоединиться к существующему VM Windows Server к домену Azure AD DS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуются следующие ресурсы и разрешения:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, по инструкциям из первого учебника [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Учетная запись пользователя, войдя часть домена Azure AD DS.

## <a name="azure-resource-manager-template-overview"></a>Обзор шаблона управления ресурсами Azure

Шаблоны менеджера ресурсов позволяют определить инфраструктуру Azure в коде. Необходимые ресурсы, сетевые соединения или конфигурация ВМ могут быть определены в шаблоне. Эти шаблоны создают согласованные, воспроизводимые развертывания каждый раз и могут быть versioned при внесении изменений. Для получения дополнительной информации смотрите [обзор шаблонов шаблонов управления ресурсами Azure.][template-overview]

Каждый ресурс определяется в шаблоне с помощью обозначения объектов JavaScript (JSON). В следующем примере JSON используется тип *ресурса Microsoft.Compute/virtualMachines/extensions* для установки расширения домена Active Directory. Используются параметры, указанные во время развертывания. При развертывании расширения VM присоединяется к указанному домену Azure AD DS.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Это расширение VM может быть развернуто, даже если вы не создаете VM в том же шаблоне. Примеры в этой статье показывают оба следующих подхода:

* [Создайте VM Windows Server и присоединитесь к управляемому домену](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Присоединение существующей виртуальной машины Windows Server к управляемому домену](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Создайте VM Windows Server и присоединитесь к управляемому домену

Если вам нужен Windows Server VM, вы можете создать и настроить его с помощью шаблона управления ресурсами. При развертывании VM для присоединения VM к домену Azure AD DS устанавливается расширение. Если у вас уже есть VM, к которому вы хотите присоединиться к домену Azure AD DS, перейдите [к существующему Домену Windows Server VM в управляемый домен.](#join-an-existing-windows-server-vm-to-a-managed-domain)

Чтобы создать VM Windows Server, затем присоединитесь к домену Azure AD DS, выполните следующие шаги:

1. Просмотрите [шаблон quickstart.](https://azure.microsoft.com/resources/templates/201-vm-domain-join/) Выберите опцию развертывания в **Azure.**
1. На странице **пользовательского развертывания** введите следующую информацию для создания и присоединения К исключаемому домену Windows Server VM к домену Azure AD DS:

    | Параметр                   | Значение |
    |---------------------------|-------|
    | Подписка              | Выберите ту подписку Azure, в которой включены доменные службы Azure AD. |
    | Группа ресурсов            | Выберите группу ресурсов для вашего VM. |
    | Местоположение                  | Выберите место расположение для вашего VM. |
    | Существующее имя VNET        | Название существующей виртуальной сети для подключения VM к, *например, myVnet*. |
    | Существующее название подсети      | Название существующей виртуальной сетевой подсети, например *Workloads.* |
    | Префикс этикетки DNS          | Введите имя DNS для использования для VM, например *myvm.* |
    | Размер виртуальной машины                   | Укажите размер VM, *например, Standard_DS2_v2*. |
    | Домен для соединения            | Azure AD DS управлял доменом DNS имя, *например, aaddscontoso.com*. |
    | Имя пользователя домена           | Учетная запись пользователя в домене Azure AD DS, который должен использоваться `contosoadmin@aaddscontoso.com`для присоединения VM к управляемому домену, например Эта учетная запись должна быть частью домена Azure AD DS, управляемого DS. |
    | Пароль домена           | Пароль для учетной записи пользователя, указанный в предыдущем параметре. |
    | Дополнительный путь OU          | Пользовательский OU, в котором добавить VM. Если вы не укажете значение для этого параметра, VM добавляется к *AAD DC Computers* OU по умолчанию. |
    | Имя пользователя VM Admin         | Укажите учетную запись локального администратора для создания на VM. |
    | VM Админ Пароль         | Укажите пароль локального администратора для VM. Создайте надежный пароль локального администратора для защиты от атак грубой силы паролей. |

1. Просмотрите условия, а затем проверить **поле, я согласен с условиями, изложенными выше**. При готовности выберите **«Покупка»** для создания и присоединения К VM к домену Azure AD DS.

> [!WARNING]
> **С осторожностью обращайтесь с паролями.**
> Файл параметров шаблона запрашивает пароль для учетной записи пользователя, которая является частью домена Azure AD DS. Не вводите значения вручную в этот файл и не оставляет его доступным в файловых долях или других общих местах.

Для успешного завершения развертывания требуется несколько минут. После завершения работа создается и присоединяется к домену Azure AD DS. VM можно управлять или подписать с помощью доменных учетных записей.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Присоединение существующей виртуальной машины Windows Server к управляемому домену

Если у вас есть существующий VM или группа вс-м, которые вы хотите присоединиться к домену Azure AD DS, вы можете использовать шаблон менеджера ресурсов, чтобы просто развернуть расширение VM.

Чтобы присоединиться к существующему VM Windows Server VM к домену Azure AD DS, выполните следующие шаги:

1. Просмотрите [шаблон quickstart.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) Выберите опцию развертывания в **Azure.**
1. На странице **пользовательского развертывания** введите следующую информацию, чтобы присоединиться к VM в домене Azure AD DS:

    | Параметр                   | Значение |
    |---------------------------|-------|
    | Подписка              | Выберите ту подписку Azure, в которой включены доменные службы Azure AD. |
    | Группа ресурсов            | Выберите группу ресурсов с существующим VM. |
    | Местоположение                  | Выберите местоположение существующего VM. |
    | Список ВМ                   | Введите список, разделенный на запятой существующего VM(ы), чтобы присоединиться к управляемому домену Azure AD DS, например *myVM1,myVM2.* |
    | Домен Присоединиться к имени пользователя     | Учетная запись пользователя в домене Azure AD DS, который должен использоваться `contosoadmin@aaddscontoso.com`для присоединения VM к управляемому домену, например Эта учетная запись должна быть частью домена Azure AD DS, управляемого DS. |
    | Домен Присоединиться к паролю пользователя | Пароль для учетной записи пользователя, указанный в предыдущем параметре. |
    | Дополнительный путь OU          | Пользовательский OU, в котором добавить VM. Если вы не укажете значение для этого параметра, VM добавляется к *AAD DC Computers* OU по умолчанию. |

1. Просмотрите условия, а затем проверить **поле, я согласен с условиями, изложенными выше**. При готовности выберите **«Покупка»,** чтобы присоединиться к VM в домене Azure AD DS.

> [!WARNING]
> **С осторожностью обращайтесь с паролями.**
> Файл параметров шаблона запрашивает пароль для учетной записи пользователя, которая является частью домена Azure AD DS. Не вводите значения вручную в этот файл и не оставляет его доступным в файловых долях или других общих местах.

Для успешного завершения развертывания требуется несколько минут. После завершения, указанные Windows VMs присоединяются к домену Azure AD DS и могут управляться или подписаны с помощью учетных записей домена.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы использовали портал Azure для настройки и развертывания ресурсов с помощью шаблонов. Вы также можете развертывать ресурсы с шаблонами менеджера ресурсов с помощью [Azure PowerShell][deploy-powershell] или [Azure CLI.][deploy-cli]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
