# Paprika Web

## DOCUMENTATION WORK IN PROGRESS

## Frontend Tech & Tools

<p align="left"><img src="https://skillicons.dev/icons?i=html,css,javascript,react,redux,styledcomponents,vite,jquery,aws,bash,git,github,githubactions,vscode,figma&perline=8" /></p>

The project is bootstrapped with [Vite](https://vitejs.dev)

## Configuration

### Environment variables

```bash
.env.localtest # local environment
.env.develop # develop environment
.env.staging # staging environment
.env.production # production environment
```

### Installation
- from root: `pnpm install:web`, from apps/web: `pnpm install`

### Development
- `pnpm dev:[ENV_NAME]` ENV_NAME: `local`, `develop`, `staging`, `prod`

**NOTE:** When developing locally, make make sure you run the paprika_web server locally on the specified port in your `.env.localtest` file.

### Google APIs OAuth Client

The project uses the Google APIs OAuth client to enable social login functionality. For project configuration details, please refer to the troubleshooting [section](#google-apis---not-a-valid-origin-for-the-client).


#### Bundle Analysis, Visualization Tool Scripts

- find unused files, dependencies and exports: `pnpm knip`
- Run bundle build visualizer: `pnpm visualizer`

## Build

### Build Scripts

Each script in the project uses variables from the corresponding `.env.[ENV_NAME]` file. The following scripts are available:

- `build:local`
- `build:develop`
- `build:staging`
- `build:production`

## Deployment

### AWS Architecture

The diagram displays an overview of the AWS architecture. The files of ReactJS SPA web application are hosted in an S3 bucket, which is accessed by Cloudfront.

```mermaid

stateDiagram-v2

  direction LR

  AWS_CF: AWS CloudFront
  AWSS3: AWS S3
  Edge_Loc: Edge Location

    Browser --> AWS_CF
    state AWS_CF {
      Edge_Loc --> Bucket
    }
    state AWSS3 {
      Bucket
    }
```

### Deployment Scripts

Refer to github actions in root `.github/workflows/**`

#### Project Actions

- `react-app.develop.yml` **develop** branch default workflow
- `react-app.staging.yml` **staging** branch default workflow
- `react-app.production.yml` **production** branch default workflow
- `react-app.deployment.yml` **develop** branch deployment workflow
- `react-app.build.yml` default build workflow

#### Environments [develop | staging | production]

##### Variables

- **AWS_REGION** - AWS Region where the bucket is located, e.g.: `us-west-2`
- **BUILD_SCRIPT** - app built script, refer to [build scripts](#build-scripts), e.g.: `build:production`
- **NODE_VERSION** - node version, e.g.: `18.18.2`
- **PNPM_VERSION** - pnpm version, e.g. `8.10.1`

##### Secrets

- **AWS_CF_DISTRIBUTION_ID** - AWS Cloudfront Distribution ID (CloudFront > Distributions > P1H9P3BOK7034B), e.g.: `P1H9P3BOK7034B`
- **AWS_GITHUB_ACTIONS_ROLE** - Verify secret with cloud devops team. e.g.: `arn:aws:iam::123456890123:role/rs-sa-github-actions-appname-production`
- **AWS_S3_BUCKET_ARN** - S3 Bucket Amazon Resource Name (ARN) found in Bucket Permissions tab, e.g.: `arn:aws:s3:::example-app-staging`
- **AWS_S3_BUCKET_NAME** - S3 Bucket Name, e.g.: `example-app-staging`

#### Actions Resources

- Checkout action [docs](https://github.com/actions/checkout/tree/v3/)
- Artifacts
  - Upload@v3 actions [docs](https://github.com/actions/upload-artifact/tree/v3/)
  - Download@v3 actions [docs](https://github.com/actions/download-artifact/tree/v3/)
- Checkout@v3 action [docs](https://github.com/actions/checkout/tree/v3/)
- Pnpm package manager installer @v2 [docs](https://github.com/pnpm/action-setup/tree/v2/)
- Node installer action @v4 [docs](https://github.com/actions/setup-node/tree/v4/)
- AWS Credentials configuration actions @v2 [docs](https://github.com/aws-actions/configure-aws-credentials/tree/v2/)
- AWS CLI [docs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/index.html)
- Slack actions @v2 [docs](https://github.com/rtCamp/action-slack-notify/tree/v2/)

##### Github Actions 101

- [Blog Article](https://lo-victoria.com/github-actions-101-creating-your-first-workflow)
- [Github Docs](https://docs.github.com/en/actions/quickstart)

## Testing

## Troubleshooting

### Google APIs - "Not a valid origin for the client"

If you encounter the following error message below:

<p style="color:red;">
"Not a valid origin for the client: CLIENT_HOST_HERE has not been registered for client ID SOME_CLIENT_ID-UUID.apps.googleusercontent.com. Please go to https://console.developers.google.com/ and register this origin for your project's client ID."
</p>

Please ensure that your current host is added to the authorized origins in your Google API credentials. Access the [Google API Console](https://console.developers.google.com/) and follow the steps below:

1. Navigate to the Credentials section.
2. Locate **OAuth 2.0 Client IDs** and select **Website v5** project.
3. Ensure that the authorized origins list includes the URL of your current host.
4. If your current host URL is not listed, click on the "Edit" or "Add" button to include it.
5. Save the changes to update the authorized origins.






-------------------------------------------------------------------------

# Paprika Web

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
