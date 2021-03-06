---
title: Сохраните и распространение образов в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как создать фабрику пользовательского образа в Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622683"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Сохранение пользовательских образов и их распространение в несколько лабораторий
В этой статье рассматриваются предоставляет действия для сохранения пользовательских образов из уже созданных виртуальных машин (ВМ). Кроме того, здесь рассматривается, как для распространения этих пользовательских образов для других DevTest Labs, в организации.

## <a name="prerequisites"></a>Технические условия
Следующие элементы уже должны быть выполнены:

- Лаборатории для фабрики образа в Azure DevTest Labs.
- Проект DevOps Azure, используемый для автоматизации фабрики образов.
- Расположение исходного кода, содержащий сценарии и конфигурации (в нашем примере, в том же проекте DevOps, упомянутые в предыдущем шаге).
- Определение для организации задач Azure Powershell сборки.

При необходимости выполните шаги [запустите фабрику образов из Azure DevOps](image-factory-set-up-devops-lab.md) для создания и настройки этих элементов. 

## <a name="save-vms-as-generalized-vhds"></a>Сохраните виртуальные машины как обобщенной виртуальных жестких дисков
Сохраните существующие виртуальные машины как обобщенной виртуальных жестких дисков.  Есть пример сценария PowerShell, чтобы сохранить существующие виртуальные машины как обобщенной виртуальных жестких дисков. Чтобы использовать его, во-первых, добавьте еще один **Azure Powershell** задач в определение сборки, как показано на следующем рисунке:

![Добавить шаг Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Получив новую задачу в списке, выберите элемент, чтобы мы смогли заполнить в деталях, как показано на следующем рисунке: 

![Настройка PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Универсальная и специализированных пользовательских образов
В [портала Azure](https://portal.azure.com), при создании пользовательского образа из виртуальной машины, вы можете иметь универсальный или специализированный пользовательский образ.

- **Специализированный образ:** Sysprep или отзыва не была запущена на компьютере. Это означает, что образ является точной копией диска операционной системы на существующей виртуальной машины (моментальный снимок).  Же файлов, приложений, учетные записи пользователей, имя компьютера и т. д., присутствуют все при создании новой машины из этого пользовательского образа.
- **Обобщенный образ:** Sysprep или отзыва была запущена на компьютере.  При выполнении этого процесса, он удаляет учетные записи пользователей, удаляет имя компьютера, исключались кустов реестра пользователя, и т.д., с целью создание универсального образа, поэтому его можно настроить при создании другой виртуальной машины.  При подготовке к использованию виртуальной машины (путем запуска средства sysprep), процесс уничтожает текущей виртуальной машины — он больше не будет работать.

Скрипт для привязки пользовательских образов в фабрике изображение будет сохранить виртуальные жесткие диски для виртуальные машины, созданные на предыдущем шаге (идентифицировать на основе тега, на ресурс в Azure).

## <a name="update-configuration-for-distributing-images"></a>Обновить конфигурацию для распространения изображений
Следующий шаг в процессе является для передачи пользовательских образов из лаборатории фабрики образа out лабораторные занятия, где они необходимы. Основой этого процесса является **labs.json** файла конфигурации. Можно найти этот файл в **конфигурации** папки, содержащейся в фабрике образов.

Существует два основных вариантов, перечисленных в файле конфигурации labs.json:

- Уникальная идентификация определенное место назначения лаборатории, используя идентификатор подписки и имя лаборатории.
- Конкретный набор образов, которые должны быть переданы в лабораторию как относительные пути к корню конфигурации. Можно указать всю папку (для получения всех образов в этой папке) слишком.

Ниже приведен пример файла labs.json с двумя labs в списке. В этом случае планируется распространять образы в двух разных labs.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Создание задачи сборки
Используя те же шаги, вы видели ранее в этой статье, добавить дополнительный **Azure Powershell** задаче сборки вы определение сборки. Введите необходимые сведения, как показано на следующем рисунке: 

![Создавать задачи для распространения образов](./media/save-distribute-custom-images/second-build-task-powershell.png)

Используются следующие параметры. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Эта задача принимает все пользовательские образы, в фабрике образов и отправляет их в любых лабораторий, определенные в файле Labs.json.

## <a name="queue-the-build"></a>Поместите сборку в очередь
После завершения задачи построения распространения поставить новую сборку, чтобы убедиться в том, что все работает. После успешного завершения сборки, новые пользовательские образы будут отображаться в целевой лабораторной среде, указанному в файле конфигурации Labs.json.

## <a name="next-steps"></a>Дальнейшие действия
В следующей статье из серии необходимо заменить изображение фабрики хранения политики и очистки действия: [Задать политику хранения и выполнение сценариев очистки](image-factory-set-retention-policy-cleanup.md).
