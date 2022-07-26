---
lab:
  title: 양식에서 데이터 추출
  module: Module 11 - Reading Text in Images and Documents
ms.openlocfilehash: 96d47db5b1db0b4d87b5c7acd267493487584c41
ms.sourcegitcommit: 94c8705b76b644d0fb0cd6df1b78ff63d5869109
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2022
ms.locfileid: "147406489"
---
# <a name="extract-data-from-forms"></a>양식에서 데이터 추출 

현재 기업이 직원에게 주문 시트를 수동으로 구매하고 데이터베이스에 데이터를 입력할 것을 요구한다고 가정합니다. AI 서비스를 활용하여 데이터 항목 프로세스를 개선하려고 합니다. 데이터베이스를 자동으로 업데이트하는 데 사용할 수 있는 구조적 데이터를 생성하고 양식을 읽는 기계 학습 모델을 빌드하기로 합니다.

사용자가 자동화된 데이터 처리 소프트웨어를 빌드하는 데 사용할 수 있는 Cognitive Service는 **Form Recognizer** 입니다. 이 소프트웨어는 OCR(광학 인식) 기술을 사용해 양식 문서에서 텍스트, 키/값 쌍 및 테이블을 추출할 수 있습니다. Form Recognizer에는 송장, 영수증 및 명함 인식용으로 미리 빌드된 모델이 포함되어 있습니다. 이 서비스에서는 사용자 지정 모델을 학습시키는 기능도 제공합니다. 이 연습에서는 사용자 지정 모델 빌드 과정을 중점적으로 진행합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 과정용 코드 리포지토리를 아직 복제하지 않았으면 복제해야 합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

Form Recognizer 서비스를 사용하려면 해당 Azure 구독의 Form Recognizer 또는 Cognitive Services 리소스가 필요합니다. 여기서는 Azure Portal을 사용하여 리소스를 만듭니다.

1.  `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.

2. **&#65291;리소스 만들기** 단추를 선택하고 *Form Recognizer* 를 검색한 후에 다음 설정을 사용하여 **Form Recognizer** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: F0

    > **참고**: 구독에 F0 Form Recognizer 서비스가 이미 포함되어 있으면 여기서는 **S0** 을 선택합니다.

3. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 나중에 코드에서 액세스를 관리하려면 이 페이지에 표시되는 **키** 중 하나와 **엔드포인트** 가 필요합니다. 

## <a name="gather-documents-for-training"></a>학습용 문서 수집

![송장 이미지](../21-custom-form/sample-forms/Form_1.jpg)  

이 리포지토리의 **21-custom-form/sample-forms** 폴더에서 샘플 양식을 사용하고, 여기에는 모델을 학습하고 테스트하는 데 필요한 모든 파일이 포함되어 있습니다.

1. Visual Studio Code의 **21-custom-form** 폴더에서 **sample-forms** 폴더를 확장합니다. 이 폴더에는 **.json** 및 **.jpg** 로 끝나는 파일이 있습니다.

    **.jpg** 파일을 사용하여 해당 모델을 학습합니다.  

    **.json** 파일은 자동으로 생성되었고 레이블 정보가 포함됩니다. 파일은 양식과 함께 해당 Blob Storage 컨테이너에 업로드됩니다. 

2. Azure Portal([https://portal.azure.com](https://portal.azure.com))로 돌아갑니다.

3. 이전에 Form Recognizer 리소스를 만든 **리소스 그룹** 을 표시합니다.

4. 리소스 그룹의 **개요** 페이지에서 **구독 ID** 및 **위치** 를 확인합니다. 후속 단계에서 이러한 값과 **리소스 그룹** 이름이 필요합니다.

![리소스 그룹 페이지 예제](./images/resource_group_variables.png)

5. Visual Studio Code의 탐색기 창에서 **21-custom-form** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기** 를 선택합니다.

6. 터미널 창에서 다음 명령을 입력하여 Azure 구독에 대해 인증된 연결을 설정합니다.
    
```
az login --output none
```

7. 메시지가 표시되면 Azure 구독에 로그인합니다. 그런 다음 Visual Studio Code로 돌아가서 로그인 프로세스가 완료될 때까지 기다립니다.

8. 다음 명령을 실행하여 Azure 위치를 나열합니다.

```
az account list-locations -o table
```

9. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus* 임).

    > **중요**: **Name** 값을 적어 두었다가 12단계에서 사용하세요.

10. 탐색기 창의 **21-custom-form** 폴더에서 **setup.cmd** 를 선택합니다. 이 배치 스크립트를 사용하여 필요한 기타 Azure 리소스를 만드는 데 필요한 Azure CLI(명령줄 인터페이스)를 실행합니다.

11. **setup.cmd** 스크립트에서 **rem** 명령을 검토합니다. 이러한 명령에는 스크립트를 실행한 프로그램이 요약되어 있습니다. 프로그램은 다음 작업을 수행합니다. 
    - Azure 리소스 그룹에서 스토리지 계정 만들기
    - 로컬 _sampleforms_ 폴더에서 스토리지 계정의 _sampleforms_ 컨테이너로 파일 업로드
    - 공유 액세스 서명 URI 인쇄

12. Form Recognizer 리소스를 배포한 구독, 리소스 그룹 및 위치 이름에 해당하는 값을 사용하여 **subscription_id**, **resource_group** 및 **location** 변수 선언을 수정합니다. 그런 다음 변경 내용을 **저장** 합니다.

    **expiry_date** 변수의 경우 이 연습에서는 그대로 유지합니다. 이 변수는 SAS(공유 액세스 서명) URI를 생성할 때 사용됩니다. 실제로는 SAS에 적절한 만료 날짜를 설정해야 합니다. [여기](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)서 SAS에 대해 자세히 알아볼 수 있습니다.  

13. **21-custom-form** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

```
setup
```

14. 스크립트 실행이 완료되면 표시되는 출력을 검토하여 Azure 리소스의 SAS URI를 확인합니다.

> **중요**: 다음 작업을 계속 진행하기 전에 나중에 다시 검색할 수 있는 위치(예: Visual Studio Code의 새 텍스트 파일)에 SAS URI를 붙여넣습니다.

15. Azure Portal에서 리소스 그룹을 새로 고쳐 방금 만든 Azure Storage 계정이 포함되어 있는지 확인합니다. 스토리지 계정을 열고 왼쪽 창에서 **스토리지 브라우저** 를 선택합니다. 그런 다음 스토리지 브라우저에서 **Blob 컨테이너** 를 확장하고 **sampleforms** 컨테이너를 선택하여 로컬 **21-custom-form/sample-forms** 폴더에서 파일이 업로드되었는지 확인합니다.

## <a name="train-a-model-using-the-form-recognizer-sdk"></a>Form Recognizer SDK를 사용하여 모델 학습

이제 **.jpg** 및 **.json** 파일을 사용하여 모델을 학습시킵니다.

1. Visual Studio Code의 **21-custom-form/sample-forms** 폴더에서 **fields.json** 을 열고 이 파일에 포함된 JSON 문서를 검토합니다. 이 파일은 양식에서 추출하도록 모델을 학습시킬 필드를 정의합니다.
2. **Form_1.jpg.labels.json** 을 열고 이 파일에 포함된 JSON을 검토합니다. 이 파일은 **Form_1.jpg** 학습 문서에서 이름이 지정된 필드의 위치와 값을 식별합니다.
3. **Form_1.jpg.ocr.json** 을 열고 이 파일에 포함된 JSON을 검토합니다. 이 파일에는 양식에 있는 모든 텍스트 영역의 위치를 비롯한 **Form_1.jpg** 텍스트 레이아웃의 JSON 표현이 포함되어 있습니다.

    *이 연습에서는 필드 정보 파일이 제공되었습니다. 자체 프로젝트에서는 [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)를 사용하여 이러한 파일을 만들 수 있습니다. 도구를 사용할 때 필드 정보 파일이 자동으로 만들어지고 연결된 스토리지 계정에 저장됩니다.*

4. Visual Studio Code의 **21-custom-form** 폴더에서 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
5. **train-model** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.

6. 언어 기본 설정에 적합한 명령을 실행하여 Form Recognizer 패키지를 설치합니다.

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

7. **train-model** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

8. 구성 파일을 편집하여 다음 항목이 반영되도록 설정을 수정합니다.
    - Form Recognizer 리소스의 **엔드포인트**
    - Form Recognizer 리소스의 **키**
    - Blob 컨테이너의 **SAS URI**

9. **train-model** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: train-model.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **클라이언트** 를 만듭니다.
    - 이 코드는 학습 클라이언트를 사용해 해당 Blob Storage 컨테이너의 이미지로 모델을 학습하고, 생성된 SAS URI를 사용하여 액세스할 수 있습니다.

10. **train-model** 폴더에서 학습 애플리케이션용 코드 파일을 엽니다.

    - **C#** : Program.cs
    - **Python**: train-model.py

11. **train-model** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python train-model.py
```

12. 프로그램이 종료될 때까지 기다렸다가 모델 출력을 검토합니다.
13. 터미널 출력에서 모델 ID를 작성합니다. 이는 랩의 다음 부분에 필요합니다. 

## <a name="test-your-custom-form-recognizer-model"></a>사용자 지정 Form Recognizer 모델 테스트 

1. **21-custom-form** 폴더 내의 선호하는 언어(**C-Sharp** 또는 **Python**) 하위 폴더에서 **test-model** 폴더를 확장합니다.

2. **test-model** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널을 엽니다**.

3. **test-model** 폴더의 터미널에서 언어 기본 설정에 적합한 명령을 실행하여 Form Recognizer 패키지를 설치합니다.

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

이전에 pip를 사용하여 Python 환경에 패키지를 설치했다면 이 작업을 반드시 수행할 필요는 없습니다. 하지만 이 단계를 수행하면 패키지가 설치되어 있는지를 확인할 수 있습니다.

4. **test-model** 폴더의 동일한 터미널에서 테이블 형식 라이브러리를 설치합니다. 그러면 출력이 테이블로 제공됩니다.

**C#**

```
dotnet add package Tabulate.NET --version 1.0.5
```

**Python**

```
pip install tabulate
```

5. **test-model** 폴더에서 구성 파일(언어 기본 설정에 따라 **appsettings.json** 또는 **.env**)을 편집하여 다음 값을 추가합니다.
    - Form Recognizer 엔드포인트
    - Form Recognizer 키
    - 모델을 학습시킬 때 생성된 모델 ID(**train-model** 폴더의 **cmd** 콘솔로 터미널을 다시 전환하면 이 ID를 확인할 수 있음) 변경 내용을 **저장** 합니다.

6. **test-model** 폴더에서 클라이언트 애플리케이션의 코드 파일(C#의 경우 *Program.cs*, Python의 경우 *test-model.py*)을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 패키지의 네임스페이스를 가져왔습니다.
    - **Main** 함수가 구성 설정을 검색하며 키와 엔드포인트를 사용하여 인증된 **클라이언트** 를 만듭니다.
    - 클라이언트를 사용하여 **test1.jpg** 이미지의 값과 양식 필드를 추출합니다.
    

7. **test-model** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

**C#**

```
dotnet run
```

**Python**

```
python test-model.py
```
    
8. 출력을 보고 모델의 출력이 "CompanyPhoneNumber" 및 "DatedAs"와 같은 필드 이름을 제공하는 방법에 대해 살펴봅니다.   

## <a name="more-information"></a>추가 정보

Form Recognizer 서비스를 사용하는 방법에 대한 자세한 내용은 [Form Recognizer 설명서](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)를 참조하세요.
