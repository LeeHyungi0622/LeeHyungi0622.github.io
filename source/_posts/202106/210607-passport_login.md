---
title: 210607 passport.js 로그인 기능구현
date: 2021-06-07 08:07:00
tags:
  - Passport.js
categories:
  - NodeJS
---

<div align="center">
  <img src="/images/post_images/210607_nodejs_passport_authentication.png" alt="Passport.js 로그인 인증">
</div>

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

`deserializeUser`부분은 로그인이 성공한 이후부터의 요청부터 router가 실행되기 이전에 실행된다.
deserializeUser부분이 실행되면, 저장되었던 사용자의 id를 토대로 사용자 정보를 복구해서 `done(null, user)를 통해 req.user로 사용자 정보를 전달`한다.

이를통해 router에서는 req.user로 사용자 정보의 참조가 가능해진다.

(8) 내부적으로 header를 통해 Front-End로 전달할 cookie 정보를 전달한다.
(9) Cookie 정보와 함께 Front-End로 사용자 정보를 전달한다.

**노트필기 참고**

<div align="center">
  <img src="/images/post_images/210605_login_session_cookie.png" alt="Session & Cookie, Login 노트필기">
</div>

  <!-- more -->

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
