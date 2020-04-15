## ORM이란?
ORM(Object Relationla Mappings)이란 프로그램상의 객체(Object)와 DB의 테이블(Realation)이 일대일 대응하는 관계를 맺는것(Mapping)을 의미한다.
ORM에서 "객체 == 테이블"이라 생각해도 된다.

ORM에서는 테이블과 매핑되는 객체의 메서드를 통해 쿼리를 조작할 수 있다.
쿼리를 메서드로 호출하고, 메서드를 호출하면 내부적으로 쿼리가 실행된다.

ORM을 이용하면 SQL Query가 아닌 메서드로 데이터를 조작할 수 있어 객체 모델로 프로그래밍 하는데 집중 할 수 있다.
각종 객체에 대한 코드를 별도로 작성하기 때문에 코드의 가독성을 올려준다. 또한 특정 데이터베이스에 대한 종속성이 사라져서
데이터베이스가 바뀌는 상황에도 유연하게 대처할 수 있으며, 데이터베이스 마이그레이션을 쉽게 할 수 있다.


## Sequelize
Node.js에서 사용할 수 있는 ORM 중 하나이며 Promise 문법을 사용한다.

**1. 설치하기**
```
npm install --save sequelize
npm install --save mysql2
npm install -g sequelize-cli 
```

**2. sequelize 초기화**
```
sequelize init
```
sequelize init을 통해 호출 하면 config, models, migrations, seeders 폴더가 생긴다.

**3. DB 커넥션 정보 설정**  
/config/config.json 파일에서 development에서 자신의 DB 정보로 바꿔주면 된다.
```javascript
"development": {
    "username": "root", //database사용자명
    "password": null,
    "database": "database_development", //database이름
    "host": "127.0.0.1", //host 주소
    "dialect": "mysql", //사용할 RDB
    "operatorsAliases": false
  },
```

**4. sync() 작성**  
/models/index.js 파일에서 정의한 모델들을 바탕으로 실제로 Model을 등록하는 일을 합니다.
따라서 sync() 메서드를 호출하면 모델을 사용할 준비가 되는 것입니다.
sync()는 모델에서 정의한 이름을 갖는 테이블이 존재하지 않을 경우에만 동작한다.
```javascript
...

const models = require("./models/index.js");

models.sequelize.sync().then( () => {
  console.log(" DB 연결 성공");
}).catch(err => {
  console.log("연결 실패");
  console.log(err);
})

...
```

**5. 모델 정의 : CLI로 정의**  
```
sequelize model:create --name TABLE_NAME --attributes "COLUMN1:type, COLUMN2:type, COLUMN3:type"
```
- migrations 폴더
  - 현재 시간을 이름으로 갖는 migration 파일이 생성
- models 폴더
  - TABLE_NAME.js 파일이 생성
- CLI를 통해 모델을 생성했을 때의 특징
  - 테이블 이름이 복수형으로 변경
  - 자동으로 id, createAt, updateAt 3개의 필드를 생성
  - migration 파일을 생성

**작업순서**
1) model:create 명령어 실행
2) /models/TABLE_NAME.js 컬럼 제약 추가 and 관계설정 
3) /magrations/date-create-TABLENAME.js 모델 정의 일치시키기 (데이터타입 sequelize로 바꿀것)
4) migrate 명령어 실행 
5) 서버 실행

## 모델정의하기 예제
**5.1) 테이블 모델 만들기**
```
sequelize model:create  --name user2 --attributes "user_id:string, password:string"
```
**/models/user2.js**
```javascript
'use strict';
module.exports = (sequelize, DataTypes) => {
  const user2 = sequelize.define('user2', {
    user_id: DataTypes.STRING,
    password: DataTypes.STRING
  }, {});
  user2.associate = function(models) {
    // associations can be defined here
  };
  return user2;
};
```

**5.2.1) 모델에 옵션 부여하기**  
**/models/user2.js**
```javascript
'use strict';
module.exports = (sequelize, DataTypes) => {
  var user2 = sequelize.define('user2', {
    user_id: {
      type: DataTypes.STRING,
      unique: true
    },
    password: {
      type: DataTypes.STRING,
      allowNull: false
    }
  }, {
    classMethods: {
      associate: function(models) {
        // associations can be defined here
      }
    }
  });
  return user2;
};
```

**5.2.2) 관계설정하기**  
모델 관계는 모델을 정의하는 파일에서 정의합니다.  
associate 프로퍼티를 함수로 정의하면, /models/index.js에서 post모델에 대한 관계를 알 수 있게됩니다.
**/models/post.js**
```javascript
'use strict';
module.exports = (sequelize, DataTypes) => {
  ...

  //관계설정 (1:M)
  post.associate = function (models) {
    post.hasMany(models.reply);
  };

  return post;
};
```

- post 객체에 associate 프로퍼티를 함수로 정의하면, /models/index.js에서 post모델에 대한 관계를 알 수 있습니다.
- 게시글과 댓글 1 : M 관계이므로, 이에 대한 메서드인 post.hasMany() 메서드를 호출하여, post는 많은 reply를 갖도록 합니다.
  - 인자로는 1 : M 관계에서 M에 해당하는 모델을 작성합니다.
  - 예제에서는 reply 모델이므로 models.reply를 전달다.

**/models/reply.js**
```javascript
'use strict';
module.exports = (sequelize, DataTypes) => {
  ...

  //관계설정 (post는 여러개의 reply를 가짐)
  reply.associate = function(models){
    reply.belongsTo(models.post, {
      foreignKey: "postId"
    })
  };

  return reply;
};
```
- reply는 하나의 post에 소속돼야 하는데, 이를 표현한 메서드는 belongsTo()입니다.
  - 즉, reply는 하나의 post에 포함된다는 의미로 reply.belongsTo() 메서드를 호출합니다.
- reply는 post와 다르게 하나의 post에 속해야 하므로 어떤 객체( 게시글 )에 속할 것인지 알아야 합니다.
  - 따라서 외래키로써 postId를 사용하겠다는 것을 객체 형태로 두 번째 인자에 전달합니다.
  - 이것이 reply 모델을 정의할 때 postId 필드를 정의한 이유입니다.


**5.3) migration 파일 수정해주기**  
**/migrations/20190720024218-create-user-2.js**
``` javascript
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('user2s', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      user_id: {
        type: Sequelize.STRING,
        unique: true
      },
      password: {
        type: Sequelize.STRING,
        allowNull: false
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('user2s');
  }
};
```
**/models/user2.js의 user_id, passwd 프로퍼티 부분만 복붙하면 되는데, 이 과정에서 DataTypes를 Sequelize로 바꿔줘야 합니다.**

**5.4) migrate 명령어 실행**
```
sequelize db:migrate
```

**출처**  
[sequeilze(2) - 모델정의하기](https://victorydntmd.tistory.com/27)
