---
title: Развертывание набора масштабирования виртуальных машин с помощью Visual Studio
description: Развертывание набора масштабирования виртуальных машин с помощью Visual Studio и шаблона Resource Manager
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066962"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Как создать масштабируемый набор виртуальных машин с помощью Visual Studio

В этой статье показано, как развернуть набор виртуальной шкалы масштабов Azure с помощью развертывания группы ресурсов Visual Studio.

[Наборы виртуальных машин Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) — это ресурс Azure Compute для развертывания и управления коллекцией аналогичных виртуальных машин с автомасштабированием и балансировкой нагрузки. Масштабируемые наборы виртуальных машин можно подготавливать и развертывать с помощью [шаблонов Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Шаблоны Менеджер ресурсов Azure можно развертывать с помощью Azure CLI, PowerShell, REST, а также непосредственно из Visual Studio. Visual Studio предоставляет набор шаблонов примеров, которые можно развернуть в рамках проекта развертывания группы ресурсов Azure.

Развертывания группы ресурсов Azure позволяют сгруппировать несколько связанных ресурсов Azure и опубликовать их в одной операции развертывания. Для получения дополнительной информации [см. Создание и развертывание групп ресурсов Azure через Visual Studio.](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## <a name="prerequisites"></a>Предварительные требования

Для начала развертывания наборов виртуальной шкалы машин в Visual Studio необходимы следующие предпосылки:

* Visual Studio 2013 или более поздней версии
* Пакет SDK Azure версии 2.7, 2.8 или 2.9

>[!NOTE]
>Эта статья использует Visual Studio 2019 с [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Создание проекта<a name="creating-a-project"></a> 

1. Откройте Visual Studio и выберите **Создать проект**.

1. При **создании нового проекта**выберите **группу ресурсов Azure** для C, а затем выберите **Следующий.**

1. В **настройке нового проекта**введите имя и выберите **Create.**

    ![Название и создание проекта](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Из списка шаблонов выберите либо **набор виртуального масштаба машины Windows,** либо шаблон **Linux Virtual Machine Scale Set.** Нажмите кнопку **ОК**.

   ![Выберите шаблон виртуальной машины](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

После создания проекта **Solution Explorer** содержит сценарий развертывания PowerShell, шаблон управления ресурсами Azure и файл параметров для набора виртуальной шкалы машин.

## <a name="customize-your-project"></a>Настройка проекта

Теперь вы можете отсеивать шаблон, чтобы настроить его под потребности приложения. Можно добавить свойства расширения виртуальной машины или отсваить правила балансировки нагрузки. По умолчанию шаблоны настройки набора виртуальной шкалы машин настроены для развертывания расширения **AzureDiagnostics,** что упрощает добавление правил автоматического масштаба. Шаблоны также развертывают балансистер нагрузки с общедоступным IP-адресом, настроенным с входящими правилами NAT.

Балансилер нагрузки позволяет подключаться к экземплярам виртуальной машины с Помощью SSH (Linux) или RDP (Windows). Диапазон портов клиента начинается с 50000. Для Linux, если вы SSH порт 50000, загрузка балансировки маршрутов вам порт 22 из первой виртуальной машины в наборе масштаба. Подключение к порту 50001 направляется в порт 22 второй виртуальной машины и так далее.

 Хороший способ отсеивать шаблоны с помощью Visual Studio — использовать **JSON Outline.** Можно организовать параметры, переменные и ресурсы. Понимая схему, Visual Studio может указать на ошибки в шаблоне перед развертыванием.

![Обозреватель JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Развертывание проекта

Развертывание шаблона менеджера ресурсов Azure для создания ресурса Виртуального набора шкалы машин:

1. В **Solution Explorer**, правой кнопкой мыши проекта и выбрать **Развертывание** > **нового**.

    ![Развертывание проекта](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. В **развертывании в группе ресурсов**выберите, какую подписку использовать, и выберите группу ресурсов. При необходимости можно создать группу ресурсов.

1. Затем выберите **параметры отсвасывала** для ввода параметров, которые передаются шаблону.

   ![Введите группу подписки и ресурсов](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Предоставьте имя пользователя и пароль для операционной системы. Эти значения необходимы для создания развертывания. Если у вас нет установленных инструментов PowerShell Tools for Visual Studio, выберите **пароли Сохранить,** чтобы избежать скрытого запроса команды PowerShell или использовать [поддержку Key Vault.](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/) Выберите **Сохранить** для продолжения.

    ![Оторитепараметры развертывания](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. В **развертывании в группе ресурсов**выберите **Развертывание**. Действие выполняется скриптом **Deploy-AzureResourceGroup.ps1.** В окне **Вывод** отображается ход развертывания.

   ![Выход показывает результаты](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Исследуйте свой виртуальный набор масштабов машины<a name="exploring-your-virtual-machine-scale-set"></a>

Выберите **View** > **Cloud Explorer** для просмотра нового набора виртуальной шкалы машин. При необходимости используйте **Refresh All.**

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Облачный исследователь** позволяет управлять ресурсами Azure в Visual Studio при разработке приложений. Масштабируемый набор виртуальных машин также можно просмотреть на [портале Azure](https://portal.azure.com) и в [обозревателе ресурсов Azure](https://resources.azure.com/).

 Портал предоставляет наилучший способ управления инфраструктурой Azure с помощью веб-браузера. Azure Resource Explorer предоставляет простой способ изучения и отладки ресурсов Azure. Azure Resource Explorer предлагает представление экземпляра, а также отображает команды PowerShell для ресурсов, которые вы просматриваете.

## <a name="next-steps"></a>Дальнейшие действия

После успешного развертывания наборов виртуальной шкалы машин через Visual Studio можно дополнительно настроить проект в соответствии с требованиями к приложению. Например, настроить автомасштаб, добавив ресурс **Insights.** Можно добавить инфраструктуру в шаблон, например автономные виртуальные машины, или развернуть приложения с помощью специального расширения скрипта. Шаблоны хорошего примера можно найти в репозитории [шаблонов Azure quickstart.](https://github.com/Azure/azure-quickstart-templates) Найдите параметр `vmss`.
