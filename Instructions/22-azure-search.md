---
lab:
  title: Azure Cognitive Search 솔루션 만들기
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: bba5786ed34cbbe806c74e2b2eec6286cb92554b
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2022
ms.locfileid: "137819513"
---
# <a name="create-an-azure-cognitive-search-solution"></a>Azure Cognitive Search 솔루션 만들기

모든 조직에서는 정보를 사용하여 의사 결정을 내리고, 질문에 답변하고, 효율적으로 업무를 수행합니다. 대부분의 조직에서 발생하는 문제는 정보 부족이 아니라 많은 문서, 데이터베이스 및 정보가 저장되는 기타 자료에서 정보를 찾고 추출하는 데 따르는 어려움입니다.

예를 들어 *Margie's Travel* 은 전 세계 도시 여행 정보를 체계적으로 제공하는 데 특화된 여행사입니다. 시간이 지남에 따라 회사는 고객이 제출한 호텔 리뷰뿐만 아니라 브로슈어 등의 문서로 된 상당한 양의 정보를 축적했습니다. 이 데이터는 여행사와 고객이 여행을 계획할 때 유용한 소스가 되지만, 엄청난 양의 데이터로 인해 특정 고객의 질문에 답변할 때 관련 정보를 찾기가 어려워질 수 있습니다.

Margie's Travel은 이 문제를 해결하기 위해 Azure Cognitive Search를 사용하여 솔루션을 구현할 수 있습니다. 이 솔루션에서는 문서를 더 쉽게 검색할 수 있도록 AI 기반 인식 기술을 사용하여 문서를 인덱싱하고 보강합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

Margie’s Travel을 위해 만들 솔루션에는 Azure 구독의 다음 리소스가 필요합니다.

- 인덱싱 및 쿼리를 관리하는 **Azure Cognitive Search** 리소스.
- **Cognitive Services** 리소스는 검색 솔루션이 AI 생성 인사이트로 데이터 소스의 데이터를 보강하는 데 사용할 수 있는 기술에 대한 AI 서비스를 제공합니다.
- 검색할 문서가 저장되는 Blob 컨테이너가 포함된 **스토리지 계정**

> **중요**: Azure Cognitive Search 및 Cognitive Services 리소스는 동일한 위치에 있어야 합니다.

### <a name="create-an-azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스 만들기

1. 웹 브라우저에서 `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *search* 를 검색한 후에 다음 설정을 사용하여 **Azure Cognitive Search** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 새 리소스 그룹 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **서비스 이름**: 고유한 이름 입력
    - **위치**: 위치 선택(Azure Cognitive Search 및 Cognitive Services 리소스는 동일한 위치에 있어야 함)
    - **가격 책정 계층**: 기본

3. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
4. Azure Portal에서 Azure Cognitive Search 리소스 블레이드의 **개요** 페이지를 검토합니다. 이 페이지에서 시각적 인터페이스를 사용하여 데이터 원본, 인덱스, 인덱서, 기술 세트를 비롯한 검색 솔루션의 다양한 구성 요소 만들기, 테스트, 관리, 모니터링을 수행할 수 있습니다.

### <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다. 검색 솔루션은 이를 사용하여 AI에서 생성된 인사이트를 사용하여 데이터 저장소의 데이터를 보강합니다.

1. Azure Portal의 홈페이지로 돌아가서 **&#65291;리소스 만들기** 단추를 선택하고 *Cognitive Services* 를 검색한 다음, 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Azure Cognitive Search 리소스와 동일한 리소스 그룹*
    - **지역**: *Azure Cognitive Search 리소스와 동일한 지역*
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
2. 필요한 확인란을 선택하고 리소스를 만듭니다.
3. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

1. Azure Portal의 홈페이지로 돌아가서 **&#65291;리소스 만들기** 단추를 선택하고 *스토리지 계정* 를 검색한 다음, 다음 설정을 사용하여 **스토리지 계정** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Azure Cognitive Search 리소스 및 Cognitive Services와 동일한 리소스 그룹
    - **스토리지 계정 이름**: 고유한 이름 입력
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **성능**: 표준
    - **복제**: LRS(로컬 중복 스토리지)
2. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
3. **개요** 페이지에서 **구독 ID** 를 확인합니다. 이 ID를 사용하여 스토리지 계정이 프로비전된 구독을 식별합니다.
4. **액세스 키** 페이지에서 스토리지 계정용으로 키 2개가 생성되었음을 확인합니다. 그런 다음 **키 표시** 를 선택하여 키를 확인합니다.

    > **팁**: 다음 절차에서 구독 ID와 키 중 하나가 필요하므로 **스토리지 계정** 블레이드는 열어 두세요.

## <a name="upload-documents-to-azure-storage"></a>Azure Storage에 문서 업로드

이제 필요한 리소스가 준비되었으므로 Azure Storage 계정에 문서 몇 개를 업로드할 수 있습니다.

1. Visual Studio Code의 **탐색기** 창에서 **22-create-a-search-solution** 폴더를 확장하고 **UploadDocs.cmd** 를 선택합니다.
2. 배치 파일을 편집하여 **YOUR_SUBSCRIPTION_ID**, **YOUR_AZURE_STORAGE_ACCOUNT_NAME** 및 **YOUR_AZURE_STORAGE_KEY** 자리 표시자를 각각 해당하는 구독 ID, Azure Storage 계정 이름, 그리고 앞에서 만든 스토리지 계정의 Azure Storage 계정 키 값으로 바꿉니다.
3. 변경 내용을 저장한 후 **22-create-a-search-solution** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
4. 다음 명령을 입력하여 Azure CLI를 통해 Azure 구독에 로그인합니다.

    ```
    az login
    ```

웹 브라우저 탭이 열리고 Azure에 로그인하라는 메시지가 표시됩니다. Azure에 로그인한 다음 브라우저 탭을 닫고 Visual Studio Code로 돌아옵니다.

5. 다음 명령을 입력하여 배치 파일을 실행합니다. 그러면 스토리지 계정에 Blob 컨테이너가 만들어지며 해당 컨테이너에 **data** 폴더의 문서가 업로드됩니다.

    ```
    UploadDocs
    ```

## <a name="index-the-documents"></a>문서 인덱싱

이제 문서를 업로드했으므로 해당 문서를 인덱싱하여 검색 솔루션을 만들 수 있습니다.

1. Azure Portal에서 Azure Cognitive Search 리소스를 찾습니다. 그런 다음, **개요** 페이지에서 **데이터 가져오기** 를 선택합니다.
2. **데이터에 연결** 페이지의 **데이터 원본** 에서 **Azure Blob Storage** 를 선택합니다. 그런 다음, 다음 값을 사용하여 데이터 저장소 세부 정보를 완료합니다.
    - **데이터 원본**: Azure Blob Storage
    - **데이터 원본 이름**: margies-data
    - **추출할 데이터**: 콘텐츠 및 메타데이터
    - **구문 분석 모드**: 기본값
    - **연결 문자열**: 기존 연결 **선택** 을 선택합니다. 그런 다음 스토리지 계정을 선택하고 마지막으로 UploadDocs.cmp 스크립트를 실행하여 만든 **margies** 컨테이너를 선택합니다.
    - **관리 ID 인증**: 없음
    - **컨테이너 이름**: margies
    - **Blob 폴더**: *비워둡니다.*
    - **설명**: Margie's Travel 웹 사이트의 브로셔 및 리뷰
3. 다음 단계(인식 기술 추가)를 진행합니다.
4. **Cognitive Services 연결** 섹션에서 Cognitive Services 리소스를 선택합니다.
5. **보강 추가** 섹션에서 다음을 수행합니다.
    - **기술 세트 이름** 을 **margies-skillset** 로 변경합니다.
    - **OCR 활성화 및 모든 텍스트를 merged_content 필드로 병합** 옵션을 선택합니다.
    - **원본 데이터 필드** 가 **merged_content** 설정되었는지 확인합니다.
    - **보강 세분성 수준** 은 **원본 필드** 로 유지합니다. 원본 필드는 인덱싱하는 문서의 전체 콘텐츠로 설정되어 있습니다. 페이지, 문장 등의 더 세부적인 수준에서 문서를 추출하도록 이 수준을 변경할 수도 있습니다.
    - 다음 보강 필드를 선택합니다.

        | 인지 기술 | 매개 변수 | 필드 이름 |
        | --------------- | ---------- | ---------- |
        | 위치 이름 추출 | | 위치 |
        | 핵심 구 추출 | | keyphrases |
        | 언어 검색 | | 언어 |
        | 이미지에서 태그 생성 | | imageTags |
        | 이미지에서 캡션 생성 | | imageCaption |

6. 선택 항목을 다시 확인합니다(나중에 변경하기 어려울 수 있음). 그런 후에 다음 단계(*대상 인덱스 사용자 지정*)를 진행합니다.
7. **인덱스 이름** 을 **margies-index** 로 변경합니다.
8. **키** 가 **metadata_storage_path** 로 설정되었는지 확인하고 **제안기 이름** 을 공백으로 두고 **검색 모드** 가 기본적으로 채워졌는지 확인합니다.
9. 인덱스 필드에서 다음 정보를 변경하고 나머지 필드는 모두 기본 설정으로 유지합니다(**중요**: 전체 표를 표시하려면 오른쪽으로 스크롤해야 할 수도 있음).

    | 필드 이름 | 조회 가능 | 필터 가능 | 정렬 가능 | 패싯 가능 | 검색 가능 |
    | ---------- | ----------- | ---------- | -------- | --------- | ---------- |
    | metadata_storage_size | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_last_modified | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | |
    | metadata_storage_name | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | metadata_author | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | 위치 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | keyphrases | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; |
    | 언어 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&#10004; | | | |

11. 선택 항목을 다시 확인합니다. 특히 각 필드에서 올바른 **조회 가능**, **필터링 가능**, **정렬 가능**, **패싯 가능** 및 **검색 가능** 옵션을 선택했는지 자세히 확인합니다(나중에 이러한 옵션을 변경하기는 어려울 수 있음). 그런 후에 다음 단계(인덱서 만들기)를 진행합니다.
12. **인덱서 이름** 을 **margies-indexer** 로 변경합니다.
13. **일정** 을 **한 번** 으로 설정된 상태로 둡니다.
14. **고급** 옵션을 확장하고 **Base-64 인코딩 키** 옵션이 선택되어 있는지 확인합니다(일반적으로 인코딩 키를 사용하면 인덱스의 효율성이 향상됨).
15. **제출** 을 선택하여 데이터 원본, 기술 세트, 인덱스 및 인덱서를 만듭니다. 인덱서는 자동으로 실행되고 다음과 같은 인덱싱 파이프라인을 실행합니다.
    1. 데이터 원본에서 문서 메타데이터 필드 및 콘텐츠 추출
    2. 인식 기술의 기술 세트를 실행하여 추가 보강 필드 생성
    3. 추출된 필드를 인덱스에 매핑
16. Azure Cognitive Search 리소스의 **개요** 페이지 아래쪽 중간 부분에 있는 **인덱서** 탭을 확인하면 새로 만든 **margies-indexer** 가 표시되어 있습니다. 몇 분 정도 기다렸다가 **상태** 가 성공으로 표시될 때까지 **&orarr;새로 고침** 을 클릭합니다.

## <a name="search-the-index"></a>인덱스 검색

이제 인덱스가 생성되었으므로 해당 인덱스를 검색할 수 있습니다.

1. Azure Cognitive Search 리소스의 **개요** 페이지 위쪽에서 **검색 탐색기** 를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 `*`(별표 하나)를 입력한 다음 **검색** 을 선택합니다.

    이 쿼리는 인덱스에서 JSON 형식의 모든 문서를 검색합니다. 결과에서 각 문서의 필드를 살펴봅니다. 이러한 필드에는 문서 콘텐츠, 메타데이터, 그리고 선택한 인식 기술이 추출한 보강된 데이터가 포함되어 있습니다.

3. `search=*&$count=true`에 대한 쿼리 문자열을 수정하고 검색을 제출합니다.

    이번에는 결과 맨 위에 **@odata.count** 필드가 포함되어 있습니다. 이 필드는 검색에서 반환된 문서 수를 나타냅니다.

4. 다음 쿼리 문자열을 사용해 봅니다.

    ```
    search=*&$count=true&$select=metadata_storage_name,metadata_author,locations
    ```

    이번에는 결과에 파일 이름, 작성자 및 문서 콘텐츠에서 언급된 위치만 포함됩니다. 파일 이름과 작성자는 각각 원본 문서에서 추출된 **metadata_storage_name** 및 **metadata_author** 필드에 포함되어 있습니다. **locations** 필드는 인식 기술을 통해 생성된 것입니다.

5. 이번에는 다음 쿼리 문자열을 사용해 봅니다.

    ```
    search="New York"&$count=true&$select=metadata_storage_name,keyphrases
    ```

    이 검색에서는 검색 가능한 필드에 "New York"이 언급된 문서를 찾은 다음 해당 문서의 파일 이름과 핵심 구를 반환합니다.

6. 쿼리 문자열을 하나 더 사용해 봅니다.

    ```
    search="New York"&$count=true&$select=metadata_storage_name&$filter=metadata_author eq 'Reviewer'
    ```

    이 쿼리는 *Reviewer* 가 작성했으며 "New York"이 언급된 모든 문서의 파일 이름을 반환합니다.

## <a name="explore-and-modify-definitions-of-search-components"></a>검색 구성 요소의 정의 살펴보기 및 수정

검색 솔루션의 구성 요소는 JSON 정의를 기반으로 합니다. 이 정의는 Azure Portal에서 확인하고 편집할 수 있습니다.

Azure Portal을 사용하여 검색 솔루션을 만들고 수정할 수도 있지만, JSON에서 검색 개체를 정의한 다음 Azure Cognitive Service REST 인터페이스를 사용하여 검색 솔루션을 만들고 수정하는 방식이 더 효율적인 경우가 많습니다.

### <a name="get-the-endpoint-and-key-for-your-azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스용 엔드포인트 및 키 가져오기

1. Azure Portal에서 Azure Cognitive Search 리소스의 **개요** 페이지로 돌아온 후 페이지 맨 위 섹션에서 리소스의 **Url**( **https://resource_name.search.windows.net** 형식)을 찾아 클립보드에 복사합니다.
2. Visual Studio Code의 탐색기 창에서 **22-create-a-search-solution** 폴더와 해당 **modify-search** 하위 폴더를 확장한 다음 **modify-search.cmd** 를 선택하여 엽니다. 이 스크립트 파일을 사용하여 *cURL* 명령을 실행합니다. 이러한 cURL 명령이 Azure Cognitive Service REST 인터페이스에 JSON을 제출합니다.
3. **modify-search.cmd** 에서 **YOUR_SEARCH_URL** 자리 표시자를 클립보드에 복사한 URL로 바꿉니다.
4. Azure Portal에서 Azure Cognitive Search 리소스의 **키** 페이지를 표시한 다음 클립보드에 **기본 관리자 키** 를 복사합니다.
5. Visual Studio Code에서 **YOUR_ADMIN_KEY** 자리 표시자를 클립보드에 복사한 키로 바꿉니다.
6. **modify-search.cmd** 의 변경 내용을 저장합니다(파일을 아직 실행하지는 마세요).

### <a name="review-and-modify-the-skillset"></a>기술 세트 검토 및 수정

1. Visual Studio Code의 **modify-search** 폴더에서 **skillset.json** 을 엽니다. 그러면 **margies-skillset** 의 JSON 정의가 표시됩니다.
2. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 개체를 확인합니다. 이 개체는 Cognitive Services 리소스를 기술 세트에 연결하는 데 사용됩니다.
3. Azure Portal에서 Cognitive Services 리소스(Azure Cognitive Search 리소스 <u>아님</u>)를 열고 **키** 페이지를 표시합니다. 그런 다음 **키 1** 을 클립보드에 복사합니다.
4. Visual Studio Code의 **skillset.json** 에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 클립보드에 복사한 Cognitive Services 키로 바꿉니다.
5. JSON 파일을 스크롤하여 Azure Portal에서 Azure Cognitive Search 사용자 인터페이스를 통해 만든 기술의 정의가 포함되어 있음을 확인합니다. 기술 목록 맨 아래에 다음 정의가 적용된 기술이 하나 더 추가되었습니다.

    ```
    {
        "@odata.type": "#Microsoft.Skills.Text.V3.SentimentSkill",
        "defaultLanguageCode": "en",
        "name": "get-sentiment",
        "description": "New skill to evaluate sentiment",
        "context": "/document",
        "inputs": [
            {
                "name": "text",
                "source": "/document/merged_content"
            },
            {
                "name": "languageCode",
                "source": "/document/language"
            }
        ],
        "outputs": [
            {
                "name": "sentiment",
                "targetName": "sentimentLabel"
            }
        ]
    }
    ```

이름이 **get-sentiment** 인 새 기술은 문서의 각 **document** 수준에서 인덱싱 중인 문서의 **merged_content** 필드에서 확인된 텍스트(원본 콘텐츠와 콘텐츠의 이미지에서 추출된 텍스트 포함)를 평가합니다. 이 기술은 문서의 추출된 **language**(기본값: 영어)를 사용하며 문서 콘텐츠의 감정 레이블을 평가합니다. 감정 레이블의 값은 “positive”, “negative”, “neutral” 또는 “mixed”일 수 있습니다. 그러고 나면 이 레이블이 새 필드 **sentimentLabel** 에 출력됩니다.

6. **skillset.json** 의 변경 내용을 저장합니다.

### <a name="review-and-modify-the-index"></a>인덱스 검토 및 수정

1. Visual Studio Code의 **modify-search** 폴더에서 **index.json** 을 엽니다. 그러면 **margies-index** 의 JSON 정의가 표시됩니다.
2. 인덱스를 스크롤하여 필드 정의를 확인합니다. 원본 문서의 메타데이터 및 콘텐츠 기반 필드도 있고 기술 세트의 기술 결과 기반 필드도 있습니다.
3. Azure Portal에서 정의한 필드 목록 끝부분에 필드 2개가 더 추가되었습니다.

    ```
    {
        "name": "sentiment",
        "type": "Edm.String",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "sortable": true
    },
    {
        "name": "url",
        "type": "Edm.String",
        "facetable": false,
        "filterable": true,
        "retrievable": true,
        "searchable": false,
        "sortable": false
    }
    ```

4. **sentiment** 필드는 기술 세트에 추가된 **get-sentiment** 기술의 출력을 추가하는 데 사용됩니다. **url** 필드는 데이터 원본에서 추출된 **metadata_storage_path** 값을 기준으로 하여 인덱싱된 각 문서의 URL을 인덱스에 추가하는 데 사용됩니다. 인덱스에는 **metadata_storage_path** 필드가 이미 포함되어 있습니다. 인덱스 키로 사용되는 이 필드는 Base-64로 인코딩되어 있으므로 키로 사용하면 효율적입니다. 하지만 실제 URL 값을 필드로 사용하려는 클라이언트 애플리케이션은 이 필드를 디코딩해야 합니다. 인코딩되지 않은 값용으로 두 번째 필드를 추가하면 이 문제가 해결됩니다.

### <a name="review-and-modify-the-indexer"></a>인덱서 검토 및 수정

1. Visual Studio Code의 **modify-search** 폴더에서 **indexer.json** 을 엽니다. 그러면 **margies-indexer** 의 JSON 정의가 표시됩니다. 이 정의는 문서 콘텐츠와 메타데이터에서 추출된 필드(**fieldMappings** 섹션), 그리고 기술 세트의 기술이 추출한 값(**outputFieldMappings** 섹션)을 인덱스의 해당 필드에 각각 매핑합니다.
3. **fieldMappings** 목록에서 **metadata_storage_path** 값이 Base-64 인코딩 키 필드로 매핑되는 방식을 확인합니다. 이 매핑은 **metadata_storage_path** 를 키로 할당하고 Azure Portal에서 키를 인코딩하는 옵션을 선택할 때 생성된 것입니다. 그리고 새 매핑이 Base-64로 인코딩되지 않은 동일 값을 **url** 필드에 명시적으로 매핑합니다.

    ```
    {
        "sourceFieldName" : "metadata_storage_path",
        "targetFieldName" : "url"
    }
    
    ```

원본 문서의 기타 모든 메타데이터 및 콘텐츠 필드는 인덱스에서 이름이 같은 필드에 암시적으로 매핑됩니다.

4. 기술 세트에 포함된 기술의 출력이 인덱스 필드에 매핑되는 **ouputFieldMappings** 섹션을 검토합니다. 대다수 매핑에는 사용자 인터페이스에서 선택한 옵션이 반영됩니다. 단, 감정 기술이 추출한 **sentimentLabel** 값을 인덱스에 추가한 **sentiment** 필드에 매핑하는 다음 매핑이 추가되었습니다.

    ```
    {
        "sourceFieldName": "/document/sentimentLabel",
        "targetFieldName": "sentiment"
    }
    ```

### <a name="use-the-rest-api-to-update-the-search-solution"></a>REST API를 사용하여 검색 솔루션 업데이트

1. **modify-search** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
2. **modify-search** 폴더의 터미널 창에서 다음 명령을 입력하여 **modify-search.cmd** 스크립트를 실행합니다. 이 스크립트는 REST 인터페이스에 JSON 정의를 제출하고 인덱싱을 시작합니다.

    ```
    modify-search
    ```

3. 스크립트 실행이 완료되면 Azure Portal의 Azure Cognitive Search 리소스 **개요** 페이지로 돌아와 **인덱서** 페이지를 표시합니다. 그런 다음 주기적으로 **새로 고침** 을 선택하여 인덱싱 작업 진행률을 추적합니다. 인덱싱이 완료되려면 1분 정도 걸릴 수 있습니다.

    너무 커서 감정을 평가할 수 없는 일부 문서의 경우 경고가 표시될 수 있습니다. 감정 분석은 전체 문서가 아닌 페이지 또는 문장 수준에서 수행되는 경우가 많습니다. 하지만 이 사례에 해당하는 시나리오에서는 대다수 문서(특히 호텔 리뷰)가 짧으므로 유용한 문서 수준 감정 점수를 평가할 수 있습니다.

### <a name="query-the-modified-index"></a>수정된 인덱스 쿼리

1. Azure Cognitive Search 리소스 블레이드 위쪽에서 **검색 탐색기** 를 선택합니다.
2. 검색 탐색기의 **쿼리 문자열** 상자에 다음 쿼리 문자열을 입력한 다음 **검색** 을 선택합니다.

    ```
    search=London&$select=url,sentiment,keyphrases&$filter=metadata_author eq 'Reviewer' and sentiment eq 'positive'
    ```

    이 쿼리는 *London* 이 언급되어 있고, 작성자가 *Reviewer* 이며, **sentiment** 레이블이 양수인 모든 문서(즉, 런던을 언급한 긍정적인 내용의 리뷰)의 **url**, **sentiment** 및 **keyphrases** 를 검색합니다.

3. **검색 탐색기** 페이지를 닫고 **개요** 페이지로 돌아옵니다.

## <a name="create-a-search-client-application"></a>검색 클라이언트 애플리케이션 만들기

이제 유용한 인덱스가 생성되었으므로 클라이언트 애플리케이션에서 해당 인덱스를 사용할 수 있습니다. 그러려면 REST 인터페이스를 사용하여 요청을 제출한 다음 HTTP를 통해 JSON 형식 응답을 수신합니다. 원하는 프로그래밍 언어용 SDK(소프트웨어 개발 키트)를 사용해도 됩니다. 이 연습에서는 SDK를 사용합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

### <a name="get-the-endpoint-and-keys-for-your-search-resource"></a>검색 리소스용 엔드포인트 및 키 가져오기

1. Azure Portal의 Azure Cognitive Search 리소스 **개요** 페이지에서 **https://*your_resource_name*.search.windows.net** 형식의 **Url** 값을 확인합니다. 이 값이 검색 리소스용 엔드포인트입니다.
2. **키** 페이지에는 **관리자** 키 2개와 **쿼리** 키 1개가 표시됩니다. *관리자* 키는 검색 리소스를 만들고 관리하는 데 사용되며 *쿼리* 키는 검색 쿼리만 수행하면 되는 클라이언트 애플리케이션에 사용됩니다.

    여기서는 클라이언트 애플리케이션 엔드포인트 및 쿼리 키가 필요합니다.

### <a name="prepare-to-use-the-azure-cognitive-search-sdk"></a>Azure Cognitive Search SDK 사용 준비

1. Visual Studio Code의 **탐색기** 창에서 **22-create-a-search-solution** 폴더로 이동한 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **margies-travel** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Azure Cognitive Search SDK 패키지를 설치합니다.

    **C#**
    
    ```
    dotnet add package Azure.Search.Documents --version 11.1.1
    ```
    
    **Python**
    
    ```
    pip install azure-search-documents==11.0.0
    ```
    
3. **margies-travel** 폴더의 내용을 보고 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Azure Cognitive Search 리소스용 **엔드포인트** 및 **쿼리 키** 를 반영하도록 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.

### <a name="explore-code-to-search-an-index"></a>인덱스를 검색하는 코드 살펴보기

**margies-travel** 폴더에는 검색 기능이 포함된 웹 애플리케이션(Microsoft C# *ASP.NET Razor* 웹 애플리케이션 또는 Python *Flask* 애플리케이션)용 코드 파일이 들어 있습니다.

1. 선택한 프로그래밍 언어에 따라 웹 애플리케이션에서 다음 코드 파일을 엽니다.
    - **C#** :Pages/Index.cshtml.cs
    - **Python**: app.py
2. 코드 파일 맨 위에서 **검색 네임스페이스 가져오기** 주석을 찾은 다음 Azure Cognitive Search SDK 사용을 위해 가져온 네임스페이스를 확인합니다.
3. **search_query** 함수에서 **검색 클라이언트 만들기** 주석을 찾은 다음 코드가 Azure Cognitive Search 리소스용 엔드포인트와 쿼리 키를 사용하여 **SearchClient** 개체를 만든다는 것을 확인합니다.
4. **search_query** 함수에서 **검색 쿼리 제출** 주석을 찾은 다음 코드가 다음 옵션을 사용하여 지정된 텍스트의 검색을 제출하는 방식을 검토합니다.
    - 검색 텍스트의 개별 단어를 **모두** 찾아야 하는 *검색 모드*
    - 검색에서 확인되는 총 문서 수를 결과에 포함
    - 제공된 필터 식과 일치하는 문서만 포함되도록 결과 필터링
    - 지정한 정렬 순서로 결과 정렬
    - **metadata_author** 필드의 각 고유 값을 *패싯* 으로 반환. 이 패싯을 사용하여 필터링을 위해 미리 정의된 값을 표시할 수 있습니다.
    - **merged_content** 및 **imageCaption** 필드에서 추출하여 검색 용어를 강조 표시한 항목을 최대 3개까지 결과에 포함
    - 지정된 필드만 결과에 포함

### <a name="explore-code-to-render-search-results"></a>검색 결과를 렌더링하는 코드 살펴보기

웹앱에는 검색 결과를 처리 및 렌더링하는 코드가 이미 포함되어 있습니다.

1. 선택한 프로그래밍 언어에 따라 웹 애플리케이션에서 다음 코드 파일을 엽니다.
    - **C#** :Pages/Index.cshtml
    - **Python**: templates/search.html
2. 검색 결과가 표시되는 페이지를 렌더링하는 코드를 살펴봅니다. 다음과 같은 부분을 확인하세요.
    - 페이지의 시작 부분에는 검색 양식이 표시됩니다. 사용자는 이 검색 양식을 통해 새 검색을 제출할 수 있습니다(Python 버전 애플리케이션에서는 **base.html** 템플릿에 이 양식이 정의되어 있음). 그러면 페이지 시작 부분에서 새로 제출된 검색을 참조합니다.
    - 그러고 나면 두 번째 양식이 렌더링되므로 사용자가 검색 결과를 상세 검색할 수 있습니다. 이 양식의 코드는 다음 작업을 수행합니다.
        - 검색 결과에서 문서 개수를 검색하여 표시합니다.
        - **metadata_author** 필드의 패싯 값을 검색하여 필터링용 옵션 목록으로 표시합니다.
        - 결과에 사용 가능한 정렬 옵션의 드롭다운 목록을 만듭니다.
    - 그런 다음 코드는 검색 결과에서 반복 실행되어 각 결과를 다음과 같이 렌더링합니다.
        - **metadata_storage_name**(파일 이름) 필드를 **url** 필드의 주소로 이동할 수 있는 링크로 표시합니다.
        - **merged_content** 및 **imageCaption** 필드에서 확인된 검색 용어를 *강조 표시* 하면 관련 컨텍스에서 검색 용어를 표시할 수 있습니다.
        - **metadata_author**, **metadata_storage_size**, **metadata_storage_last_modified** 및 **language** 필드를 표시합니다.
        - 문서의 **감정** 레이블을 표시합니다. 양수, 음수, 중립 또는 혼합일 수 있습니다.
        - 처음 5개 **keyphrases**(있는 경우)를 표시합니다.
        - 처음 5개 **locations**(있는 경우)를 표시합니다.
        - 처음 5개 **imageTags**(있는 경우)를 표시합니다.

### <a name="run-the-web-app"></a>웹앱 실행

 1. **margies-travel** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    flask run
    ```

2. 앱이 정상적으로 시작되면 표시되는 메시지에서 실행 중인 웹 애플리케이션의 링크( *http://localhost:5000/* 또는 *http://127.0.0.1:5000/* )를 따라 이동하여 웹 브라우저에서 Margie's Travel 사이트를 엽니다.
3. Margie's Travel 웹 사이트에서 검색 상자에 **London hotel** 을 입력하고 **검색** 을 클릭합니다.
4. 검색 결과를 검토합니다. 검색 결과에는 파일 이름(파일 URL의 하이퍼링크 포함), 검색 용어(*London* 및 *hotel*)가 강조 표시된 파일 콘텐츠 추출 내용, 그리고 인덱스 필드에서 가져온 파일의 기타 특성이 포함되어 있습니다.
5. 결과 페이지에는 결과를 상세 검색하는 데 사용할 수 있는 몇 가지 사용자 인터페이스 요소가 포함되어 있습니다. 여기에는 다음이 포함됩니다.
    - **metadata_author** 필드의 패싯 값 기반 *필터*. 즉, 패싯 가능 필드를 사용하여 패싯 목록을 반환할 수 있습니다. 패싯이란 사용자 인터페이스에서 필터 가능 값으로 표시할 수 있는 고유 값 몇 개가 포함된 필드입니다. 
    - 지정한 필드 및 정렬 방향(오름차순 또는 내림차순)을 기준으로 결과 *순서* 를 지정하는 기능. 기본 순서는 *관련성* 을 기준으로 설정됩니다. 관련성은 인덱스 필드의 검색 용어 빈도와 중요도를 평가하는 *점수 매기기 프로필* 을 기반으로 하여 **search.score()** 값으로 계산됩니다.
6. **검토자** 필터와 **긍정적인 내용부터** 정렬 옵션을 선택하고 **결과 구체화** 를 선택합니다.
7. 결과가 리뷰만 포함하도록 필터링되고 감정 레이블에 따라 정렬되는지 확인합니다.
8. **검색** 상자에 **quiet hotel in New York** 을 새 검색으로 입력하고 결과를 검토합니다.
9. 다음 검색 용어를 사용해 봅니다.
    - **Tower of London**(일부 문서에서 이 용어는 *핵심 구* 로 식별됨)
    - **skyscraper**(이 단어는 어떤 문서의 실제 콘텐츠에도 표시되지는 않지만 일부 문서의 이미지용으로 생성된 *이미지 캡션* 및 *이미지 태그* 에서 확인됨)
    - **Mojave desert**(일부 문서에서 이 용어는 *위치* 로 식별됨)
10. Margie’s Travel 웹 사이트가 포함된 브라우저 탭을 닫고 Visual Studio Code로 돌아갑니다. 그런 다음 **margies-travel** 폴더의 Python 터미널(dotnet 또는 flast 애플리케이션이 실행되고 있는 터미널)에서 Ctrl+C를 입력하여 앱을 중지합니다.

## <a name="more-information"></a>추가 정보

Azure Cognitive Search에 대해 자세히 알아보려면 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/search-what-is-azure-search)를 참조하세요.
