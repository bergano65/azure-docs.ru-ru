---
заголовок: Модули PowerShell для Студии машинного обучения titleSuffix: Студия машинного обучения Azure description: Использование PowerShell для создания рабочих областей, экспериментов, веб-служб и другого, а также управления этими компонентами в Студии машинного обучения Microsoft Azure. services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=haining, previous-author=hning86 ms.date: 25.01.2019
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Модули PowerShell для Студии машинного обучения Microsoft Azure

С помощью модулей PowerShell вы можете программным способом управлять ресурсами Студии, например, рабочими областями, наборами данных и веб-службами.

Вы можете взаимодействовать с ресурсами Студии с помощью трех модулей Powershell:

* [Azure PowerShell Az](#az-rm) 2018 года выпуска содержит все функции AzureRM, а также различные имена командлетов.
* [AzureRM](#az-rm) 2016 года выпуска.
* [Классический PowerShell для Машинного обучения Azure](#classic) 2016 года выпуска.

Несмотря на то что эти модули имеют некоторые сходства, каждый предназначен для определенного сценария. В этой статье описаны различия между модулями PowerShell, а также предложена помощь в выборе подходящих вам модулей.

## <a name="choosing-modules"></a> Выбор модулей

Выбор между доступными модулями PowerShell зависит от типа управляемых ресурсов.

Проверьте [таблицу поддержки](#support-table) указанную ниже, чтобы увидеть, какие ресурсы поддерживаются каждым из модулей. Так как классический PowerShell можно устанавливать параллельно с Az или AzureRM, вы можете устанавливать два модуля и охватывать все типы ресурсов (классический с использованием Az или с AzureRM)

Тем не менее не рекомендуется устанавливать Az и AzureRM одновременно. Чтобы выбрать между Az и AzureRM, Microsoft рекомендует Az для всех последующих развернутых служб. Используйте AzureRm, только если особые обстоятельства вашей среды требуют этого.

Чтобы узнать больше о различиях между Az и AzureRM, а также о предоставленном нами пути миграции, см. статью [Знакомство с новым модулем Az для Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Azure PowerShell Az и AzureRM

AZ и AzureRM управляют решениями развернутых служб с помощью модуля **Azure Resource Manager**. Эти ресурсы включают рабочие области и новые веб-службы Студии. Чтобы управлять ресурсами, развернутыми с помощью классической модели развертывания, следует использовать классический модуль PowerShell. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Az теперь является предполагаемым модулем PowerShell для взаимодействия с Azure и включает все прежние функции AzureRM. AzureRM продолжит получать исправления ошибки, но не будет получать новые командлеты или функции. Несмотря на наличие пути обновления от AzureRM, если возникли проблемы с Az при работе со Студией, сообщите о проблеме и вернитесь к использованию AzureRM.

Чтобы начать работу с использованием команды Az, выполните действия, описанные в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> Классический PowerShell

[Классическая модель PowerShell](https://aka.ms/amlps) Студии позволяет управлять развернутыми ресурсами, используя **классическую модель развертывания**. Эти ресурсы состоят из ресурсов пользователей Студии, классических веб-служб и классических конечных точек службы.

Тем не менее Майкрософт рекомендует использовать модель развертывания Resource Manager для всех новых ресурсов, чтобы упростить развертывание и управление ресурсами. Дополнительные сведения о моделях развертывания см. в статье [Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте приведенным [здесь](https://github.com/hning86/azuremlps/blob/master/README.md) инструкциям по установке. В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell.

## <a name="support-table"></a> Таблица поддержки PowerShell

 **Рабочие области Студии** | **Az** |  **AzureRM** | **PowerShell — классическая модель** |
| --- | --- | --- | --- | --- |
| Создание и удаление рабочих областей | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Шаблоны Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Управление пользователями рабочей области |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Управление тарифными планами с обязательствами | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Веб-службы** | **Az** | **AzureRM** | **PowerShell — классическая модель** |
| Управление веб-службами | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Новые веб-службы) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Новые веб-службы) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (классические веб-службы) |
| Управление конечными точками и ключами |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Новые веб-службы) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Новые веб-службы) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (классические веб-службы) |
|||
| **Ресурсы пользователя** | **Az** | **AzureRM** | **PowerShell — классическая модель** |
| Управление наборами данных и обученными моделями |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Управление экспериментами |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Управление настраиваемыми модулями |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Дополнительная информация
Используйте следующие ссылки для полной документации модулей PowerShell:
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell — классическая модель](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
