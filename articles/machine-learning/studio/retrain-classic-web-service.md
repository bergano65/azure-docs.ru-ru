---
title: Повторное обучение и развертывание классической веб-службы
titleSuffix: Azure Machine Learning Studio
description: Узнайте, как повторно обучить модель и обновить классическую веб-службу так, чтобы она использовала заново обученную модель в Студии машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: peterlu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: 4f3ca01ae44900e4d0ce22b79db44d7bfa84e56d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456560"
---
# <a name="retrain-and-deploy-a-classic-studio-web-service"></a>Повторное обучение и развертывание классической веб-службы Студии

Повторное обучение моделей машинного обучения является одним из способов обеспечения точности и основывается на самых актуальных доступных данных. С помощью этой статьи вы узнаете, как повторно обучить классическую веб-службу Студии. Инструкции по повторному обучению новой веб-службы Студии см. в [этом практическом руководстве](retrain-machine-learning-model.md).

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас уже есть повторно обученный и прогнозный эксперимент. Эти шаги описаны в статье [Переобучение модели Студии машинного обучения Azure](retrain-models-programmatically.md). Однако вместо развертывания вашей модели машинного обучения в качестве новой классической веб-службы вы развернете свой прогнозный эксперимент.
     
## <a name="add-a-new-endpoint"></a>Добавление новой конечной точки

Прогнозная веб-служба, которую вы развернули, содержит конечную точку по умолчанию, синхронизированную с исходной обученной моделью обучающего и оценивающего экспериментов. Чтобы обновить веб-службу с помощью новой обученной модели, необходимо создать конечную точку оценки.

Новую конечную точку можно добавить в веб-службу двумя способами:

* Программным образом
* С помощью портала веб-служб Azure.

> [!NOTE]
> Важно добавить конечную точку к прогнозной веб-службе, а не к обучающей. Если вы правильно установили обучающую и прогнозную веб-службы, вы увидите две отдельные веб-службы. Имя прогнозной веб-службы должно заканчиваться на [predictive exp.].
>

### <a name="programmatically-add-an-endpoint"></a>Добавление конечной точки программно

Конечные точки оценки можно добавить с помощью примера кода, приведенного в этом [репозитории GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Добавление конечной точки с помощью портала веб-служб Azure

1. В Студии машинного обучения в левой области навигации щелкните "Web Services" (Веб-службы).
1. В нижней части панели мониторинга веб-служб щелкните **Manage endpoints preview**(Управление конечными точками (предварительная версия)).
1. Щелкните **Добавить**.
1. Введите имя и описание новой конечной точки. Выберите уровень ведения журнала и укажите, включать ли образцы данных. Дополнительные сведения о ведении журнала см. в статье [Включение функции ведения журналов для веб-служб машинного обучения](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Обновление обученной модели добавленной конечной точки

### <a name="retrieve-patch-url"></a>Извлечение URL-адреса PATCH

### <a name="option-1-programmatically"></a>Вариант 1. Программным образом

Чтобы получить правильный URL-адрес PATCH программным образом, выполните перечисленные ниже действия.

1. Выполните пример кода [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
1. В выходных данных AddEndpoint найдите значение *HelpLocation* и скопируйте URL-адрес.

   ![HelpLocation в выходных данных примера addEndpoint.](./media/troubleshooting-retraining-a-model/addEndpoint-output.png)
1. Вставьте URL-адрес в браузер, чтобы перейти на страницу, содержащую ссылки на справочные статьи о веб-службе.
1. Щелкните ссылку **Обновить ресурс**, чтобы открыть страницу справки с информацией об исправлении.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>Вариант 2. Использование портала веб-служб Машинного обучения Azure

Выполните следующие действия, чтобы получить правильный URL-адрес PATCH с помощью веб-портала.

1. Войдите на портал [веб-служб машинного обучения Azure](https://services.azureml.net/).
1. Щелкните **Веб-службы** или **Classic Web Services** (Классические веб-службы) в верхней части экрана.
1. Щелкните веб-службу оценки, с которой вы работаете (если вы не изменяли имя веб-службы по умолчанию, оно будет заканчиваться на "[Scoring Exp.]").
1. Щелкните **+СОЗДАТЬ**.
1. После добавления конечной точки щелкните ее имя.
1. В поле URL-адреса **исправления** щелкните **API Help** (Справка по API), чтобы открыть страницу справки по исправлению.

> [!NOTE]
> Если вы добавите конечную точку не к прогнозной веб-службе, а к веб-службе обучения, при выборе ссылки **Обновить ресурс** появится сообщение об ошибке: "Sorry, but this feature is not supported or available in this context. Эта веб-служба не содержит обновляемых ресурсов. We apologize for the inconvenience and are working on improving this workflow" (К сожалению, эта функция не поддерживается или недоступна в данном контексте. Приносим извинения за неудобства. Мы работаем над исправлением этой ошибки).
>

Страница исправления содержит URL-адрес исправления, который нужно использовать, и пример кода, с помощью которого можно выполнить вызов.

![URL-адрес исправления.](./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Обновление конечной точки

Теперь обученную модель можно использовать для обновления созданной конечной точки оценки.

В следующем примере кода показано, как использовать параметры *BaseLocation*, *RelativeLocation*, *SasBlobToken* и URL-адрес исправления для обновления конечной точки.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

Параметры *apiKey* и *endpointUrl* для этого вызова можно получить на панели мониторинга конечной точки.

Значение параметра *Имя* в *ресурсах* должно соответствовать имени ресурса сохраненной обученной модели в прогнозном эксперименте. Чтобы получить имя ресурса:

1. Войдите на [портале Azure](https://portal.azure.com).
1. В меню слева щелкните **Машинное обучение**.
1. В поле "Имя" выберите рабочую область, а затем щелкните **Веб-службы**.
1. В поле "Имя" щелкните **Модель ценза [predictive exp.]**.
1. Щелкните новую добавленную конечную точку.
1. На панели мониторинга конечной точки щелкните **Обновить ресурс**.
1. На странице документации по API обновления ресурсов для веб-службы можно найти **имя ресурса** в разделе **обновляемых ресурсов**.

Если срок действия маркера SAS истекает до завершения обновления конечной точки, необходимо выполнить операцию GET с использованием идентификатора задания, чтобы получить новый маркер.

Если код выполнен успешно, новая конечная точка начнет использовать переобученную модель примерно через 30 секунд.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о том, как управлять веб-службами или отслеживать несколько экспериментов, см. в следующих статьях:

* [Обзор портала веб-служб](manage-new-webservice.md)
* [Управление итерациями экспериментов](manage-experiment-iterations.md)