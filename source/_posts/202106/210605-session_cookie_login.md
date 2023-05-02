---
title: 210605 cookie, session, JWT(JSON Web Token)
date: 2021-06-05 08:02:00
tags:
  - Cookie
  - Session
  - JWT
  - CORS
categories:
  - Dev-Environment
---

<div align="center">
  <img src="/images/post_images/210605_cookie-session.png" alt="Session & Cookie">
</div>

## <b>cookie와 session이 필요한 이유</b>

cookie와 session이 필요한 이유를 이해하기 위해서는 `HTTP 프로토콜의 connectionless, stateless한 특성에 대한 이해`가 필요하다.
`connectionless`는 클라이언트에서 요청을 한 뒤에 서버로부터 응답을 받으면 해당 연결을 끊어버리는 HTTP 프로토콜의 특징이다. HTTP가 TCP를 기반으로 구현되었기 때문에 네트워크 관점에서 keep-alive는 옵션으로, 연결비용을 줄이는 것을 장점으로 비연결지향이라고 한다.

`stateless`는 클라이언트와 서버의 통신이 끝나면 상태를 유지하지 않는 HTTP 프로토콜의 특징이다. 연결이 끊기는 순간 클라이언트와 서버간의 통신이 끝나고, 상태 정보는 유지하지 않는 것이 특징이다.

cookie와 session은 앞서 설명한 HTTP 프로토콜의 두 가지 특징(`connectionless, stateless`)이 가지는 단점을 해결하기 위해 사용된다.
우리가 특정 웹 페이지를 이용할때 한 번의 로그인을 한 뒤에 로그인한 사용자에 대한 인증을 유지하는 것이 바로 쿠키와 세션을 사용했기 때문이다. 쿠키와 세션을 사용하지 않는다면, 새로운 페이지로 이동할때마다 다시 로그인을 해야한다.

## <b>cookie, session, JWT(JSON Web Token)</b>

cookie와 session의 차이점에 있어, 정보가 저장되는 위치와 보안에 대한 부분도 있지만 이보다 더 중요한 것은 `lifecycle에 대해 이해`하는 것이 중요하다. cookie와 session 모두 만료시간이 있지만, cookie의 경우에는 클라이언트의 로컬에 파일로 저장되기 때문에 브라우저가 종료되어도 계속 정보가 남아있지만, 세션의 경우에는 만료시간과 상관없이 브라우저가 종료되면 삭제된다는 특징을 가지고 있다.

  <!-- more -->

- ## <b>cookie</b>

  - 쿠키는 클라이언트(브라우저) 로컬에 저장되는 키와 값이 들어있는 데이터 파일이다.
  - 사용자 인증 유효시간을 지정할 수 있으며, 유효기간이 정해지면 브라우저가 종료되어도 지정된 유효기간까지 인증이 유지된다는 특징을 가진다. (라이프 사이클)
  - 쿠키는 `클라이언트의 상태정보를 로컬에 저장`했다가 참조한다.
  - 클라이언트에 최대 300개까지 쿠키를 저장할 수 있으며, 하나의 도메인당 20개의 값을 가질 수 있다. 하나의 쿠키값은 4KB까지 저장하게 된다.
  - `서버 사이드에서 클라이언트의 요청에 대한 응답을 보낼때` Response header에 Set-Cookie 속성을 사용하면 클라이언트에 쿠키를 생성할 수 있다.
  - 클라이언트가 요청을 할때, 별도의 요청을 하지 않아도, 브라우저가 자동으로 Request header에 쿠키를 넣어서 전송하게 된다.
  - 쿠키는 로컬에 저장되기 때문에 서버로의 요청시에 스니핑 당할 우려가 있어 보안에 취약하다.

  - ### <b>cookie의 구성</b>

    cookie는 이름, 값, 유효시간, 도메인, 경로, 총 5가지 요소로 구성이 되어있으며, 아래와 같은 특징을 지닌다.

    - **이름** : 쿠키를 구별하는데 사용된다.
    - **값** : 쿠키의 이름과 mapping된 값이다.
    - **유효시간** : 쿠키의 유지시간이다.
    - **도메인** : 쿠키를 전송할 도메인 정보이다.
    - **경로** : 쿠키를 전송할 요청 경로이다.

  - ### <b>cookie의 동작 방식</b>

    클라이언트에서 서버로 페이지를 요청하면, 서버에서 쿠키를 생성해서 response header에 포함시켜 응답을 보내준다. 여기서 중요한 특징은 브라우저가 종료되어도 쿠키 만료 기간이 남아있다면, 클라이언트에서 보관을 한다.
    클라이언트에서 같은 요청을 하는 경우, request header에 쿠키를 담아서 함께 보낸다.
    서버에서 쿠키를 읽어서 이전 상태 정보를 변경할 필요가 있을때, 쿠키를 업데이트하고 response header에 업데이트된 쿠키를 담아서 응답한다.

    우리가 흔히 사이트를 방문할때 `아이디와 비밀번호를 변경 혹은 저장하겠느냐?`는 팝업창이 뜨는데, 이것이 바로 서버 사이드에서 업데이트된 쿠키를 전달했을 경우에, 클라이언트의 기존 쿠키 정보와 다른 경우에 팝업이 되는 것이다.

    `쇼핑몰의 장바구니 기능`과 `자동 로그인 기능`, 팝업창에 있는 `오늘 더 이상 이창을 보지 않음` 과 같은 기능이 대표적인 쿠키의 사용 예이다.

- ## <b>session</b>

  - session 또한 cookie를 기반으로 하지만, 사용자 정보 파일을 브라우저가 아닌 서버 측에 저장한다.
  - session은 cookie를 사용해서 session ID 만을 저장한다.
  - 서버에서는 클라이언트를 구분하기 위해 session ID를 부여하고, 브라우저가 서버에 접속한 순간부터 종료되는 시점까지 인증상태를 유지한다. (`접속시간을 제한하여 일정 시간 응답이 없으면 정보가 유지되지 않게 설정 가능`)
  - 쿠키와는 다르게 사용자에 대한 정보를 서버에 두기 때문에 쿠키보다는 보안적 측면에서 좋지만, 접속 사용자 정보가 많아질수록 서버 메모리를 많이 차지하게 된다. (동시간 접속자 수가 많은 웹 사이트의 경우, 서버에 과부화를 주게 되므로 성능저하의 요인)
  - 클라이언트에서 요청을 보내면 서버가 클라이언트에게 unique한 ID를 부여하게 되는데, 이것이 바로 session ID이다.
  - session은 서버의 자원을 사용하며, 서버의 처리가 필요하기 때문에 요청 속도가 쿠키보다 느리다.

    - ### <b>session의 동작 방식</b>

      클라이언트가 서버에 접속하게 되면, session ID를 발급받는다. 이후 클라이언트는 발급받은 session ID를 쿠키를 사용해서 저장하고 가지고 있는다.
      클라이언트는 서버에 특정 요청을 할 때, 이 쿠키의 session ID를 서버에 전달해서 사용한다.
      서버는 session ID를 전달받고, 해당 session ID로 session에 있는 사용자 정보를 가져온다.
      사용자 정보를 가지고 서버 요청을 처리하고 클라이언트에 응답한다.

      세션은 각 각의 클라이언트에게 고유의 ID를 부여하고, session ID를 통해 클라이언트를 구분해서 요청을 처리하게 된다. `대표적으로 로그인과 같은 보안상 중요한 작업을 수행할 때 사용`된다.

- ## <b>cache</b>

  캐시는 이미지나 CSS, JS 파일등을 브라우저나 서버의 앞 단에 저장해놓고 사용하는 것을 말한다.
  종종 서버에서 변경이 일어나도 이미 브라우저에 저장된 캐시를 참조해서 변경된 내용이 반영되지 않는 경우가 생기는데, 이 경우에는 `cache를 지워주거나, 서버에서 클라이언트로 응답을 보낼때 header에 cache의 만료시간 명시하는 방법`을 이용하면 해결할 수 있다.

- ## <b>JWT(JSON Web Token)</b>
  세션은 사용자의 수 만큼 서버 메모리를 차지하기 때문에 이러한 서버의 메모리상의 문제를 보안한 token 기반의 인증방식인 `JWT(Json Web Token)`을 사용하는 추세이다.

## <b>쿠키와 세션을 로그인의 흐름</b>

**로그인을 하게 되면, 브라우져와 백엔드 서버는 서로 같은 로그인 사용자 정보를 갖고 있어야 한다.**
그러므로 백엔드에서 로그인이 성공했다면, 프론트엔드로 로그인된 사용자 정보를 보내줘야 한다. 하지만, 백엔드에서 프론트엔드로 로그인한 사용자 정보를 그대로 보내준다면 어떻게 될까? 비밀번호와 같은 정보가 그대로 노출되어 계정이 보안에 취약해진다.

이러한 문제로 인해 백엔드에서 프론트엔드로 사용자 정보를 보낼때 데이터를 그대로 보내지 않고, 랜덤한 문자열을 보내주게 되는데 이것이 바로 `쿠키(Cookie)`이다.
실제 정보를 대신해서 랜덤한 문자열 토큰을 보내주게 되는 것이다.

보냄과 동시에 백엔드 서버에서는 로그인한 사용자 정보를 해당 쿠키와 연결되어있다고 정의를 하게 되는데, 이 부분이 바로 `세션(Session)`이다.

이렇게 쿠키와 세션이 정의가 된 이후부터는 `브라우져를 통해 게시글이나 댓글을 작성하게 되면, 쿠키를 함께 담아서 백엔드로 보내게 된다.`
백엔드 서버에서는 전달받은 쿠키를 읽어서 어떤 사용자인지 파악한 뒤에 해당 요청을 처리하게 된다.

## <b>백엔드 서버에서의 사용자 정보</b>

백엔드 서버에서 로그인한 사용자의 모든 정보를 보관하게 되면, 사용자가 많은 경우, 서버가 과부화 걸린다. 이러한 이유로 `사용자의 id만을 저장하고, 전체 사용자 정보를 복구할 때에는 해당 사용자 id와 mapping되는 전체 사용자 정보를 DB에서 참조`를 한다.

## <b>Passport.js를 활용한 로그인 흐름</b>

아래는 passport-local을 사용한 일반 로그인 과정의 예시이다.

`Front-End`
(1) 로그인 폼에 이메일과 비밀번호를 입력
(2) saga의 로그인 함수 실행

`Back-End`
(3) login POST router 실행
(4) passport.authenticate('local', ...) 부분이 실행
(5) passport/local.js 로그인 전략 부분이 실행
(6) (5)의 결과가 passport.authenticate('local', ...) 부분의 callback 함수로 전달되고, 로그인에 문제가 없는 경우, passport 로그인 시도(`req.login`)
(7) passport/index.js의 `serializeUser`부분이 실행된다.
`serializeUser`부분에서는 cookie에 묶어 줄 로그인 사용자 id를 전달한다.
`deserializeUser`부분에서는 로그인이 성공한 이후부터의 요청부터 `done(null, user)를 통해 req.user로 사용자 정보를 전달`한다.
(8) 내부적으로 header를 통해 Front-End로 전달할 cookie 정보를 전달한다.
(9) Cookie 정보와 함께 Front-End로 사용자 정보를 전달한다.

**노트필기 참고**

<div align="center">
  <img src="/images/post_images/210605_login_session_cookie.png" alt="Session & Cookie, Login 노트필기">
</div>

## <b>로그인 기능 구현(passport)</b>

passport, passport-local 설치

```zsh
$ npm i passport passport-local
```

`passport-local은 일반적인 id, password를 통한 로그인을 위한 라이브러리`

`back/passport/index.js`
<ins><b>작성된 index.js 파일은 app.js에서 사용된다.</b></ins>

```javascript
const passport = require('passport');
const local = require('./local');

module.exports = () => {
  passport.serializeUser(() => {});

  passport.deserializeUser(() => {});
  // 작성한 login 전략이 포함된 local.js 파일
  local();
};
```

`passport-local 로그인 전략 작성`

```javascript
const passport = require('passport');
const { Strategy: LocalStrategy } = require('passport-local');
const { User } = require('../models');
const bcrypt = require('bcrypt');

module.exports = () => {
  //(객체, 함수)
  passport.use(
    new LocalStrategy(
      {
        //req.body.email
        usernameField: 'email',
        // req.body.password
        passwordField: 'password'
      },
      async (email, password, done) => {
        // 로그인 전략
        try {
          const user = await User.findOne({
            where: { email }
          });
          // user가 존재하지 않는 경우
          if (!user) {
            // 서버에러, 성공, 클라이언트 에러
            return done(null, false, { reason: '존재하지 않는 사용자입니다.' });
          }
          // user가 존재하는 경우, 비밀번호 체크
          const result = await bcrypt.compare(password, user.password);
          if (result) {
            // 비밀번호가 일치하는 경우, 서버에 user 정보를 보내준다.
            return done(null, user);
          }
          // password가 일치하지 않는 경우
          return done(null, false, { reason: '비밀번호가 틀렸습니다.' });
        } catch (error) {
          console.error(error);
          return done(error);
        }
      }
    )
  );
};
```

`back/passport/local.js`

```javascript
// login 전략
const passport = require('passport');
const { Strategy: LocalStrategy } = require('passport-local');

module.exports = () => {
  passport.use(new LocalStrategy());
};
```

`back/routes/user.js`

```javascript
...
const passport = require('passport');

// 일반 로그인인 경우 작성한 passport-local login 전략을 사용한다.
// 앞서 전략에서 작성한 done([서버에러], [성공], [클라이언트 에러])가
// 아래의 callback 함수의 err, user, info로 넘어간다.

// 아래와 같이 middleware를 확장해서 작성할 수 있다.(next를 통해 에러를 전달하기 위해서)
router.post('/login', (req, res, next) => {
    passport.authenticate('local', (err, user, info) => {
        if (err) {
            console.error(err);
            return next(err);
        }
        if (info) {
            // client error
            // 401: 허가되지 않음.
            return res.status(401).send(info.reason);
        }
        // passport login을 사용해서 로그인을 실행한다.
        return req.login(user, async(loginErr) => {
            //passport 로그인 에러발생 처리
            if (loginErr) {
                console.error(loginErr);
                return next(loginErr);
            }
            // 최종 로그인 성공시, 사용자 정보를 프론트로 넘겨준다.
            return res.json(user);
        });
    })(req, res, next);
});
```
