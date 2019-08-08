---
title: Начало работы с Android и платформой удостоверений Майкрософт | Azure
description: Получение маркера доступа для приложения Android и вызов API Microsoft Graph или API, которые требуют маркеры доступа от платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76547fd708f880bdf5167d71db121e69fc5b1d30
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823801"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Вход пользователей и вызов Microsoft Graph из приложения Android

Из этого руководства вы узнаете, как интегрировать приложение Android с платформой удостоверений Майкрософт. Ваше приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph.  

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Как работает это руководство

![Схема работы примера приложения, создаваемого в этом кратком руководстве](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

В этом руководстве приложение авторизует пользователя и получает данные от его имени.  Доступ к этим данным будет осуществляться через защищенный API (в данном случае API Microsoft Graph), который требует авторизации и защищен платформой удостоверений Майкрософт.

В частности:

* Приложение авторизует пользователя через браузер или Microsoft Authenticator и Корпоративный портал Intune.
* Пользователь принимает разрешения, которые запрашивает приложение.
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph.

В этом примере используется библиотека проверки подлинности Майкрософт для Android (MSAL), [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal), которая помогает реализовать проверку подлинности.

 MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, а также управляет учетными записями.

## <a name="prerequisites"></a>Предварительные требования

* Для работы с этим руководством требуется Android Studio 16 или последующей версии (рекомендуется версия 19 или последующая).

## <a name="create-a-project"></a>Создание проекта

В рамках этого руководства будет создан проект. Если вместо этого вы хотите скачать готовое руководство, [скачайте код](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Запустите Android Studio и выберите **Start a new Android Studio project** (Создать проект Android Studio).
2. Выберите **Basic Activity** (Базовое действие) и щелкните **Next** (Далее).
3. Присвойте имя приложению.
4. Сохраните имя пакета. Позже вы укажете его на портале Azure.
5. Присвойте параметру **Minimum API level** (Минимальный уровень API) задайте **API 19** или выше и щелкните **Finish** (Готово).
6. В представлении проекта в раскрывающемся списке выберите **Project** (Проект), чтобы отобразить исходные и другие файлы проекта, откройте **app/build.gradle** и задайте параметру `targetSdkVersion` значение `27`.

## <a name="register-your-application"></a>Регистрация приложения

1. Перейдите на [портал Azure](https://aka.ms/MobileAppReg).
2. На панели [Регистрация приложений](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) щелкните **+Новая регистрация**.
3. Введите **имя** приложения, а затем, не указывая URI перенаправления, щелкните **Зарегистрировать**.
4. На панели в открывшемся разделе **Управление** выберите **Аутентификация** >  **+Добавить платформу** > **Android**.
5. Введите имя пакета проекта. Если вы скачали код, это будет значение `com.azuresamples.msalandroidapp`.
6. В разделе **Хэш подписи** на странице **Настройка приложения Android** щелкните **Generating a development Signature Hash** (Создания хэша подписи для разработки) и скопируйте команду KeyTool для использования с вашей платформой.

   > [!Note]
   > KeyTool. exe устанавливается как часть пакета средств разработки Java (JDK). Необходимо также установить средство OpenSSL для выполнения команды KeyTool.

7. Введите **хэш подписи**, созданный с помощью KeyTool.
8. Щелкните `Configure` и сохраните **конфигурацию MSAL**, отображаемую на странице **Настройка Android**, чтобы указать ее при последующей настройке приложения.  Нажмите кнопку **Done**(Готово).

## <a name="build-your-app"></a>Создание приложения

### <a name="add-your-app-registration"></a>Добавление регистрации приложения

1. На панели проекта Android Studio перейдите к **app\src\main\res**.
2. Щелкните правой кнопкой мыши **res** и выберите **New** (Создать)  > **Directory** (Каталог). Введите `raw` в качестве имени каталога и нажмите кнопку **ОК**.
3. В расположении **app** > **src** > **res** > **raw** создайте файл JSON с именем `auth_config.json` и вставьте в него ранее сохраненную конфигурацию MSAL. Дополнительные сведения см. в статье о [настройке MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. В **app** > **src** > **main** > **AndroidManifest.xml** добавьте действие `BrowserTabActivity`, как показано ниже. Эта запись позволяет корпорации Майкрософт выполнять обратный вызов приложения после завершения проверки подлинности:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Замените имя пакета, зарегистрированного на портале Azure, значением `android:host=`.
    Замените хэш ключа, зарегистрированный на портале Azure, значением `android:path=`. Хэш подписи не должен быть указан в формате URL-адреса.

5. В файле **AndroidManifest.xml** непосредственно над тегом `<application>` добавьте следующие разрешения:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Создание пользовательского интерфейса приложения

1. В окне проекта Android Studio перейдите к папке **app** > **src** > **main** > **res** > **layout**, откройте файл **activity_main.xml** и представление **Text** (Текст).
2. Замените макет действия, например с `<androidx.coordinatorlayout.widget.CoordinatorLayout` на `<androidx.coordinatorlayout.widget.LinearLayout`.
3. Добавьте в узел `LinearLayout` свойство `android:orientation="vertical"`.
4. Вставьте следующий код в узел `LinearLayout`, заменив текущее содержимое:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Добавление MSAL в проект

1. В окне проекта Android Studio перейдите к **app** > **src** > **build.gradle**.
2. В разделе **Dependencies** (Зависимости) вставьте следующий код:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Использование MSAL

Теперь внесите изменения в `MainActivity.java`, чтобы добавить и использовать MSAL в приложении.
В окне проекта Android Studio перейдите к **app** > **src** > **main** > **java** > **com.example.msal** и откройте `MainActivity.java`.

#### <a name="required-imports"></a>Необходимые операторы import

Добавьте следующие операторы import в начало `MainActivity.java`:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Создание экземпляра MSAL

Внутри класса `MainActivity` нужно создать экземпляр MSAL, а также несколько конфигураций, касающихся того, что будет выполнять приложение, включая области действия и веб-API, к которым необходимо получить доступ.

Скопируйте следующие переменные в класс `MainActivity`:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Замените содержимое файла `onCreate()` следующим кодом, чтобы создать экземпляр MSAL:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

Приведенный выше код пытается автоматически выполнить вход пользователей при открытии приложения с помощью `getAccounts()` и, в случае успешного выполнения, `acquireTokenSilentAsync()`.  В следующих разделах мы реализуем обработчик обратных вызовов на случай, если в системе нет учетных записей, с которых выполнен вход.

#### <a name="use-msal-to-get-tokens"></a>Использование MSAL для получения маркеров

Теперь мы можем реализовать логику обработки пользовательского интерфейса приложения и интерактивно получать маркеры через MSAL.

MSAL предоставляет два основных метода получения маркеров: `acquireTokenSilentAsync()` и `acquireToken()`.  

`acquireTokenSilentAsync()` выполняет вход и получает маркеры без какого-либо взаимодействия с пользователем при условии наличия учетной записи. В случае успешного входа MSAL передаст маркеры в приложение, а случае сбоя создаст исключение `MsalUiRequiredException`.  Если это исключение создано или требуется реализовать интерактивный вход пользователей (могут потребоваться или не потребоваться учетные данные, многофакторная проверка подлинности или другие политики условного доступа), тогда можно использовать `acquireToken()`.  

Метод `acquireToken()` отображает пользовательский интерфейс при попытке входа пользователя и получения маркеров. При этом он может использовать файлы cookie сеанса в браузере или учетную запись в Microsoft Authenticator для реализации интерактивного единого входа.

Создайте следующие три метода пользовательского интерфейса внутри класса `MainActivity`:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Добавьте следующие методы для получения текущего действия и обработки автоматических и интерактивных обратных вызовов:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Использование MSAL для выхода

Теперь добавим поддержку функции выхода.

> [!Important]
> Если функция выхода реализована с помощью MSAL, из приложения удаляется вся известная информация о пользователе, но сеанс на устройстве пользователя продолжает быть активным. Если пользователь пытается снова выполнить вход, может отобразиться интерфейс входа, в котором может не потребоваться повторно вводить учетные данные, так как сеанс на устройстве остается активным.

Чтобы добавить функцию выхода, добавьте следующий метод в класс `MainActivity`. Этот метод выполняет перебор всех учетных записей и удаляет их.

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Вызов API Microsoft Graph

Получив маркер, можно выполнить запрос к [API Microsoft Graph](https://graph.microsoft.com). Маркер доступа будет содержаться в `AuthenticationResult` внутри метода обратного вызова проверки подлинности `onSuccess()`. Чтобы создать авторизованный запрос, вашему приложению потребуется добавить маркер доступа в заголовок HTTP:

| ключ заголовка    | value                 |
| ------------- | --------------------- |
| Авторизация | Bearer \<маркер доступа> |

Добавьте следующие два метода в класс `MainActivity` для вызова графа и обновления пользовательского интерфейса:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Приложения для нескольких учетных записей

Это приложение создано для сценария с одной учетной записью. MSAL также поддерживает сценарии с несколькими учетными записями, но это требует дополнительной работы с приложениями. Вам нужно будет создать пользовательский интерфейс, чтобы помочь пользователю выбрать нужную учетную запись для каждого действия, для которого требуются маркеры. Кроме того, приложение может реализовать эвристику для выбора нужной учетной записи с помощью метода `getAccounts()`.

## <a name="test-your-app"></a>Тестирование приложения

### <a name="run-locally"></a>Локальный запуск

Выполните сборку и развертывание приложения на тестовом устройстве или в эмуляторе. При этом должен выполняться вход в приложение и получение маркеров для учетных записей Azure AD или личных учетных записей Майкрософт.

После выполнения входа в приложении будут отображаться данные, возвращенные из конечной точки Microsoft Graph `/me`.

### <a name="consent"></a>Согласие на предоставление разрешений

При первом входе любого пользователя в приложение от платформы удостоверений Майкрософт появится запрос на предоставление согласия на запрашиваемые разрешения.  Хотя большинство пользователей могут дать согласие, некоторые арендаторы Azure AD отключили согласие пользователей, что предусматривает получение согласия администраторов от имени всех пользователей. Для поддержки этого сценария обязательно зарегистрируйте области своего приложения на портале Azure.

## <a name="get-help"></a>Получение справки

Если у вас возникли вопросы касательно этого руководства или платформы удостоверений Майкрософт, см. статью, посвященную [справке и поддержке](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)