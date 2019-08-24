---
title: Диагностика сбоев артефактов на виртуальной машине Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как устранять неполадки при сбоях артефактов в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991368"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Диагностика сбоев артефактов в лаборатории 
После создания артефакта можно проверить, работает ли он. Журналы артефактов в Azure DevTest Labs предоставляют сведения, которые можно использовать для диагностики сбоя артефакта. Есть два способа просмотреть данные журнала артефактов для виртуальной машины Windows:

* На портале Azure
* в виртуальной машине.

> [!NOTE]
> Чтобы можно было правильно идентифицировать и объяснить сбой, важно правильно структурировать артефакт. Сведения о том, как правильно создать артефакт, см. в статье [Создание пользовательских артефактов для виртуальной машины DevTest Labs](devtest-lab-artifact-author.md). Правильно структурированный артефакт можно изучить на примере этого артефакта [тестовых типов параметров](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Устранение неполадок при сбоях артефактов с помощью портала Azure

1. На портале Azure в списке ресурсов выберите свою лабораторию.
2. Выберите виртуальную машину Windows, содержащую анализируемый артефакт.
3. На левой панели в разделе **ОБЩИЕ** выберите **Артефакты**. Появится список артефактов, связанных с этой виртуальной машиной. В нем указаны имя и состояние артефакта.

   ![Состояние артефакта](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Выберите артефакт, отображаемый состояние **Сбой**. Артефакт откроется и отобразится сообщение расширения, содержащее сведения о сбое артефакта.

   ![Сообщение об ошибке артефакта](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Устранение неполадок при сбоях артефактов из виртуальной машины

1. Войдите в виртуальную машину, содержащую артефакт, для которого необходимо выполнить диагностику.
2. Перейдите к расположению C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, где *1.9* — это номер версии расширения пользовательских сценариев Azure.

   ![Файл состояния](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Откройте файл **состояния**.

Инструкции по поиску файлов журналов на виртуальной машине **Linux** см. в следующей статье: [Использование расширения пользовательских сценариев Azure версии 2 с виртуальными машинами Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Связанные записи в блогах
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Присоединение виртуальной машины к имеющемуся домену AD с помощью шаблона ARM в Azure DevTest Labs)

## <a name="next-steps"></a>Следующие шаги
* Узнайте, как [добавить в лабораторию репозиторий Git](devtest-lab-add-artifact-repo.md).

