# blueshw.github.io

## project
* [HEXO 프레임워크](https://hexo.io/ko/)
* 문장 형식은 `~입니다`, `~요`를 사용한다.

### hexo 설치
hexo 명령어를 global로 사용할 수 있도록 -g 옵션으로 설치
```
npm install -g hexo-cli
```

### project 구조
~~~
- scaffolds : layout의 기반 구조 (post, page, draft 등)
- themes : [base theme](https://github.com/Kaijun/hexo-theme-huxblog.git)
- source : 소스 폴더
    - img : 공통으로 사용하는 이미지
    - _post : post 소스 폴더
~~~

## writing
hexo new 명령어로 새로운 포스팅 폴더를 만든다.
새롭게 생성한 post는 `source/_posts` 폴더에 저장된다.
```
hexo new [layout] <title>
(ex) hexo new post my-first-post
```

별도의 placeholder를 사용하지 않고 파일명(폴더명)이 url의 directory가 되도록 `-(dash)`로 띄어쓰기를 구분한다.
```
url 체계
/{year}/{month}/{day}/{title}

(ex)
title: this-is-my-first-post
=> https://blueshow.github.io/2018/01/15/this-is-my-first-post
```

## test
로컬환경에서 실행하기
```
hexo server
```

기본 테스트 페이지
```
http://localhost:4000
```

## generating & deploy
정적파일을 생성(generate)하고 blueshw.github.io 레파지토리에 배포(deploy)한다.
레파지토리 정보는 `_config.yml`을 참조한다.
```
hexo generate
hexo generate --watch
hexo deploy --generate (생성 + 배포)
```
