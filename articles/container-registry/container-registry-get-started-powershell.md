---
title: Краткое руководство. Создание частного реестра Docker в Azure с помощью PowerShell
description: Быстрый способ изучить создание частного реестра контейнеров Docker в Azure с помощью Azure PowerShell.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b8ff8e671d51a148177e66b30225dd7536a48028
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299749"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Краткое руководство. Создание частного реестра контейнеров Docker с помощью Azure PowerShell

Реестр контейнеров Azure — это управляемая служба частного реестра контейнеров Docker для создания, хранения и обслуживания образов контейнеров Docker. В этом руководстве рассматривается создание реестра контейнеров Azure с помощью PowerShell. Затем используйте команды Docker, чтобы отправить образ контейнера в реестр, после чего извлеките образ из контейнера и запустите его.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством требуется модуль Azure PowerShell 5.7.0 или более поздней версии. Выполните команду `Get-Module -ListAvailable AzureRM`, чтобы определить установленную версию. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

Также необходим локально установленный модуль Docker. Docker предоставляет пакеты для систем [macOS][docker-mac], [Windows][docker-windows] и [Linux][docker-linux].

Та как в службе Azure Cloud Shell нет всех необходимых компонентов Docker (управляющая программа `dockerd`), ее нельзя использовать в этом руководстве.

## <a name="sign-in-to-azure"></a>Вход в Azure

С помощью команды [Connect-AzureRmAccount][Connect-AzureRmAccount] войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

После регистрации в Azure создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Создание реестра контейнеров

Теперь создайте реестр контейнеров в новой группе ресурсов с помощью команды [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry].

Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. В следующем примере создается реестр с именем myContainerRegistry007. Замените имя *myContainerRegistry007* в следующей команде. Затем с ее помощью создайте реестр.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

В этом кратком руководстве описано, как создать реестр ценовой категории *Базовый*. Это оптимальный (недорогой) вариант для разработчиков, которые знакомятся с Реестром контейнеров Azure. См. дополнительные сведения о [доступных номерах SKU реестра контейнеров][container-registry-skus].

## <a name="log-in-to-registry"></a>Вход в раздел реестра

Перед отправкой и извлечением образов контейнеров необходимо войти в реестр. В рабочих сценариях для доступа к реестру контейнеров следует использовать индивидуальный идентификатор или субъект-службу, но чтобы упростить это краткое руководство, включите администратора в реестре с помощью команды [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential].

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Затем выполните команду [docker login][docker-login], чтобы войти в систему.

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

По завершении команда возвращает `Login Succeeded`.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с ресурсами, созданными в этом кратком руководстве, с помощью команды [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] вы можете удалить группу ресурсов, реестр контейнеров и сохраненные образы контейнеров.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы создали Реестр контейнеров Azure с использованием Azure PowerShell, отправили образ контейнера в него, а затем извлекли этот образ оттуда и запустили его. Чтобы продолжить работу с Реестром контейнеров Azure, перейдите к следующим руководствам.

> [!div class="nextstepaction"]
> [Руководства по использованию Реестра контейнеров Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRMContainerRegistry]: /powershell/module/azurerm.containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md