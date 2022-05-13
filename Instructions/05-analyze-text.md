---
lab:
  title: 텍스트 분석
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: 27cd22e81d4fe8fda27e6fc960d3b3aeb8debded
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2022
ms.locfileid: "137819448"
---
# <a name="analyze-text"></a>텍스트 분석

**언어** 서비스는 언어 감지, 감정 분석, 핵심 구 추출, 엔터티 인식 등의 텍스트 분석을 지원하는 Cognitive Service입니다.

회사 웹 사이트로 제출된 호텔 리뷰를 처리하려는 여행사의 경우를 예로 들어 보겠습니다. 이 여행사는 언어 서비스를 사용하여 각 리뷰를 작성한 언어, 리뷰의 감정(긍정적, 중립, 부정적), 리뷰에 설명되어 있는 주요 토픽을 나타낼 수 있는 핵심 구, 그리고 리뷰에 언급되어 있는 장소, 주요 건물, 사람 등의 명명된 엔터티를 확인할 수 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 이 페이지에 표시되는 키 중 하나와 엔드포인트가 필요합니다.

## <a name="prepare-to-use-the-language-sdk-for-text-analytics"></a>텍스트 분석에 언어 SDK 사용 준비

이 연습에서는 언어 서비스 Text Analytics SDK를 사용해 호텔 리뷰를 분석하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **05-analyze-text** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **text-analysis** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Text Analytics SDK 패키지를 설치합니다.
    
    **C#**
    
    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    ```
    
    **Python**
    
    ```
    pip install azure-ai-textanalytics==5.1.0
    ```
    
3. **text-analysis** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Service 리소스용 **엔드포인트** 및 인증 **키** 를 반영하여 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.

4. **text-analysis** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: text-analysis.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Text Analytics SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

    **C#**
    
    ```C#
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**

    ```Python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

5. **Main** 함수에서 구성 파일의 Cognitive Services 엔드포인트 및 키를 로드하는 코드가 이미 제공되어 있음을 확인합니다. 그런 후 **엔드포인트와 키를 사용하여 클라이언트 만들기** 주석을 찾아서 다음 코드를 추가하여 Text Analytics API용 클라이언트를 만듭니다.

    **C#**

    ```C#
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(cogSvcKey);
    Uri endpoint = new Uri(cogSvcEndpoint);
    TextAnalyticsClient CogClient = new TextAnalyticsClient(endpoint, credentials);
    ```

    **Python**

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(cog_key)
    cog_client = TextAnalyticsClient(endpoint=cog_endpoint, credential=credential)
    ```

6. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

6. 오류가 발생하지 않고 코드가 실행되어 **reviews** 폴더에 있는 각 리뷰 텍스트 파일의 내용이 표시되는지 확인합니다. 애플리케이션은 Text Analytics API용 클라이언트를 만들었지만 해당 클라이언트를 사용하지는 않습니다. 다음 절차에서 애플리케이션이 서비스를 사용하도록 수정합니다.

## <a name="detect-language"></a>언어 검색

Text Analytics API용 클라이언트를 만들었으므로 해당 클라이언트를 사용해 각 리뷰를 작성한 언어를 감지해 보겠습니다.

1. 프로그램의 **Main** 함수에서 **언어 가져오기** 주석을 찾습니다. 그 후에 이 주석 아래에 각 리뷰 문서의 언어를 감지하는 데 필요한 코드를 추가합니다.

    **C#**
    
    ```C
    // Get language
    DetectedLanguage detectedLanguage = CogClient.DetectLanguage(text);
    Console.WriteLine($"\nLanguage: {detectedLanguage.Name}");
    ```

    **Python**
    
    ```Python
    # Get language
    detectedLanguage = cog_client.detect_language(documents=[text])[0]
    print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))
    ```

    > **참고**: 이 예제에서는 각 리뷰를 개별적으로 분석하므로 각 파일 분석을 위해 서비스를 각기 별도로 호출합니다. 이 방식 대신 문서 컬렉션을 만든 후 단일 호출에서 서비스에 컬렉션을 전달하는 방식을 사용할 수 있습니다. 두 방식에서 모두 서비스의 응답은 문서 컬렉션으로 구성되어 있습니다. 따라서 위 Python 코드의 응답에는 첫 번째(유일한) 문서의 인덱스([0])만 지정되어 있습니다.

6. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

7. 출력을 확인합니다. 이번에는 각 리뷰의 언어가 식별되었습니다.

## <a name="evaluate-sentiment"></a>감정 평가

감정 분석은 텍스트를 긍정적 또는 부정적으로 분류할 때 흔히 사용되는 기술입니다(중립적 또는 혼합으로 분류할 수도 있음).     감정 분석은 소셜 미디어 게시물, 제품 리뷰, 그리고 텍스트 감정이 유용한 인사이트를 제공할 수 있는 기타 항목을 분석하는 데 흔히 사용됩니다.

1. 프로그램의 **Main** 함수에서 **감정 가져오기** 주석을 찾습니다. 그 후에 이 주석 아래에 각 리뷰 문서의 감정을 감지하는 데 필요한 코드를 추가합니다.

    **C#**
    
    ```C
    // Get sentiment
    DocumentSentiment sentimentAnalysis = CogClient.AnalyzeSentiment(text);
    Console.WriteLine($"\nSentiment: {sentimentAnalysis.Sentiment}");
    ```

    **Python**
    
    ```Python
    # Get sentiment
    sentimentAnalysis = cog_client.analyze_sentiment(documents=[text])[0]
    print("\nSentiment: {}".format(sentimentAnalysis.sentiment))
    ```

2. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

3. 출력을 확인합니다. 리뷰의 감정이 감지되었습니다.

## <a name="identify-key-phrases"></a>핵심 구 식별

텍스트 본문에서 핵심 구를 식별하면 텍스트에 설명되어 있는 주요 토픽을 확인하는 데 유용할 수 있습니다.

1. 프로그램의 **Main** 함수에서 **핵심 구 가져오기** 주석을 찾습니다. 그 후에 이 주석 아래에 각 리뷰 문서의 핵심 구를 감지하는 데 필요한 코드를 추가합니다.

    **C#**

    ```C
    // Get key phrases
    KeyPhraseCollection phrases = CogClient.ExtractKeyPhrases(text);
    if (phrases.Count > 0)
    {
        Console.WriteLine("\nKey Phrases:");
        foreach(string phrase in phrases)
        {
            Console.WriteLine($"\t{phrase}");
        }
    }
    ```
    
    **Python**
    
    ```Python
    # Get key phrases
    phrases = cog_client.extract_key_phrases(documents=[text])[0].key_phrases
    if len(phrases) > 0:
        print("\nKey Phrases:")
        for phrase in phrases:
            print('\t{}'.format(phrase))
    ```

2. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. 출력을 확인합니다. 각 문서에는 리뷰 내용과 관련한 몇 가지 인사이트를 제공하는 핵심 구가 포함되어 있습니다.

## <a name="extract-entities"></a>엔터티 추출

문서나 기타 텍스트 본문에서는 사람, 장소, 기간 또는 기타 엔터티를 언급하는 경우가 많습니다. Text Analytics API는 텍스트 내 엔터티의 여러 범주(및 하위 범주)를 감지할 수 있습니다.

1. 프로그램의 **Main** 함수에서 **엔터티 가져오기** 주석을 찾습니다. 그 후에 이 주석 아래에 각 리뷰에 언급되어 있는 엔터티를 식별하는 데 필요한 코드를 추가합니다.

    **C#**
    
    ```C
    // Get entities
    CategorizedEntityCollection entities = CogClient.RecognizeEntities(text);
    if (entities.Count > 0)
    {
        Console.WriteLine("\nEntities:");
        foreach(CategorizedEntity entity in entities)
        {
            Console.WriteLine($"\t{entity.Text} ({entity.Category})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get entities
    entities = cog_client.recognize_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nEntities")
        for entity in entities:
            print('\t{} ({})'.format(entity.text, entity.category))
    ```

2. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. 출력을 확인합니다. 텍스트에서 엔터티가 감지되었습니다.

## <a name="extract-linked-entities"></a>연결된 엔터티 추출

Text Analytics API는 범주화된 엔터티뿐 아니라 Wikipedia 등의 데이터 원본에 대한 알려진 링크가 있는 엔터티도 감지할 수 있습니다.

1. 프로그램의 **Main** 함수에서 **연결된 엔터티 가져오기** 주석을 찾습니다. 그 후에 이 주석 아래에 각 리뷰에 언급되어 있는 연결된 엔터티를 식별하는 데 필요한 코드를 추가합니다.

    **C#**
    
    ```C
    // Get linked entities
    LinkedEntityCollection linkedEntities = CogClient.RecognizeLinkedEntities(text);
    if (linkedEntities.Count > 0)
    {
        Console.WriteLine("\nLinks:");
        foreach(LinkedEntity linkedEntity in linkedEntities)
        {
            Console.WriteLine($"\t{linkedEntity.Name} ({linkedEntity.Url})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get linked entities
    entities = cog_client.recognize_linked_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nLinks")
        for linked_entity in entities:
            print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
    ```

2. 변경 내용을 저장하고 **text-analysis** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. 출력을 확인합니다. 연결된 엔터티가 식별되었습니다.

## <a name="more-information"></a>추가 정보

**언어** 서비스를 사용하는 방법에 대한 자세한 내용은 [Text Analytics 설명서](https://docs.microsoft.com/azure/cognitive-services/language-service/)를 참조하세요.
