---
title: Расширьте лаборатории Azure DevTest, используя функции Azure (ru) Документы Майкрософт
description: Узнайте, как расширить лаборатории Azure DevTest с помощью функций Azure.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014364"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Использование Функций Azure для расширения возможностей DevTest Labs
Функции Azure можно использовать для поддержки дополнительных сценариев, помимо тех, которые уже поддерживаются DevTest Labs. Функции Azure можно использовать для расширения встроенной функциональности службы для удовлетворения ваших бизнес-потребностей. В следующем списке приведены некоторые из возможных сценариев. В этой статье показано, как реализовать один из этих примеров сценариев.

- Предоставьте резюме виртуальных машин (VM) на высшем уровне в лаборатории
- [Настройка лаборатории для использования шлюза удаленных рабочих столов](configure-lab-remote-desktop-gateway.md)
- Отчетность о соответствии требованиям на странице внутренней поддержки
- Разрешить пользователям выполнять операции, требующие увеличения разрешений в подписке
- [Запуск рабочих процессов на основе событий DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Обзор
[Azure Functions](../azure-functions/functions-overview.md) — это бессерверная вычислительная платформа в Azure. Использование функций Azure в решении с DevTest Labs позволяет нам дополнять существующие функции собственным кодом. Для получения дополнительной информации о функциях Azure смотрите [документацию Azure Functions](../azure-functions/functions-overview.md). Чтобы проиллюстрировать, как функции Azure могут помочь выполнить ваши требования или завершить сценарии в DevTest Labs, в этой статье приводится пример предоставления резюме на высшем уровне в области VMs в лаборатории следующим образом:

**Пример требования/сценарий:** Пользователи могут видеть сведения обо всех ввозах в лаборатории, включая операционную систему, владельца и любые прикладные артефакты.  Кроме того, если артефакт **Прикладных обновлений Windows** не был недавно применен, есть простой способ применить его.

Для завершения сценария вы будете использовать две функции, описанные на следующей диаграмме:  

![Общий поток](./media/extend-devtest-labs-azure-functions/flow.png)

Исходный код для этих выборочных функций находится в [репозитории DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (доступны как реализация, так и реализация PowerShell).

- **UpdateInternalSupportPage**: Эта функция запрашивает DevTest Labs и обновляет внутреннюю страницу поддержки непосредственно с подробной информацией о виртуальных машинах.
- **ApplyWindowsUpdateArtifact:** Для VM в лаборатории эта функция применяет артефакт **обновления Windows.**

## <a name="how-it-works"></a>Принцип работы
Когда пользователи выбирают страницу **внутренней поддержки** в DevTest Labs, у них есть предварительно заполненная страница с информацией о VMs, владельцах лабораторий и контактах поддержки.  

При выборе кнопки **Select здесь для обновления** страница вызывает первую функцию Azure: **UpdateInternalSupportPage.** Функция запрашивает DevTest Labs для получения информации, а затем переписывает страницу **внутренней поддержки** с новой информацией.

Там в дополнительные действия, которые могут быть приняты, для любых vMs, на которых артефакты обновления Windows не были применены в последнее время, будет кнопка для применения обновлений окон для VM. При выборе кнопки**обновления Windows** для VM страница вызывает вторую функцию Azure: **ApplyWindowsUpdateArtifact.** Эта функция проверяет, работает ли виртуальная машина, и если да, то непосредственно применяет артефакт [обновления Windows.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)

## <a name="step-by-step-walkthrough"></a>Пошаговое руководство
В этом разделе содержатся пошаговые инструкции по настройке ресурсов Azure, необходимых для обновления страницы **внутренней поддержки.** Это пошаговое поство является одним из примеров расширения DevTest Labs. Этот шаблон можно использовать для других сценариев.

### <a name="step-1-create-a-service-principal"></a>Шаг 1: Создание основного сервиса 
Первый шаг заключается в том, чтобы получить услугу основной с разрешением на подписку, которая содержит лабораторию. Директор службы должен использовать проверку подлинности на основе паролей. Это можно сделать с помощью [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)или [портала Azure.](../active-directory/develop/howto-create-service-principal-portal.md) Если у вас уже есть директор службы для использования, вы можете пропустить этот шаг.

Запишите **идентификатор приложения,** **ключ**и **идентификатор клиента** для основного обслуживания. Они понадобятся вам позже в этом пошаговом пошаговом пошаговом пошаге. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Шаг 2: Скачать образец и открыть в Visual Studio 2019
Загрузите копию [образца функций C'Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) локально (либо путем клонирования репозитория, либо загрузки репозитория [отсюда).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Откройте выборочное решение с Visual Studio 2019.  
1. Установите рабочую нагрузку **разработки Azure** для Visual Studio, если она еще не установлена. Он может быть установлен с помощью **инструментов** -> **Получить инструменты и особенности** пункт меню).

    ![Рабочая нагрузка "Разработка для Azure".](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Создайте решение. Выберите элемент меню **«Сборка»,** а затем **постройте** элемент меню решения.

### <a name="step-3-deploy-the-sample-to-azure"></a>Шаг 3: Развертывание образца в Azure
В Visual Studio, в окне **Solution Explorer** нажмите правой кнопкой мыши на проект **AzureFunctions,** а затем выберите **Publish.** Следуйте за мастером, чтобы завершить публикацию в новом или существующем приложении Azure Function App. Подробную информацию о разработке и развертывании функций Azure с помощью Visual Studio можно узнать с [помощью Visual Studio.](../azure-functions/functions-develop-vs.md)

![Диалоговое окно "Опубликовать"](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Шаг 4: Настройки приложений
После публикации функций необходимо получить URL-адреса для этих функций с портала Azure. 

1. Перейдите на [портал Azure](https://portal.azure.com). 
1. Найдите функциональное приложение.
1. На странице **Функциональные приложения** выберите функцию. 
1. Выберите **URL функции Get,** как показано на следующем изображении. 

    ![Функции функций Azure URL-адресов](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Скопируйте и сохраните URL-адрес. Повторите эти действия для другой функции Azure. 

Вам также потребуется дополнительная информация о главном обслуживании, например идентификатор приложения, ключ и идентификатор клиента.


### <a name="step-5--update-application-settings"></a>Шаг 5: Обновление настроек приложений
В Visual Studio после публикации функции Azure выберите **настройки службы приложений Edit Azure** под **действием «Действия».** Обновление следующих настроек приложения (удаленный):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (по умолчанию до 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Параметры приложения](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Шаг 6: Проверьте функцию Azure
Последним шагом в этом пошаговом шаге является тестирование функции Azure.  

1. Перейдите к функции **UpdateInternalSupportPage** в приложении функции, созданной на шаг 3. 
1. Выберите **тест** на правой стороне страницы. 
1. Введите свойства маршрута (LABNAME, RESOURCEGROUPNAME, и SUBSCRIPTIONID).
1. Выберите **выполнить** функцию.  

    Эта функция обновит внутреннюю страницу поддержки указанной лаборатории. Она также включает в себя кнопку для пользователей, чтобы напрямую позвонить функции в следующий раз

    ![Функция тестирования](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Дальнейшие действия
Функции Azure могут помочь расширить функциональность DevTest Labs за пределы уже встроенной и помочь клиентам удовлетворить свои уникальные требования к своим командам. Эта модель может быть расширена & расширена еще больше, чтобы охватить еще больше.  Чтобы узнать больше о DevTest Labs, смотрите следующие статьи: 

- [DevTest Labs Корпоративная справочная архитектура](devtest-lab-reference-architecture.md)
- [Вопросы и ответы](devtest-lab-faq.md)
- [Масштабирование Лабораторий DevTest](devtest-lab-guidance-scale.md)
- [Автоматизация лабораторий DevTest с помощью PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








