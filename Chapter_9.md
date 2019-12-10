# Chapter 9 Computing with App Engine

**이 챕터는 구글 Associate Cloud Engineer 인증 시험 과목 중, 아래 내용을 다룬다.**
* 3.3 App Engine과 Cloud Functions 리소스 배포하고 구현

이 챕터는 App Engine Standard 어플리케이션을 배포하는 방법을 설명한다. App Engine 어플리케이션의 구조를 확인 하는 것으로 시작하고, 어플리케이션 설정을 지정하는 방법에 대해서 설명한다. 그 다음, 스케일링과 트래픽 분산을 통해서 App Engine 어플리케이션을 튜닝에 관심을 기울일 것이다. 또한, App Engine 어플리케이션 버전에 대해서도 논의한다.

구글 App Engine은 근본적으로 언어에 특화된 환경에서 어플리케이션을 실행하도록 설계되었다. App Engine이 소개된 이후, 구글은 App Engine Flexible을 소개했다. 이는 컨테이너에 커스텀 런타임을 배포하는데 사용될 수 있다. 이 챕터는 App Engine Standard로 알려진 근본적인 App Engine 환경에 어플리케이션을 배포하는 방법을 설명한다.

## App Engine 컴포넌트

App Engine Standard 어플리케이션은 4가지 컴포넌트로 구성된다.
* Application
* Service
* Version
* Instance

App Engine 어플리케이션은 project에서 생성되는 고수준의 리소스이다. 즉, 각 프로젝트는 하나의 App Engine 어플리케이션을 갖을 수 있다. App Engine과 연관된 모든 리소스는 어플리케이션이 생성될 때 지정되는 region에서 생성된다.

어플리케이션은 최소 하나의 service를 갖는다. 이는 App Engine 환경에서 실행되는 코드이다. 어플리케이션 코드의 다양한 버전이 존재할 수 있기 때문에, App Engine은 어플리케이션의 버전관리를 지원한다. service는 다양한 버전을 갖을 수 있고, 새로운 기능, 버그 수정, 이전 버전에 상대적인 변화를 통합하는 새로운 버전은 일반적으로 약간 다르다. 버전이 실행될 때, 어플리케이션의 인스턴스를 생성한다.

Service는 전형적으로 *microservices*로 알려진 다수의 서비스로 구성된 복합한 어플리케이션으로 하나의 기능을 수행하도록 구성된다. 하나의 microservice는 데이터 접근을 위한 API 요청을 처리한다. 반면에, 또 다른 microservice는 비용 청구를 목적으로 인증과 3번째 레코드 데이터를 수행한다.

![9.1](./img/ch09/9.1.png)

**그림 9.1** App Engine 어플리케이션의 컴포넌트 계층

Service는 소스 코드와 설정 파일에 의해서 정의된다. 파일의 조합은 어플리케이션의 버전을 구성한다. 소스코드나 설정 파일이 약간 변경되었다면, 또다른 버전을 생성한다. 이 방법으로 한 번에 어플리케이션의 다양한 버전이 유지될 수 있다. 모든 사용자에게 변경을 배포하기 전에 적은 수의 사용자에게 새로운 기능을 테스트하는데 특히 유용하다. 버그나 다른 문제가 해당 버전에서 발생한다면, 이전 버전으로 쉽게 롤백할 수 있다. 다양한 버전을 유지하는 다른 장점은 트래픽을 이관하고 분리할 수 있다. 이후 챕터에서 더 자세한 정보를 설명할 것이다.

## App Engine 어플리케이션 배포

구글 Associate Cloud Engineer 인증 시험은 어플리케이션을 작성하는 엔지니어를 요구하지 않는다. 하지만, 어플리케이션을 배포하는 방법을 알아야 한다. 이 섹션에서는 구글에서 Hello World 예시를 다운로드하고, 배포할 샘플 어플리케이션으로 사용한다. 어플리케이션은 Python으로 작성 되어서 App Engine에서 Python 런타임을 사용할 것이다.

### Cloud Shell과 SDK를 사용하여 어플리케이션 배포

처음, Cloud Shell을 사용하여 터미널에서 작업할 것이다. Cloud Console에서 Cloud Shell 아이콘을 클릭하여 시작한다. `gcloud`가 App Engine과 작동하도록 구성되어있는지 확인한다.

```bash
gcloud components install app-engine-python
```

이 것은 필요한 App Engine Python 라이브러리를 설치하거나 업데이트할 것이다. 라이브러리가 최신이면, 상태를 알리는 메시지를 수진할 것이다.

Cloud Shell을 열 면, `python-docs-samples` 이름의 디렉토리를 갖을 수 있다. 이 디렉토리는 이 챕터에서 사용할 Hello World 어플리케이션을 포함한 예제 어플리케이션을 포함한다. 디렉토리가 보이지 않는다면, 아래를 참고하여 구글에서 Hello World 어플리케이션을 다운로드 할 수 있다.

```bash
git clone [https://github.com/GoogleCloudPlatform/python-docs-samples](https://github.com/GoogleCloudPlatform/python-docs-samples)
```

다음, 워킹디렉토리를 Hello World 어플리케이션 디렉토리로 변경한다.

```bash
cd python-docs-samples/appengine/standard/hello_world
```

디렉토리의 파일을 조회하면 3개의 파일이 확인될 것이다.
* app.yaml
* main.py
* main_test.py

여기에서는 주로 `app.yaml` 파일을 다룬다. 아래 명령을 사용하여 파일의 컨텐츠를 조회한다.

```bash
cat app.yaml
```

그림 9.2에서 보여지는 것 처럼 설정의 상세정보를 보여준다.

![9.2](./img/ch09/9.2.png)

**그림 9.2** Python 어플리케이션을 위한 `app.yaml`파일의 컨텐츠

어플리케이션 설정 파일은 사용할 Python의 버전, 배포할 API 버전, `true`로 설정된 Python 파라미터인 `threadsafe`를 지정한다. 마지막 3 라인은 실행할 스크립트를 지정한다. 여기서는 `main.py`를 지정했다.

어플리케이션을 배포하기 위해, 다음 명령을 사용할 수 있다.

```bash
gcloud app deploy app.yaml
```

그러나, `app.yaml`은 기본 값이다. 그래서 파일 이륾을 사용하는 경우, `deploy` 명령에서 `app.yaml`을 지정할 필요가 없다.

이 명령은 `app.yaml` 파일이 있는 디렉토리에서 반드시 실행되어야 한다. `gcloud app deploy` 명령은 몇 가지 optional 파라미터를 갖는다.
* `--version`, 커스텀 버전 ID를 지정
* `--project`, 어플리케이션에서 사용할 projectID를 지정
* `--no-promote`, 트래픽 라우팅없이 어플리케이션을 배포

`gcloud app deploy` 명령을 실행하면, 그림 9.3같은 output을 확인할 수 있다.

![9.3](./img/ch09/9.3.png)

**그림 9.3** `gcloud app deploy` 명령의 output

