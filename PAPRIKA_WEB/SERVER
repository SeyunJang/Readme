# Paprika Web Server

> Send Anywhere 웹 프로젝트

## 시작하기

### 환경설정

#### Ubuntu


-   NodeJS >= node 10

```
    $ sudo apt install nodejs
```

-   AWS Elastic Beanstalk CLI

```
    $ sudo apt install python python-pip
    $ pip install awsebcli --upgrade --user
```

-   git-flow

```
    $ apt install git-flow
```

### 설치

```
    $ git clone git@bitbucket.org:estmob/paprika_web.git
    $ cd paprika_web
    $ git checkout master

    $ git flow init
    # default all

    $ eb init
    # 3) us-west-2 : US West (Oregon)
    # 3) web
    # 2) web-staging
    # n

    $ npm install
```

## Development

### 브랜치 모델

-   [git-flow](https://github.com/nvie/gitflow)

### devServer 실행

```
    # 웹 서버 시작
    $ npm run start

    # CRA 개발 서버 시작
    $ npm run dev
```

### Development Script

Run multiple development commands in separate terminals.

- run `npm run develop`

The `develop` script runs commands below

```bash
# Establish a secure connection to a remote host while enabling local port forwarding to a Redis server
npm run sshTunneling

# run a local web server
npm run watch:local

# run a local frontend app
npm run dev
```


### 빌드

```
    $ npm run build:dev
```

### 테스트 실행

```
    # 웹 서버 lint
    $ npm run lint

    # 웹 서버 유닛 테스트
    $ npm run test:server-unit
```

## Deployment

- Updated [instructions](https://confluence.rakuten-it.com/confluence/display/SA/FE+Deployment+Instructions)
- Legacy [instructions](./docs/legacy.md)


## 참고사항

-   테스트 서버는 beanstalk가 아닌 사내 장비에 배포

## 구성 및 주의사항

### 메인 웹앱

-   `src/`에 위치
-   CRA 1.0으로 생성되었으며 `eject`후 커스터마이징
-   엔트리 포인트 `src/index.js`, 템플릿 `public/template.pug`
-   `public/template.html`은 devServer용 템플릿 파일로 실제 서비스에는 사용되지 않습니다
-   route 추가 후에는 웹 서버에 정확한 로그를 남기기 위해 `server/routes/page.js` page 함수 내 paths에 경로를 추가해 주는 것을 권장합니다 (CSR 앱이므로 추가하지 않아도 서비스에 문제는 없음)

### 모바일 다운로드 랜딩

-   `server/views/share-landing*.pug` 와 숫자키 랜딩인 `server/views/link-app.pug`
-   모바일 다운로드 랜딩 경량화를 위해 메인 웹앱이 아닌 서버 템플릿 엔진을 이용합니다
-   별도 polyfill이 없으므로 브라우저가 지원하는 기능을 잘 알아보고 작업합니다

### 크롬 확장프로그램 v2 내 웹앱

-   `src/extension`에 위치
-   앤트리 포인트 `src/extension.js`, 템플릿 `public/extension-template.pug`
-   웹앱 URL은 chrome.send-anywhere.com/v2 지만 직접 접근은 불가함
-   자세한 내용은 paprika_chrome_extension[https://bitbucket.org/estmob/paprika_chrome_extension/src/master] 프로젝트 참고

### 크롬 확장프로그램 v1 (웨일 확장 프로그램) 내 웹앱

-   `server/views/webapp`에 위치
-   google polymer project[https://www.polymer-project.org] 로 구현
-   자세한 내용은 paprika_chrome_extension[https://bitbucket.org/estmob/paprika_chrome_extension/src/v1] 프로젝트 참고

### MS office 365 outlook addon

-   스토어[https://appsource.microsoft.com/en-us/product/office/WA104380391]
-   `server/views/webapp`에 위치
-   대시보드[https://partner.microsoft.com/ko-kr/dashboard/office/products/8abbf816-9184-4852-8483-0bfe38b24eba/overview] 에 `config/outlook_addin_manifest.xml` 파일 업로드
-   개발버전 테스트 하는 방법
    -   테스트할 브라우저 캐시 삭제 후 아래 링크 참조
    -   [Sideload Office Add-ins in Office on the web for testing](https://docs.microsoft.com/en-us/office/dev/add-ins/testing/sideload-office-add-ins-for-testing)

### 웹 위젯 (워드프레스 플러그인)

-   `public/widgets`와 `server/views/widgets`에 위치
-   현재 위젯 생성기는 제공되지 않으나 이미 사용 중인 코드는 막지 않음
-   동일한 코드를 워드프레스 플러그인으로 래핑한 버전도 있음. 자세한 내용은 paprika_wordpress[https://bitbucket.org/estmob/paprika_wordpress/src/master/] 프로젝트 참고

### 투데이 인앱

-   `src/socialInApp`에 위치
-   엔트리 포인트 `src/social-in-app.js`, 템플릿 `public/social-in-app-template.pug`
-   android/iOS 앱 내 투데이 메뉴를 위해 웹 투데이를 래핑
-   클라이언트가 페이지를 부를 때 파라미터로 넘겨주는 유저 정보로 임시 로그인 상태를 만듦

### 웹서버

-   node.js + express + pug[https://pugjs.org/api/getting-started.html]
-   웹앱 및 템플릿 파일 서빙
-   API 서버 래핑
