---
title: Проверочное тестирование функций в интерактивном режиме по модели "проверка как услуга" для Azure Stack | Документация Майкрософт
description: Узнайте, как создать тесты для проверки функций в интерактивном режиме по модели "проверка как услуга" для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972071"
---
# <a name="interactive-feature-verification-testing"></a>Тестирование интерактивной проверки функций  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

На платформе тестирования для проверки функций в интерактивном режиме вы можете подать заявку на подготовку теста для вашей системы. Когда вы подаете заявку, специалисты Майкрософт с помощью платформы подготавливают тесты, этапы которых выполняются вручную и в интерактивном режиме. С помощью платформы специалисты Майкрософт могут объединить несколько отдельных автоматических тестов.

В этой статье описан простой сценарий, выполняемый вручную. Тест позволяет проверить замену диска в Azure Stack. На каждом этапе платформа собирает данные журнала диагностики. Вы можете отлаживать проблемы по мере их обнаружения. Платформа также позволяет отправлять журналы, созданные с использованием других средств или процессов, и оставлять отзывы о сценарии.

## <a name="overview-of-a-test-pass"></a>Общие сведения о прохождении теста

Тест для выявления замены диска проводится в широком ряде сценариев. В этом примере тест состоит из семи этапов:

1.  Создание рабочего процесса **прохождения теста**.
2.  Выбор **Disk Identification Test** (Тест на идентификацию диска).
3.  Запуск теста.
4.  Выбор действий в сценарии интерактивной проверки.
5.  Проверка результатов выполнения сценария.
6.  Отправка результатов теста специалистам корпорации Майкрософт.
7.  Проверка состояния на портале VaaS (проверка как услуга).

## <a name="create-a-new-test-pass"></a>Создание процесса прохождения теста

1.  Перейдите на [портал проверки Azure Stack](https://www.azurestackvalidation.com) и выполните вход.

2.  Создайте новое решение или выберите существующее.

3.  Нажмите **Start** (Запустить) на плитке **Test Pass** (Прохождение теста).

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Введите имя рабочего процесса **прохождения теста**.

5.  Укажите имя среды и общие параметры тестирования, следуя инструкциям из статьи [Распространенные параметры рабочего процесса для проверки как услуги Azure Stack](azure-stack-vaas-parameters.md).

6.  Строка подключения системы диагностики должны иметь формат, указанный в разделе [Параметры тестирования](azure-stack-vaas-parameters.md#test-parameters) статьи [Распространенные параметры рабочего процесса для проверки как услуги Azure Stack](azure-stack-vaas-parameters.md).

7.  Задайте значения для обязательных параметров теста.

8.  Выберите агент для тестового запуска в интерактивном режиме.

> [!Note]  
> Для проверки функции идентификации диска в интерактивном режиме необходимо указать имя и пароль администратора домена.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Выбор теста

1.  Выберите **Disk Identification Test\<version>** (Тест на идентификацию диска <версия>).

    > [!Note]  
    > Номер версии будет увеличиваться по мере того, как будут вноситься изменения во вспомогательные тесты. Всегда будет использоваться последняя версия, если Майкрософт не укажет иное.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Выберите **Edit** (Изменить) и укажите имя и пароль администратора домена.

3.  Выберите агент или виртуальную машину для развертывания, где будет запущен тест.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Нажмите кнопку **Submit** (Отправить) для запуска теста.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Запуск теста

Запросы для теста на идентификацию диска отображаются на компьютере, где запущен агент VaaS. Как правило, это виртуальная машина для развертывания или Jumpbox для экземпляра Azure Stack.

![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Выбор действий

1.  Перейдите по ссылкам **Documentation** (Документация) и **Validation** (Проверка), чтобы ознакомиться с инструкциями от корпорации Майкрософт по выполнению этого сценария.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Щелкните **Далее**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Следуйте инструкциям по выполнению скрипта предварительной проверки.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Когда скрипт предварительной проверки будет успешно завершен, выполните вручную сценарий (замена диска) согласно инструкциями, приведенным по ссылкам **Documentation** (Документация) и **Validation** (Проверка) на вкладке **Information** (Сведения).

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Не закрывайте диалоговое окно, когда выполняете сценарий вручную.

6.  По завершении этого сценария следуйте инструкциям по выполнению скрипта последующей проверки.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  Когда выполняемый вручную сценарий (замена диска) будет успешно завершен, нажмите кнопку **Next** (Далее).

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Если закрыть окно, тест остановится до того, как вы его завершите.

## <a name="check-the-status"></a>Проверка состояния

1.  По завершении теста вам будет предложено отправить отзыв.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Ваши вопросы помогут нам оценить результативность и степень готовности этого сценария к выпуску.

## <a name="send-the-test-result"></a>Отправка результатов теста

1.  Присоедините все файлы журналов, которые нужно отправить в корпорацию Майкрософт.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Примите условия лицензионного соглашения для отправки отзывов.

3.  Нажмите кнопку **Submit** (Отправить), чтобы отправить результаты специалистам корпорации Майкрософт.

## <a name="next-steps"></a>Дополнительная информация

- [Мониторинг теста с помощью проверки как услуги Azure Stack](azure-stack-vaas-monitor-test.md)
