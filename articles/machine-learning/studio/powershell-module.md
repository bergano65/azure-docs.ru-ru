---
title: Модули PowerShell
titleSuffix: ML Studio (classic) - Azure
description: Используйте PowerShell для создания и управления рабочими областями, экспериментами, веб-службами и т. д. Машинное обучение Azure Studio (классическая модель).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204296"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Модули PowerShell для Машинное обучение Azure Studio (классическая модель)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

С помощью модулей PowerShell можно программно управлять ресурсами и активами студии (классическими), такими как рабочие области, наборы данных и веб-службы.

С помощью трех модулей PowerShell можно взаимодействовать с ресурсами студии (классические).

* [Azure PowerShell Az](#az-rm) 2018 года выпуска содержит все функции AzureRM, а также различные имена командлетов.
* [AzureRM](#az-rm) , выпущенная в 2016, заменена PowerShell AZ
* [Классический PowerShell для Машинного обучения Azure](#classic) 2016 года выпуска.

Хотя эти модули PowerShell имеют некоторые сходства, каждый из них предназначен для определенных сценариев. В этой статье описаны различия между модулями PowerShell, а также предложена помощь в выборе подходящих вам модулей.  

Проверьте [таблицу поддержки](#support-table) указанную ниже, чтобы увидеть, какие ресурсы поддерживаются каждым из модулей. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az и AzureRM

Az теперь является предполагаемым модулем PowerShell для взаимодействия с Azure и включает все прежние функции AzureRM. AzureRM продолжит получать исправления ошибки, но не будет получать новые командлеты или функции.  AZ и AzureRM управляют решениями развернутых служб с помощью модуля **Azure Resource Manager**. К этим ресурсам относятся рабочие области Studio (классические) и Studio (классическая модель) "новые веб-службы". 

Классическую программу PowerShell можно установить вместе с az или AzureRM, чтобы охватить как "новые", так и "классические" типы ресурсов. Тем не менее не рекомендуется устанавливать Az и AzureRM одновременно. Чтобы выбрать между Az и AzureRM, Microsoft рекомендует Az для всех последующих развернутых служб.  Дополнительные сведения о AZ и AzureRM и пути перехода см. в статье [Введение в Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Чтобы начать работу с использованием команды Az, выполните действия, описанные в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Классический PowerShell

[Классический модуль PowerShell](https://aka.ms/amlps) для Studio (классическая модель) позволяет управлять ресурсами, развернутыми с помощью **классической модели развертывания**. В число этих ресурсов входят пользовательские активы (классические) Studio, "классические" веб-службы и "классические" конечные точки веб-служб.

Однако корпорация Майкрософт рекомендует использовать модель развертывания диспетчер ресурсов для всех будущих ресурсов, чтобы упростить развертывание и управление ресурсами. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте приведенным [здесь](https://github.com/hning86/azuremlps/blob/master/README.md) инструкциям по установке. В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell.

Классическую программу PowerShell можно установить вместе с az или AzureRM, чтобы охватить как "новые", так и "классические" типы ресурсов.

## <a name="powershell-support-table"></a><a name="support-table"></a> Таблица поддержки PowerShell


| | **AZ** |  **Классическая модель PowerShell** |
| --- | --- | --- |
| Создание и удаление рабочих областей | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Управление планами обязательств рабочей области | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Управление пользователями рабочей области |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Управление веб-службами | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("новые" веб-службы)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("классические" веб-службы) |
| Управление конечными точками или ключами веб-службы |  [Get-Азмлвебсервицекэй](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Управление пользовательскими наборами данных и обученными моделями| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Управление экспериментами пользователей |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Управление настраиваемыми модулями | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Дальнейшие шаги
Ознакомьтесь с полной документацией по этому модулю PowerShell:
* [Классическая модель PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
