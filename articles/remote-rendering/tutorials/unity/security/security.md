---
title: Защита службы "Удаленная отрисовка Azure" и хранилища моделей
description: Усиление безопасности приложения Удаленной отрисовки для защиты содержимого
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 36d8d6afde8b1178963b33b9514e53ce0ffccf6f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224464"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Руководство по Защита службы "Удаленная отрисовка Azure" и хранилища моделей

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Защита Хранилища BLOB-объектов Azure, содержащего модели Удаленной отрисовки Azure
> * Проверка подлинности в Azure AD для доступа к экземпляру Удаленной отрисовки Azure
> * Использование учетных данных Azure для проверки подлинности в службе "Удаленная отрисовка Azure"

## <a name="prerequisites"></a>Предварительные требования

* Перед изучением этого учебника ознакомьтесь с [учебником по детализации материалов, освещения и эффектов](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Зачем нужна дополнительная защита

Текущее состояние приложения и его доступ к ресурсам Azure выглядят следующим образом:

![Начальная схема защиты](./media/security-one.png)

Какая бы схема ни использовалась, "AccountID + AccountKey" или "URL-адрес + маркер SAS", имя пользователя и пароль, по сути, хранятся вместе. Например, если предоставляется "AccountID + AccountKey", злоумышленник сможет с легкостью использовать ваши ресурсы Удаленной отрисовки Azure без вашего разрешения за ваш счет.

## <a name="securing-your-content-in-azure-blob-storage"></a>Защита содержимого в Хранилище BLOB-объектов Azure

При правильной настройке доступ службы "Удаленная отрисовка Azure" к содержимому Хранилища BLOB-объектов Azure будет безопасен. Инструкции по настройке экземпляра Удаленной отрисовки Azure с учетными записями Хранилища BLOB-объектов см. в [практическом руководстве по привязке учетных записей хранения](../../../how-tos/create-an-account.md#link-storage-accounts).

При использовании связанного Хранилища BLOB-объектов для загрузки моделей применяются несколько иные методы.

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

В приведенных выше строках используется версия `FromSAS` действия params и действия session. Их необходимо преобразовать в версии, не связанные с SAS.

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Давайте изменим **RemoteRenderingCoordinator**, чтобы загрузить настраиваемую модель из связанной учетной записи Хранилища BLOB-объектов.

1. Если вы этого еще не сделали, выполните инструкции в [практическом руководстве по привязке учетных записей хранения](../../../how-tos/create-an-account.md#link-storage-accounts), чтобы предоставить экземпляру Удаленной отрисовки Azure доступ к экземпляру Хранилища BLOB-объектов.
1. Добавьте следующий измененный метод **LoadModel** в **RemoteRenderingCoordinator** непосредственно под текущим методом **LoadModel**.

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Этот код по большей части идентичен исходному методу `LoadModel`, но мы заменили версию вызовов метода с SAS на версии, не связанные с SAS.

    Кроме того, в аргументы были добавлены дополнительные входные данные `storageAccountName` и `blobContainerName`. Мы вызываем этот новый метод **LoadModel** из другого метода, аналогичного первому методу **LoadTestModel**, созданному при прохождении первого учебника.

1. Добавьте следующий метод в **RemoteRenderingCoordinator** непосредственно после **LoadTestModel**.

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Этот код добавляет три дополнительные строковые переменные в компонент **RemoteRenderingCoordinator**.
    ![Связанная модель](./media/storage-account-linked-model.png)

1. Добавьте значения в компонент **RemoteRenderingCoordinator**. После выполнения инструкций в [кратком руководстве по преобразованию модели](../../../quickstarts/convert-model.md) значения должны быть такими:

    * **Имя учетной записи хранения**. Имя учетной записи хранения — глобально уникальное имя, выбранное для вашей учетной записи хранения. В кратком руководстве использовалось имя *arrtutorialstorage*. Ваше значение будет другим.
    * **Имя контейнера BLOB-объектов**. Для контейнера Хранилища BLOB-объектов используется arroutput.
    * **Путь к модели**. Сочетание "outputFolderPath" и "outputAssetFileName", определенных в файле *arrconfig.json*. В кратком руководстве это были "outputFolderPath": "converted/robot", "outputAssetFileName": "robot.arrAsset". В результате путь к модели получился следующим: "converted/robot/robot.arrAsset". Ваше значение будет другим.

    >[!TIP]
    > Если вы [выполните скрипт **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) без аргумента "-UseContainerSas", он выведет все указанные выше значения вместо маркера SAS. ![Связанная модель](./media/converted-output.png)
1. Удалите или отключите на время игровой объект **TestModel**, чтобы освободить место для загрузки настраиваемой модели.
1. Воспроизведите сцену и подключитесь к удаленному сеансу.
1. Щелкните правой кнопкой мыши **RemoteRenderingCoordinator** и выберите **Load Linked Custom Model** (Загрузить связанную настраиваемую модель).
    ![Загрузка связанной модели](./media/load-linked-model.png)

После выполнения этих действий уровень безопасности приложения повысился за счет удаления маркера SAS из локального приложения.

Теперь текущее состояние приложения и его доступ к ресурсам Azure выглядят следующим образом:

![Улучшенная безопасность](./media/security-two.png)

Нам нужно удалить из локального приложения еще один "пароль", AccountKey. Для этого можно воспользоваться проверкой подлинности Azure Active Directory (AAD).

## <a name="azure-active-directory-azure-ad-authentication"></a>Проверка подлинности через Azure Active Directory (Azure AD)

Проверка подлинности AAD позволяет лучше контролировать, какие пользователи или группы могут использовать Удаленную отрисовку Azure. Вместо использования ключа учетной записи служба "Удаленная отрисовка Azure" может принимать [маркеры доступа](https://docs.microsoft.com/azure/active-directory/develop/access-tokens). Маркеры доступа можно рассматривать как ключ конкретного пользователя с ограниченным сроком действия, который разблокирует только определенные части конкретного ресурса, для которого он был запрошен.

У скрипта **RemoteRenderingCoordinator** имеется делегат с именем **ARRCredentialGetter**. Он содержит метод, возвращающий объект **AzureFrontendAccountInfo**, который используется для настройки удаленного управления сеансами. Этому делегату **ARRCredentialGetter** можно назначить другой метод. Это позволит использовать поток входа Azure, создающий объект **AzureFrontendAccountInfo**, который содержит маркер доступа Azure. Этот маркер доступа будет действовать только для пользователя, который выполнил вход.

1. Следуйте указаниям [практического руководства по настройке проверки подлинности, изложенной в разделе "Проверка подлинности для развернутых приложений"](../../../how-tos/authentication.md#authentication-for-deployed-applications). В частности, выполните инструкции в документации по Пространственным привязкам Azure, приведенные в разделе [Аутентификация пользователей с помощью AAD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication). Такой подход предполагает регистрацию нового приложения Azure Active Directory и настройку доступа к экземпляру Удаленной отрисовки Azure.
1. После настройки нового приложения AAD убедитесь, что он выглядит так, как на следующих изображениях.

    **AAD Application (Приложение AAD) -> Authentication (Аутентификация)** ![Аутентификация приложения](./media/app-authentication-public.png)

    **AAD Application (Приложение AAD) -> API Permissions (Разрешения API)** ![API приложения](./media/request-api-permissions-step-five.png)

1. После настройки учетной записи Удаленной отрисовки убедитесь, что ваша конфигурацию выглядит так, как на следующем рисунке:

    **AAR (Маршрутизация запросов приложений) -> AccessControl (IAM) (Управление доступом (IAM))** ![Роль ARR](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Для управления сеансами через клиентское приложение роли *владельца* недостаточно. Каждому пользователю, которому необходимо предоставить возможность управления сеансами, следует назначить роль **клиента Удаленной отрисовки**. Каждому пользователю, которому необходимо управлять сеансами и преобразовывать модели, следует назначить роль **администратора Удаленной отрисовки**.

Теперь, когда вы настроили все необходимое в Azure, нужно изменить способ подключения кода к службе AAD. Для этого необходимо реализовать экземпляр **BaseARRAuthentication**, который вернет новый объект **AzureFrontendAccountInfo**. В этом случае сведения об учетной записи будут настроены с помощью маркера доступа Azure.

1. Создайте скрипт с именем **AADAuthentication** и замените его код следующим:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts.mixedreality.azure.com/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Этот код не является полным и не готов к использованию в коммерческой программе. Например, вам, вероятно, потребуется добавить как минимум возможность выхода из нее. Это можно сделать с помощью метода `Task RemoveAsync(IAccount account)`, предоставляемого клиентским приложением. Этот код предназначен только для учебных целей. У вас будет своя реализация, характерная для вашего приложения.

Код сначала пытается получить маркер автоматически с помощью **AquireTokenSilent**. Попытка будет успешной, если пользователь ранее проходил проверку подлинности для этого приложения. Если это не так, перейдем к более сложной стратегии, требующей участия пользователя.

В этом коде для получения маркера доступа мы используем [поток кода устройства](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code). Этот поток позволяет пользователю войти в свою учетную запись Azure на компьютере или мобильном устройстве, после чего приложению HoloLens будет возвращен полученный в итоге маркер.

Наиболее важной частью этого класса с точки зрения Удаленной отрисовки Azure является эта строка:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Здесь мы создаем объект **AzureFrontendAccountInfo**, используя домен и идентификатор учетной записи, а также маркер доступа. Этот маркер затем используется службой "Удаленная отрисовка Azure" для запроса, создания и объединения сеансов удаленной отрисовки при условии, что пользователь авторизован в соответствии с разрешениями на основе ролей, настроенными ранее.

После этих изменений текущее состояние приложения и его доступ к ресурсам Azure выглядят следующим образом:

![Еще более надежная защита](./media/security-three.png)

Поскольку учетные данные пользователя не хранятся на устройстве (или даже вводятся на устройстве как в данном случае), риск их раскрытия очень низкий. Теперь устройство использует маркер доступа конкретного пользователя с ограниченным сроком действия для доступа к службе "Удаленная отрисовка Azure", которая, в свою очередь, использует управление доступом (IAM) для доступа к Хранилищу BLOB-объектов. Эти два действия позволили полностью удалить "пароли" из исходного кода и существенно повысить уровень безопасности. Тем не менее это не самый безопасный способ защиты. Перемещение модели и управления сеансами в веб-службу позволит обеспечить еще более надежную защиту. Дополнительные вопросы безопасности обсуждаются в главе, посвященной [готовности к коммерческому использованию](../commercial-ready/commercial-ready.md).

### <a name="testing-aad-auth"></a>Тестирование проверки подлинности AAD

Если в редакторе Unity активна проверка подлинности AAD, вам потребуется проходить аутентификацию при каждом запуске приложения. На устройстве этап проверки подлинности выполняется в первый раз, а затем требуется только после истечения срока действия маркера или в случае его недействительности.

1. Добавьте компонент **AADAuthentication** в игровой объект **RemoteRenderingCoordinator**.

    ![Компонент проверки подлинности AAD](./media/azure-active-directory-auth-component.png)

1. Введите значения в поля идентификатора клиента и идентификатора арендатора. Эти значения можно найти на странице обзора регистрации приложения.

    * **Account Domain** (Домен учетной записи) — это тот же домен, который вы использовали в качестве домена учетной записи **RemoteRenderingCoordinator**.
    * **Active Directory Application Client ID** (Идентификатор клиента приложения Active Directory) — это *идентификатор приложения (клиента)* , указанный при регистрации приложения AAD (см. изображение ниже).
    * **Azure Tenant ID** (Идентификатор арендатора Azure) — это *идентификатор каталога (арендатора)* , указанный при регистрации приложения AAD (см. изображение ниже).
    * **Azure Remote Rendering Account ID** (Идентификатор учетной записи Удаленной отрисовки Azure) — это тот же **идентификатор учетной записи**, который вы использовали для **RemoteRenderingCoordinator**.

    ![Компонент проверки подлинности AAD](./media/app-overview-data.png)

1. Нажмите кнопку воспроизведения в редакторе Unity и дайте согласие на выполнение сеанса.
    Так как компонент **AADAuthentication** имеет контроллер представления, он автоматически привязывается для отображения запроса после модальной панели авторизации сеанса.
1. Следуйте инструкциям, приведенным на панели справа от **AppMenu**.
    Должно отображаться примерно следующее: ![Компонент аутентификации AAD](./media/device-flow-instructions.png) После ввода указанного кода на дополнительном устройстве (или в браузере на том же устройстве) и входа с использованием ваших учетных данных запрашивающему приложению (в данном случае редактору Unity) будет возвращен маркер доступа.
1. После этого все функции приложения должны работать надлежащим образом. Проверьте наличие ошибок в консоли Unity, если вам не удалось выполнить эти этапы так, как ожидалось.

## <a name="build-to-device"></a>Сборка на устройстве

Если вы выполняете сборку приложения на устройстве с помощью MSAL, необходимо включить файл в папку **Assets** проекта. Это поможет компилятору правильно собрать приложение с помощью библиотеки *Microsoft.Identity.Client.dll*, входящей в состав **учебных ресурсов**.

1. Добавьте в папку **Assets** новый файл с именем **link.xml**.
1. Добавьте в файл следующий код:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Сохранение изменений

Выполните действия, описанные в [кратком руководстве по развертыванию примера Unity в HoloLens в разделе "Сборка примера проекта"](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project), чтобы выполнить сборку в HoloLens.

## <a name="next-steps"></a>Дальнейшие действия

Остальные учебники этого цикла посвящены созданию готового к использованию приложения, использующего Удаленную отрисовку Azure.

> [!div class="nextstepaction"]
> [Дальнейшие действия. Готовность к коммерческому использованию](../commercial-ready/commercial-ready.md)
