---
title: Руководство. Распознавание эмоций лица на изображении — API распознавания эмоций, C#
titlesuffix: Azure Cognitive Services
description: Изучите простое приложение Windows для распознавания эмоции на лицах на изображении.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: da605ec4013fb11606f99f3d9a2dcfcfcab00d3b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163333"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Руководство. Распознавание эмоций лица на изображении

> [!IMPORTANT]
> 15 февраля 2019 г. API распознавания эмоций будет отмечен как нерекомендуемый. Сейчас функция распознавания эмоций является общедоступной в составе [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/). 

Изучите простое приложение Windows, использующее API распознавания эмоций, чтобы распознать эмоции на лицах на изображении. Приведенный ниже пример позволяет отправить URL-адрес изображения или локально хранящихся файлов. Вы можете использовать этот пример с открытым кодом в качестве шаблона для создания собственного приложения для Windows с использованием API распознавания эмоций и WPF (Windows Presentation Foundation), входящего в состав .NET Framework.

## <a name="Prerequisites">Предварительные требования</a>
#### <a name="platform-requirements"></a>Требования платформы  
Пример был разработан для платформы .NET Framework с помощью [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Подписка на службу API распознавания эмоций и получение ключа подписки  
Перед созданием примера необходимо подписаться на API распознавания эмоций, который является частью службы Microsoft Cognitive Services. Ознакомьтесь со страницей [подписок](https://azure.microsoft.com/try/cognitive-services/). В этом руководстве можно использовать как первичный, так и вторичный ключ. Обязательно следуйте рекомендациям по обеспечению секретности и безопасности вашего ключа API.  

#### <a name="get-the-client-library-and-example"></a>Получение клиентской библиотеки и примера  
Вы можете загрузить клиентскую библиотеку API распознавания эмоций через [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Содержимое скачанного ZIP-файла нужно извлечь в папку по своему усмотрению. Многие пользователи выбирают папку Visual Studio 2015.
## <a name="Step1">Шаг 1. Открытие примера</a>
1.  Запустите Microsoft Visual Studio 2015 и выберите **Файл**, щелкните **Открыть**, а затем **Проект или решение**.
2.  Перейдите к папке, где были сохранены загруженные файлы API распознавания эмоций. Щелкните **API распознавания эмоций**, затем **Windows**, а затем папку **Sample-WPF**.
3.  Чтобы открыть, дважды щелкните файл Visual Studio 2015 Solution (SLN), с именем **EmotionAPI-WPF-Samples.sln**. При этом откроется решение в Visual Studio.

## <a name="Step2">Шаг 2. Компиляция примера</a>
1. В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet**.

  ![Открытый диспетчер пакетов NuGet](../Images/EmotionNuget.png)

2.  Откроется окно **Диспетчер пакетов NuGet**. Сначала выберите **Просмотреть** в левом верхнем углу, а затем в поле поиска введите Newtonsoft.Json, выберите пакет **Newtonsoft.Json** и нажмите кнопку **Установить**.  

  ![Перейдите к пакету NuGet](../Images/EmotionNugetBrowse.png)  

3.  Нажмите клавиши CTRL+SHIFT+B или выберите действие **Build** (Сборка) в меню ленты, а затем выберите **Build Solution** (Собрать решение).

## <a name="Step3">Шаг 3. Выполнение примера</a>
1.  После завершения сборки нажмите клавишу **F5** или щелкните **Start** (Запустить) в меню ленты, чтобы выполнить созданный пример.
2.  Найдите окно API распознавания эмоций с **текстовым полем**, в котором написано **Вставьте ваш ключ подписки здесь для запуска**. Вставьте ключ подписки в текстовое поле, как показано на снимке экрана ниже. Вы можете сохранить ключ подписки на своем компьютере или ноутбуке, нажав кнопку "Сохранить ключ". Если вы хотите удалить ключ подписки из системы, нажмите кнопку "Удалить ключ". Таким образом вы удаляете его с ноутбука или компьютера.

  ![Функциональные возможности API распознавания эмоций](../Images/EmotionKey.png)

3.  Щелкните в разделе **Выбор сценария**, чтобы использовать один из двух сценариев **Detect emotion using a stream** (Обнаружить эмоции с помощью потока) или **Detect emotion using a URL** (Обнаружить эмоции с помощью URL-адреса), затем следуйте инструкциям на экране. Корпорация Майкрософт получает загружаемые изображения и может использовать их для улучшения API распознавания эмоций и связанных с ним служб. Отправляя изображение, вы подтверждаете, что выполнили наши [правила поведения разработчиков](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Доступны примеры изображений, которые можно использовать в этом примере приложения. Эти изображения можно найти в репозитории [API распознавания лиц на GitHub](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) в папке **Data**. Обратите внимание, что использование этих изображений лицензируется по соглашению о добросовестном использовании, это означает, что они подходят для тестирования этого примера, но не для повторной публикации.

## <a name="Review">Просмотр и изучение</a>
Теперь у вас есть работающее приложение, и на его примере мы рассмотрим интеграцию приложений с Microsoft Cognitive Services. Благодаря этому вам будет проще продолжать развитие этого приложения или разработать собственное приложение, которое использует службу API распознавания эмоций от корпорации Майкрософт.

В этом примере приложение использует клиентскую библиотеку API распознавания эмоций и тонкую клиентскую программу-оболочку C# для API распознавания эмоций от корпорации Майкрософт. При построении примера приложения, как описано выше, вы получаете клиентскую библиотеку из пакета NuGet. Вы можете просмотреть исходный код клиентской библиотеки в папке [Клиентская библиотека](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary) в разделе **Распознавание эмоций**, **Windows**, **Клиентская библиотека**, который является частью загруженного репозитория файлов, упомянутого выше в [предварительных требованиях](#Prerequisites).

Вы также можете узнать, как использовать код клиентской библиотеки, в **обозревателе решений**. В разделе **EmotionAPI-WPF_Samples** разверните **DetectEmotionUsingStreamPage.xaml**, чтобы найти файл **DetectEmotionUsingStreamPage.xaml.cs**, который используется для просмотра локально хранящегося файла, или разверните **DetectEmotionUsingURLPage.xaml**, чтобы найти файл **DetectEmotionUsingURLPage.xaml.cs**, который используется при отправке URL-адреса изображения. Дважды щелкните XAML.CS-файлы, чтобы они отобразились в новых окнах Visual Studio.

Чтобы проверить, как клиентская библиотека API распознавания эмоций используется в нашем примере, рассмотрим два фрагмента кода из **DetectEmotionUsingStreamPage.xaml.cs** и **DetectEmotionUsingURLPage.xaml.cs**. Каждый файл содержит комментарии к коду, в которых есть фразы "KEY SAMPLE CODE STARTS HERE" (Здесь начинается основной пример кода) и "KEY SAMPLE CODE ENDS HERE" (Здесь завершается основной пример кода), которые помогут найти приведенные ниже фрагменты кода.

API распознавания эмоций — это возможность работать с URL-адресом изображения или двоичными данными изображения (в виде потока октетов) в качестве входных данных. Ниже рассмотрены два примера. В обоих случаях вы сначала найдете директиву использования, позволяющую использовать клиентскую библиотеку API распознавания эмоций.
```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Use the following namespace for EmotionServiceClient
            // -----------------------------------------------------------------------
            using Microsoft.ProjectOxford.Emotion;
            using Microsoft.ProjectOxford.Emotion.Contract;
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs

В этом фрагменте кода показано, как использовать клиентскую библиотеку для отправки ключа подписки и URL-адреса фотографии в службу API распознавания эмоций.

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs

Ниже показано, как отправить ключ подписки и локально хранящееся изображение в службу API распознавания эмоций.


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
