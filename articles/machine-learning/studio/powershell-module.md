---
title: Модули PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Используйте PowerShell для создания и управления рабочими пространствами (классическими) рабочими пространствами, экспериментами, веб-сервисами и т.д.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204296"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Модули PowerShell для студии машинного обучения Azure (классический)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Используя модули PowerShell, вы можете программно управлять ресурсами и ресурсами Studio (классическими) ресурсами и ресурсами, такими как рабочие области, наборы данных и веб-службы.

Вы можете взаимодействовать с ресурсами Studio (классические) с помощью трех модулей Powershell:

* [Azure PowerShell Az](#az-rm) 2018 года выпуска содержит все функции AzureRM, а также различные имена командлетов.
* [AzureRM](#az-rm) выпущен в 2016 году, заменив PowerShell Az
* [Классический PowerShell для Машинного обучения Azure](#classic) 2016 года выпуска.

Хотя эти модули PowerShell имеют некоторое сходство, каждый из них предназначен для конкретных сценариев. В этой статье описаны различия между модулями PowerShell, а также предложена помощь в выборе подходящих вам модулей.  

Проверьте [таблицу поддержки](#support-table) указанную ниже, чтобы увидеть, какие ресурсы поддерживаются каждым из модулей. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az и AzureRM

Az теперь является предполагаемым модулем PowerShell для взаимодействия с Azure и включает все прежние функции AzureRM. AzureRM продолжит получать исправления ошибки, но не будет получать новые командлеты или функции.  AZ и AzureRM управляют решениями развернутых служб с помощью модуля **Azure Resource Manager**. Эти ресурсы включают студии (классические) рабочие пространства и Studio (классический) "Новые" веб-сервисы. 

Классика PowerShell может быть установлена наряду с Az или AzureRM, чтобы охватить как «новые», так и «классические» типы ресурсов. Тем не менее не рекомендуется устанавливать Az и AzureRM одновременно. Чтобы выбрать между Az и AzureRM, Microsoft рекомендует Az для всех последующих развернутых служб.  Узнайте больше о Az против AzureRM и пути миграции в [представлении к Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

Чтобы начать работу с использованием команды Az, выполните действия, описанные в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Классический PowerShell

Классический модуль [Studio PowerShell](https://aka.ms/amlps) позволяет управлять ресурсами, развернутыми с помощью **классической модели развертывания.** Эти ресурсы включают в себя studio (классические) пользовательские активы, "классические" веб-сервисы и "классические" конечные точки веб-сервиса.

Однако корпорация Майкрософт рекомендует использовать модель развертывания управления ресурсами для всех будущих ресурсов для упрощения развертывания и управления ресурсами. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте приведенным [здесь](https://github.com/hning86/azuremlps/blob/master/README.md) инструкциям по установке. В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell.

Классика PowerShell может быть установлена наряду с Az или AzureRM, чтобы охватить как «новые», так и «классические» типы ресурсов.

## <a name="powershell-support-table"></a><a name="support-table"></a> Таблица поддержки PowerShell


| | **Az** |  **Классика PowerShell** |
| --- | --- | --- |
| Создание/удаление рабочих областей | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Управление планами обязательств в рабочей области | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Управление пользователями рабочей области |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Управление веб-службами | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("новые" веб-сервисы)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("классические" веб-сервисы) |
| Управление конечными точками/ключами веб-службы |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Управление наборами пользовательских данных/обученными моделями| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Управление пользовательскими экспериментами |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Управление настраиваемыми модулями | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Дальнейшие действия
Проконсультируйтесь с полной документацией этих модулей PowerShell:
* [Классика PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
