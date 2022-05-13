---
lab:
  title: 대화 언어 이해 클라이언트 애플리케이션 만들기(미리 보기)
ms.openlocfilehash: 486a6716a189156739e9107824e561f7bb8fa95c
ms.sourcegitcommit: 2c92ea1491cac72a4765755cf2bc6d8b5f657a46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2022
ms.locfileid: "141580028"
---
# <a name="create-a-language-service-client-application"></a>언어 서비스 클라이언트 애플리케이션 만들기

언어용 Azure Cognitive Service의 대화 언어 이해 기능을 사용하면 클라이언트 앱이 사용자의 자연어 입력을 해석하고, 사용자 의도(달성하려는 것)를 예측하고, 의도를 적용해야 하는 엔터티를 식별하는 데 사용할 수 있는 대화 언어 모델을 정의할 수 있습니다.  REST 인터페이스를 통해 직접 또는 언어별 SDK(소프트웨어 개발 키트)를 통해 대화 언어 이해 모델을 사용하는 클라이언트 애플리케이션을 만들 수 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.

2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.

3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.

4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-language-service-resources"></a>언어 서비스 리소스 만들기

Azure 구독에 언어 서비스 리소스가 이미 있는 경우 이 연습에서 사용할 수 있습니다. 그렇지 않은 경우에는 관련 지침에 따라 리소스를 만듭니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.

2. **&#65291;리소스 만들기** 단추를 선택하고, 언어 서비스를 검색하고, 다음 설정을 사용하여 **언어 서비스** 리소스를 만듭니다.

    - **기본 기능** 전체
    - **사용자 지정 기능**: 없음
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: *westus2*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: *S*
    - **약관**: 확인하려면 확인란 선택
    - **책임 있는 AI 알림**: 확인하려면 확인란 선택

3. 리소스가 생성될 때까지 기다립니다. 리소스를 만든 리소스 그룹으로 이동하여 리소스를 확인할 수 있습니다.

## <a name="import-train-and-publish-a-conversational-language-understanding-model"></a>대화 언어 이해 모델 가져오기, 학습 및 게시

이전 랩 또는 연습에서 만든 **Clock** 앱이 이미 있으면 이 연습에서 사용할 수 있습니다. 그렇지 않은 경우에는 다음 지침에 따라 해당 앱을 만듭니다.

1. 새 브라우저 탭에서 Language Studio - 미리 보기 포털(`https://language.cognitive.azure.com`)을 엽니다.

2. Azure 구독과 연결된 Microsoft 계정으로 로그인합니다. 언어 서비스 포털에 처음 로그인하는 경우 계정 세부 정보 액세스를 위한 몇 가지 권한을 앱에 부여해야 할 수 있습니다. 그런 후에 Azure 구독 및 방금 만든 작성 리소스를 선택하여 *시작* 단계를 완료합니다.

3. **대화 언어 이해** 페이지를 엽니다.

3. **&#65291;새 프로젝트 만들기** 옆에 있는 드롭다운 목록을 보고 **가져오기** 를 선택합니다. **파일 선택** 을 클릭한 다음, 이 연습의 랩 파일이 포함된 프로젝트 폴더의 **10b-clu-client-(preview)** 하위 폴더로 이동합니다. **Clock.json** 을 선택하고, **열기** 를 클릭한 다음, **완료** 를 클릭합니다.

4. 효과적인 언어 서비스 앱을 만들 수 있는 팁이 포함된 패널이 표시되면 해당 패널을 닫습니다.

5. Language Studio 포털 왼쪽에서 **모델 학습** 을 선택하여 앱을 학습시킵니다. **학습 작업 시작** 을 클릭하고, 모델 이름을 **Clock** 으로 지정하고, 학습을 사용한 평가가 사용하도록 설정되어 있는지 확인합니다. 학습을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    > **참고**: 모델 이름 **Clock** 은 clock-client 코드(랩의 뒷부분에서 사용됨)로 하드 코딩되므로 이름은 설명된 대로 정확히 대소문자를 구분하고 철자를 표시합니다. 

6. Language Studio 포털 왼쪽에서 **모델 배포** 를 선택하고 **배포 추가** 를 사용하여 **production** 이라는 Clock 모델에 대한 배포를 만듭니다.

    > **참고**: 배포 이름 **production** 은 clock-client 코드(랩의 뒷부분에서 사용됨)로 하드 코딩되므로 이름은 설명된 대로 정확히 대소문자를 구분하고 철자를 표시합니다. 

7. 배포가 완료된 후 **production** 배포를 선택한 다음, **예측 URL 가져오기** 를 클릭합니다. 클라이언트 애플리케이션은 배포된 모델을 사용하려면 엔드포인트 URL이 필요합니다.

8. Language Studio 포털 왼쪽에서 **프로젝트 설정** 을 선택하고 **기본 키** 를 적어 둡니다. 클라이언트 애플리케이션은 배포된 모델을 사용하려면 기본 키가 필요합니다.

9. 클라이언트 애플리케이션은 배포된 모델에 연결하고 인증을 받으려면 예측 URL 및 언어 서비스 키의 정보가 필요합니다.

## <a name="prepare-to-use-the-language-service-sdk"></a>언어 서비스 SDK 사용 준비

이 연습에서는 Clock 모델(게시된 대화 언어 이해 모델)을 사용하여 사용자 입력의 의도를 예측하고 적절하게 대응하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **.NET** 또는 **Python** 용 SDK를 사용하도록 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **10b-clu-client-(preview)** 폴더로 이동하고 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.

2. **clock-client** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기** 를 선택합니다. 그런 다음, 언어 기본 설정에 적합한 명령을 실행하여 대화 언어 서비스 SDK 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Azure.AI.Language.Conversations --version 1.0.0-beta.2
    ```

    **Python**

    ```
    pip install azure-ai-language-conversations --pre
    python -m pip install python-dotenv
    python -m pip install python-dateutil
    ```

3. **clock-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.

    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 포함된 구성 값을 업데이트하여 언어 리소스에 대한 **엔드포인트 URL** 및 **기본 키** 를 포함합니다. 다음과 같이 Azure Portal 또는 Language Studio에서 필요한 값을 찾을 수 있습니다.

    - Azure Portal: 언어 리소스를 엽니다. **리소스 관리** 에서 **키 및 엔드포인트** 를 선택합니다. **KEY 1** 및 **엔드포인트** 값을 구성 설정 파일에 복사합니다.
    - Language Studio: **Clock** 프로젝트를 엽니다. 언어 서비스 엔드포인트는 **모델 배포** 페이지의 **예측 URL 가져오기** 아래에서 찾을 수 있고 **기본 키** 는 **프로젝트 설정** 페이지에서 찾을 수 있습니다. 예측 URL의 언어 서비스 엔드포인트 부분은 **.cognitiveservices.azure.com/** 로 끝납니다. 예: `https://ai102-langserv.cognitiveservices.azure.com/`

4. **clock-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: clock-client.py

    코드 파일을 열고 위쪽에서 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음, 이 주석 아래에 다음 언어별 코드를 추가하여 언어 서비스 SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

    **C#**

    ```C#
    // Import namespaces
    using Azure;
    using Azure.AI.Language.Conversations;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.language.conversations import ConversationAnalysisClient
    from azure.ai.language.conversations.models import ConversationAnalysisOptions
    ```

## <a name="get-a-prediction-from-the-conversational-language-model"></a>대화 언어 모델에서 예측 가져오기

이제 SDK를 사용하여 언어 이해 모델에서 예측을 가져오는 코드를 구현할 수 있습니다.

1. **Main** 함수에서 구성 파일의 예측 엔드포인트 및 키를 로드하는 코드가 이미 제공되어 있음을 알 수 있습니다. 그런 후 **언어 서비스 모델에 대한 클라이언트 만들기** 주석을 찾고 다음 코드를 추가하여 언어 서비스 앱에 대한 예측 클라이언트를 만듭니다.

    **C#**

    ```C#
    // Create a client for the Language service model
    Uri lsEndpoint = new Uri(predictionEndpoint);
    AzureKeyCredential lsCredential = new AzureKeyCredential(predictionKey);

    ConversationAnalysisClient lsClient = new ConversationAnalysisClient(lsEndpoint, lsCredential);
    ```

    **Python**

    ```Python
    # Create a client for the Language service model
    client = ConversationAnalysisClient(
        ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    ```

2. **Main** 의 코드는 사용자가 "quit"을 입력할 때까지 사용자 입력을 제공하라는 메시지를 표시합니다. 이 루프 내에서 **언어 서비스 모델을 호출하여 의도 및 엔터티 가져오기** 주석을 찾고 다음 코드를 추가합니다.

    **C#**

    ```C#
    // Call the Language service model to get intent and entities
    var lsProject = "Clock";
    var lsSlot = "production";

    ConversationsProject conversationsProject = new ConversationsProject(lsProject, lsSlot);
    Response<AnalyzeConversationResult> response = await lsClient.AnalyzeConversationAsync(userText, conversationsProject);

    ConversationPrediction conversationPrediction = response.Value.Prediction as ConversationPrediction;

    Console.WriteLine(JsonConvert.SerializeObject(conversationPrediction, Formatting.Indented));
    Console.WriteLine("--------------------\n");
    Console.WriteLine(userText);
    var topIntent = "";
    if (conversationPrediction.Intents[0].Confidence > 0.5)
    {
        topIntent = conversationPrediction.TopIntent;
    }

    var entities = conversationPrediction.Entities;
    ```

    **Python**

    ```Python
    # Call the Language service model to get intent and entities
    convInput = ConversationAnalysisOptions(
        query = userText
        )
    
    cls_project = 'Clock'
    deployment_slot = 'production'

    with client:
        result = client.analyze_conversations(
            convInput, 
            project_name=cls_project, 
            deployment_name=deployment_slot
            )

    # list the prediction results
    top_intent = result.prediction.top_intent
    entities = result.prediction.entities

    print("view top intent:")
    print("\ttop intent: {}".format(result.prediction.top_intent))
    print("\tcategory: {}".format(result.prediction.intents[0].category))
    print("\tconfidence score: {}\n".format(result.prediction.intents[0].confidence_score))

    print("view entities:")
    for entity in entities:
        print("\tcategory: {}".format(entity.category))
        print("\ttext: {}".format(entity.text))
        print("\tconfidence score: {}".format(entity.confidence_score))

    print("query: {}".format(result.query))
    ```

    언어 서비스 모델을 호출하면 예측/결과가 반환됩니다. 여기에는 상위(가장 가능성이 높은) 의도, 그리고 입력 발화에서 검색된 모든 엔터티가 포함됩니다. 이제 클라이언트 애플리케이션은 해당 예측을 사용하여 적절한 작업을 결정한 다음 수행해야 합니다.

3. **적절한 작업 적용** 주석을 찾고 다음 코드를 추가합니다. 이 코드는 애플리케이션에서 지원하는 의도(**GetTime**, **GetDate** 및 **GetDay**)를 확인하고 관련 엔터티가 검색되었는지를 확인한 후 기존 함수를 호출하여 적절한 응답을 생성합니다.

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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Location")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        location = entity.Text;
                    }
                    
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Date")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        date = entity.Text;
                    }
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
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Weekday")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        day = entity.Text;
                    }
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
            for entity in entities:
                if 'Location' == entity.category:
                    # ML entities are strings, get the first one
                    location = entity.text
        # Get the time for the specified location
        print(GetTime(location))

    elif top_intent == 'GetDay':
        date_string = date.today().strftime("%m/%d/%Y")
        # Check for entities
        if len(entities) > 0:
            # Check for a Date entity
            for entity in entities:
                if 'Date' == entity.category:
                    # Regex entities are strings, get the first one
                    date_string = entity.text
        # Get the day for the specified date
        print(GetDay(date_string))

    elif top_intent == 'GetDate':
        day = 'today'
        # Check for entities
        if len(entities) > 0:
            # Check for a Weekday entity
            for entity in entities:
                if 'Weekday' == entity.category:
                # List entities are lists
                    day = entity.text
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

> **참고**: 여기서는 애플리케이션에 의도적으로 단순한 논리가 사용되었으며 몇 가지 제한이 적용되었습니다. 예를 들어 시간을 가져올 때는 제한된 도시 세트만 지원되며 일광 절약 시간제는 무시됩니다. 목표는 애플리케이션이 수행해야 하는 일반적인 언어 서비스 사용 패턴의 예를 확인하는 것입니다.
>
>   1. 예측 엔드포인트에 연결
>   2. 발화를 제출하여 예측 가져오기
>   3. 예측된 의도와 엔터티에 적절하게 응답하는 논리 구현

6. 테스트를 완료한 후 *quit* 을 입력합니다.

## <a name="more-information"></a>자세한 정보

언어 서비스 클라이언트 만들기에 대해 자세히 알아보려면 [개발자 설명서](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)를 참조하세요.
