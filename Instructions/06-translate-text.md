---
lab:
  title: 텍스트 번역
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: 4ca4394ce5d9456abeabbdded1ee219f271f284e
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "137819573"
---
# <a name="translate-text"></a>텍스트 번역

**Translator** 서비스는 언어 간에 텍스트를 번역할 수 있는 Cognitive Service입니다.

회사 웹 사이트로 제출된 호텔 리뷰를 살펴보고 분석에 사용되는 언어로는 영어를 표준으로 사용하려는 여행사의 경우를 예로 들어 보겠습니다. 이 회사는 Translator 서비스를 사용해 각 리뷰가 작성된 언어를 확인하고 영어로 작성되지 않은 리뷰는 작성 시에 사용했던 원본 언어에 관계없이 영어로 번역할 수 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

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
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 키 중 하나, 그리고 이 페이지에서 서비스가 프로비전된 위치가 필요합니다.

## <a name="prepare-to-use-the-translator-service"></a>Translator 서비스 사용 준비

이 연습에서는 Translator REST API를 사용해 호텔 리뷰를 번역하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 의 API 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **06-translate-text** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **text-translation** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Services 리소스용 인증 **키** 와 해당 리소스를 배포한 **위치**(엔드포인트 <u>아님</u>)를 포함하도록 해당 파일에 포함된 구성 값을 업데이트합니다. Cognitive Services 리소스의 **키 및 엔드포인트** 페이지에서 둘 다 복사해야 합니다. 변경 내용을 저장합니다.
3. **text-translation** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: text-translation.py

    코드 파일을 열고 포함되어 있는 코드를 살펴봅니다.

4. **Main** 함수에서 구성 파일의 Cognitive Services 키 및 지역을 로드하는 코드가 이미 제공되어 있음을 확인합니다. Translation 서비스용 엔드포인트도 코드에 지정되어 있습니다.
5. **text-translation** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 연 후에 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

6. 오류가 발생하지 않고 코드가 실행되어 **reviews** 폴더에 있는 각 리뷰 텍스트 파일의 내용이 표시되는지 확인합니다. 애플리케이션은 현재 Translator 서비스를 사용하고 있지 않습니다. 다음 절차에서 애플리케이션이 서비스를 사용하도록 수정합니다.

## <a name="detect-language"></a>언어 검색

Translator 서비스는 번역할 텍스트의 원본 언어를 자동 감지할 수 있습니다. 또한 텍스트가 작성된 언어를 명시적으로 감지할 수도 있습니다.

1. 코드 파일에서 **GetLanguage** 함수를 찾습니다. 이 함수는 현재 모든 텍스트 값에 대해 "en"을 반환합니다.
2. **GetLanguage** 함수의 **Translator 감지 기능 사용** 주석 아래에 다음 코드를 추가하여 Translator REST API를 사용해 지정된 텍스트의 언어를 감지합니다. 이때 언어를 반환하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

**C#**

```C#
// Use the Translator detect function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/detect?api-version=3.0";
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get language
        JArray jsonResponse = JArray.Parse(responseContent);
        language = (string)jsonResponse[0]["language"]; 
    }
}
```

**Python**

```Python
# Use the Translator detect function
path = '/detect'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0'
}

headers = {
'Ocp-Apim-Subscription-Key': cog_key,
'Ocp-Apim-Subscription-Region': cog_region,
'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get language
language = response[0]["language"]
```

3. 변경 내용을 저장하고 **text-translation** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. 출력을 확인합니다. 이번에는 각 리뷰의 언어가 식별되었습니다.

## <a name="translate-text"></a>텍스트 번역

이제 애플리케이션이 리뷰가 작성된 언어를 확인할 수 있습니다. 따라서 Translator 서비스를 사용해 영어 이외의 언어로 작성된 리뷰를 영어로 번역할 수 있습니다.

1. 코드 파일에서 **Translate** 함수를 찾습니다. 이 함수는 현재 모든 텍스트 값에 대해 빈 문자열을 반환합니다.
2. **Translate** 함수의 **Translator 번역 기능 사용** 주석 아래에 다음 코드를 추가하여 Translator REST API를 사용해 지정된 텍스트를 원본 언어에서 영어로 번역합니다. 이때 번역을 반환하는 함수 끝부분의 코드를 바꾸지 않도록 주의하세요.

**C#**

```C#
// Use the Translator translate function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/translate?api-version=3.0&from=" + sourceLanguage + "&to=en" ;
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get translation
        JArray jsonResponse = JArray.Parse(responseContent);
        translation = (string)jsonResponse[0]["translations"][0]["text"];  
    }
}
```

**Python**

```Python
# Use the Translator translate function
path = '/translate'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0',
    'from': source_language,
    'to': ['en']
}

headers = {
    'Ocp-Apim-Subscription-Key': cog_key,
    'Ocp-Apim-Subscription-Region': cog_region,
    'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get translation
translation = response[0]["translations"][0]["text"]
```

3. 변경 내용을 저장하고 **text-translation** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. 출력을 확인합니다. 영어가 아닌 리뷰가 영어로 번역되었습니다.

## <a name="more-information"></a>추가 정보

**Translator** 서비스를 사용하는 방법에 대한 자세한 내용은 [Translator 설명서](https://docs.microsoft.com/azure/cognitive-services/translator/)를 참조하세요.
