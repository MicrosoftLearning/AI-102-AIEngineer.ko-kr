---
lab:
  title: Language Understanding 클라이언트 애플리케이션 만들기
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: 36f75e47910707c959495140be43c4196649d471
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "137819479"
---
# <a name="create-a-language-understanding-client-application"></a>Language Understanding 클라이언트 애플리케이션 만들기

Language Understanding 서비스에서는 언어 모델을 캡슐화하는 앱을 정의할 수 있습니다. 애플리케이션은 이 모델을 통해 사용자의 자연어 입력을 해석하고, 사용자의 *의도*(사용자가 달성하려는 목표)를 예측하고, 해당 의도를 적용해야 하는 *엔터티* 를 식별할 수 있습니다. REST 인터페이스를 통해 Language Understanding 앱을 직접 사용하거나 언어별 SDK(소프트웨어 개발 키트)를 통해 앱을 사용하는 클라이언트 애플리케이션을 만들 수 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-language-understanding-resources"></a>Language Understanding 리소스 만들기

Azure 구독에 Language Understanding 작성 및 예측 리소스가 이미 포함되어 있으면 이 연습에서 해당 리소스를 사용할 수 있습니다. 그렇지 않은 경우에는 다음 지침에 따라 해당 리소스를 만듭니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&amp;#65291;리소스 생성** 단추를 선택하고, *언어 이해* 를 감지하며, 다음 설정을 사용하여 **Language Understanding** 리소스를 생성합니다.
    - **생성 옵션**: 둘 다
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **이름**: *고유 이름 입력*
    - **작성 위치**: *기본 위치 선택*
    - **가격 책정 계층 작성**: F0
    - **예측 위치**: *작성 위치와 <u>동일한 위치</u> 선택* .
    - **예측 가격 책정 계층**: F0(F0을 사용할 수 없으면 S0 선택)

3. 리소스를 만들 때까지 기다렸다가 두 개의 Language Understanding 리소스가 프로비저닝되었는지 확인합니다. 하나는 작성용이고 다른 하나는 예측용입니다. 이 항목을 생성된 리소스 그룹으로 이동하여 두 항목을 모두 볼 수 있습니다.

## <a name="import-train-and-publish-a-language-understanding-app"></a>Language Understanding 앱 가져오기, 학습 및 게시

이전 연습에서 만든 **Clock** 앱이 이미 있으면 이 연습에서 해당 앱을 사용할 수 있습니다. 그렇지 않은 경우에는 다음 지침에 따라 해당 앱을 만듭니다.

1. 새 브라우저 탭에서 Language Understanding 포털 `https://www.luis.ai`를 엽니다.
2. Azure 구독과 연결된 Microsoft 계정으로 로그인합니다. Language Understanding 포털에 처음 로그인하는 경우 계정 세부 정보에 액세스할 수 있는 일부 권한을 앱에 부여해야 할 수 있습니다. 그런 후에 Azure 구독 및 방금 만든 작성 리소스를 선택하여 *시작* 단계를 완료합니다.
3. **대화 앱** 페이지의 **&#65291;새 앱** 옆에 있는 드롭다운 목록을 확인한 후 **LU로 가져오기** 를 선택합니다.
프로젝트 폴더에서 이 연습용 랩 파일이 포함된 **10-luis-client** 하위 폴더로 이동하여 **Clock.lu** 를 선택합니다. 그런 다음 clock 앱의 고유한 이름을 지정합니다.
4. 효과적인 Language Understanding 앱을 만들기 위한 팁이 포함된 패널이 표시되면 닫습니다.
5. Language Understanding 포털 위쪽에서 **학습** 을 선택하여 앱을 학습시킵니다.
6. Language Understanding 포털 오른쪽 위에서 **게시** 를 선택하여 **프로덕션 슬롯** 에 앱을 게시합니다.
7. 게시가 완료되면 Language Understanding 포털 위쪽에서 **관리** 를 선택합니다.
8. **설정** 페이지에서 **앱 ID** 를 확인합니다. 클라이언트 애플리케이션이 앱을 사용하려면 이 ID가 필요합니다.
9. **Azure 리소스** 페이지의 **예측 리소스** 아래에 예측 리소스가 나열되어 있지 않으면 Azure 구독의 예측 리소스를 추가합니다.
10. 예측 리소스의 **기본 키**, **보조 키** 및 **엔드포인트 URL** 을 확인합니다. 클라이언트 애플리케이션이 예측 리소스에 연결하여 인증을 하려면 엔드포인트와 키 중 하나가 필요합니다.

## <a name="prepare-to-use-the-language-understanding-sdk"></a>Language Understanding SDK 사용 준비

이 연습에서는 Clock Language Understanding 앱을 사용하여 사용자 입력에서 의도를 예측하고 적절하게 응답을 하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **10-luis-client** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **clock-client** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Language Understanding SDK 패키지를 설치합니다.

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

**Runtime** (예측) 패키지 외에 **Authoring** 패키지도 있습니다. 이 패키지를 사용하면 Language Understanding 모델 작성 및 관리용 코드를 작성할 수 있습니다.

**Python**

```
pip install azure-cognitiveservices-language-luis==0.7.0
```
    
Python SDK 패키지에는 **prediction** 및 **authoring** 용 클래스가 모두 포함되어 있습니다.

3. **clock-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 파일에 포함되어 있는 구성 값을 업데이트하여 Language Understanding 앱의 **앱 ID** 및 예측 리소스의 **엔드포인트 URL** 과 **키** 중 하나를 포함합니다(Language Understanding 포털의 앱 **관리** 페이지에서 확인 가능).

4. **clock-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: clock-client.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Language Understanding 예측 SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

**C#**

```C#
// Import namespaces
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

**Python**

```Python
# Import namespaces
from azure.cognitiveservices.language.luis.runtime import LUISRuntimeClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="get-a-prediction-from-the-language-understanding-app"></a>Language Understanding 앱에서 예측 가져오기

이제 SDK를 사용하여 Language Understanding 앱에서 예측을 가져오는 코드를 구현할 수 있습니다.

1. **Main** 함수에서 구성 파일의 앱 ID, 예측 엔드포인트 및 키를 로드하는 코드가 이미 제공되어 있음을 확인합니다. 그런 후 **LU 앱용 클라이언트 만들기** 주석을 찾아서 다음 코드를 추가하여 Language Understanding 앱용 예측 클라이언트를 만듭니다.

**C#**

```C#
// Create a client for the LU app
var credentials = new Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.ApiKeyServiceClientCredentials(predictionKey);
var luClient = new LUISRuntimeClient(credentials) { Endpoint = predictionEndpoint };
```

**Python**

```Python
# Create a client for the LU app
credentials = CognitiveServicesCredentials(lu_prediction_key)
lu_client = LUISRuntimeClient(lu_prediction_endpoint, credentials)
```

2. **Main** 의 코드는 사용자가 "quit"을 입력할 때까지 사용자 입력을 제공하라는 메시지를 표시합니다. 이 루프 내에서 **LU 앱을 호출하여 의도 및 엔터티 가져오기** 주석을 찾은 후 다음 코드를 추가합니다.

**C#**

```C#
// Call the LU app to get intent and entities
var slot = "Production";
var request = new PredictionRequest { Query = userText };
PredictionResponse predictionResponse = await luClient.Prediction.GetSlotPredictionAsync(luAppId, slot, request);
Console.WriteLine(JsonConvert.SerializeObject(predictionResponse, Formatting.Indented));
Console.WriteLine("--------------------\n");
Console.WriteLine(predictionResponse.Query);
var topIntent = predictionResponse.Prediction.TopIntent;
var entities = predictionResponse.Prediction.Entities;
```

**Python**

```Python
# Call the LU app to get intent and entities
request = { "query" : userText }
slot = 'Production'
prediction_response = lu_client.prediction.get_slot_prediction(lu_app_id, slot, request)
top_intent = prediction_response.prediction.top_intent
entities = prediction_response.prediction.entities
print('Top Intent: {}'.format(top_intent))
print('Entities: {}'.format (entities))
print('-----------------\n{}'.format(prediction_response.query))
```

Language Understanding 앱을 호출하면 예측이 반환됩니다. 이 예측에는 상위(가장 가능성이 높은) 의도, 그리고 입력 발화에서 감지된 모든 엔터티가 포함됩니다. 이제 클라이언트 애플리케이션은 해당 예측을 사용하여 적절한 작업을 결정한 다음 수행해야 합니다.

3. **적절한 작업 적용** 주석을 찾은 후 다음 코드를 추가합니다. 이 코드는 애플리케이션에서 지원하는 의도(**GetTime**, **GetDate** 및 **GetDay**)를 확인한 다음 관련 엔터티가 감지되었는지를 확인합니다. 그런 후에 기존 함수를 호출하여 적절한 응답을 생성합니다.

**C#**

```C#
// Apply the appropriate action
switch (topIntent)
{
    case "GetTime":
        var location = "local";
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a location entity
            if (entities.ContainsKey("Location"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Location"].ToString());
                // ML entities are strings, get the first one
                location = entityJson[0].ToString();
            }
        }

        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;

    case "GetDay":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Date entity
            if (entities.ContainsKey("Date"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Date"].ToString());
                // Regex entities are strings, get the first one
                date = entityJson[0].ToString();
            }
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;

    case "GetDate":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entities.Count > 0)
        {
            // Check for a Weekday entity
            if (entities.ContainsKey("Weekday"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Weekday"].ToString());
                // List entities are lists
                day = entityJson[0][0].ToString();
            }
        }
        // Get the date for the specified day
        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;

    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("Try asking me for the time, the day, or the date.");
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if top_intent == 'GetTime':
    location = 'local'
    # Check for entities
    if len(entities) > 0:
        # Check for a location entity
        if 'Location' in entities:
            # ML entities are strings, get the first one
            location = entities['Location'][0]
    # Get the time for the specified location
    print(GetTime(location))

elif top_intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if len(entities) > 0:
        # Check for a Date entity
        if 'Date' in entities:
            # Regex entities are strings, get the first one
            date_string = entities['Date'][0]
    # Get the day for the specified date
    print(GetDay(date_string))

elif top_intent == 'GetDate':
    day = 'today'
    # Check for entities
    if len(entities) > 0:
        # Check for a Weekday entity
        if 'Weekday' in entities:
            # List entities are lists
            day = entities['Weekday'][0][0]
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('Try asking me for the time, the day, or the date.')
```
    
4. 변경 내용을 저장하고 **clock-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python clock-client.py
```

5. 메시지가 표시되면 발화를 입력하여 애플리케이션을 테스트합니다. 예를 들어 다음을 시도해 보세요.

    *Hello*
    
    *What time is it?*

    *What's the time in London?*

    *What's the date?*

    *What date is Sunday?*

    *What day is it?*

    *What day is 01/01/2025?*

> **참고**: 여기서는 애플리케이션에 의도적으로 단순한 논리가 사용되었으며 몇 가지 제한이 적용되었습니다. 예를 들어 시간을 가져올 때는 제한된 도시 세트만 지원되며 일광 절약 시간제는 무시됩니다. 이러한 방식이 사용된 이유는 일반적인 Language Understanding 사용 패턴의 예를 확인하기 위해서입니다. 이러한 패턴에서 애플리케이션은 다음 작업을 수행해야 합니다.
>
>   1. 예측 엔드포인트에 연결
>   2. 발화를 제출하여 예측 가져오기
>   3. 예측된 의도와 엔터티에 적절하게 응답하는 논리 구현

6. 테스트를 완료한 후 *quit* 을 입력합니다.

## <a name="more-information"></a>추가 정보

Language Understanding 클라이언트 만들기에 대해 자세히 알아보려면 [개발자 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)를 참조하세요.
