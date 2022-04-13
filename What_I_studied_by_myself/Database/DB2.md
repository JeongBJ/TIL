# 🌱DB 02

## ✅ Foreign Key

### Foreign Key

- 외래 키(외부 키)
- 관계형 데이터베이스에서 한 테이블의 필드 중 다른 테이블의 행을 식별할 수 있는 키
- 참조하는 테이블에서 속성(필드)에 해당하고, 이는 참조되는 테이블의 기본 키(Primary Key)를 가리킨다.
- 참조하는 테이블의 외래 키는 참조되는 테이블 행 1개에 대응된다.
  - 이 때문에 참조하는 테이블에서 참조되는 테이블의 존재하지 않는 행을 참조할 수 없다.
- 참조하는 테이블의 행 여러 개가 참조되는 테이블의 동일한 행을 참조할 수 있다.



#### Foreign Key의 예시

- 하나의 게시글과 댓글간의 모델 관계(1:N 관계)
  - **1:N 관계**에서 **외래키는 N의 역할을 하는 모델**이 가지게 된다.

#### Foreign Key의 특징

- 키를 사용하여 부모 테이블의 유일한 값을 참조(**참조 무결성**)

- 외래 키의 값이 반드시 부모 테이블의 **기본 키일 필요는 없지만 유일한 값**이어야 한다.
  - 보통 부모 테이블의 pk(id)가 기본 키값의 역할을 수행한다.

#### [참고] 참조 무결성

- 데이터베이스 관계 모델에서 관련된 2개의 테이블 간의 일관성을 말한다.
- 외래 키가 선언된 테이블의 외래 키 속성(열)의 값은 그 테이블의 부모가 되는 테이블의 기본 키 값으로 존재해야 한다

#### ForeignKey

- A many-to-one relationshop
- 2개의 위치 인자가 반드시 **필요**
  1. **참조하는 model class**
  2. **on_delete 옵션**
- migrate 작업 시 외래키 필드 이름에 _id를 추가하여 데이터베이스 열 이름을 만든다.
  - 시험문제 ) 'Comment 모델은 외래키 article_id를 포함합니다.' 라고 적혀있다면 외래키 필드명은 article이다.

#### comment 모델 정의하기

``` python
# articles/models.py
# 댓글은 articles 앱에서 구현한다.(게시판 요소)

class Comment(models.Model):# Comment 모델(모델 클래스)은 다음과 같은 필드들을 가진다.
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    # 외래키와 관련된 변수는, 1:N관계에서 참조되는 모델의 단수형 소문자로 작성한다.(M:N관계에서는 복수형으로 작성한다.)
    # 외래키 필드, ForeignKey필드를 사용한다.(Field가 붙지 않음에 유의한다.)
    # 두개의 인자를 사용한다. 참조하는 모델과 on_delete
    content = models.CharField(max_length=200)
    # 댓글의 내용
    created_at = models.DateTimeField(auto_now_add=True)
    # 생성일
    updated_at = models.DateTimeField(auto_now=True)
    # 수정일
    
    def __str__(self):
        return self.content
    # 객체 표현방식
```



#### ForeignKey arguments - 'on_delete'

- 외래 키가 참조하는 객체가 사라졌을 때 외래 키를 가진 객체를 어떻게 처리할 지를 정의한다.
- Database Integrity(데이터 무결성)을 위해서 매우 중요한 설정
- on_delete 옵션에 사용가능한 값들
  - CASECADE : 부모 객체(참조 된 객체)가 삭제 됐을 때 이를 참조하는 객체도 삭제
  - PROTECT, SET_NULL, SET_DEFAULT, SET(), DO_NOTHING, RESTRICT

#### [참고] 데이터 무결성

- 데이터의 정확성과 일관성을 유지하고 보증하는 것을 가리키며, 데이터베이스나 RDBMS 시스템의 중요한 기능

- 무결성 제한의 유형
  1. 개체 무결성(Entity integrity)
     - PK의 개념과 관련
     - 모든 테이블이 PK를 가져야 하며 PK로 선택된 열은 고유한 값이어야 하고 빈 값은 허용치 않음을 규정
  2. 참조 무결성(Referential integrity)
     - FK(외래 키) 개념과 관련
     - FK 값이 데이터 베이스의 특정 테이블의 PK 값을 참조하는 것
  3. 범위(도메인) 무결성(Domain  integrity)
     - 정의된 형식(범위)에서 관계형 데이터 베이스의 모든 컬럼이 선언되도록 규정

#### 데이터베이스의 ForeignKey 표현

- 만약 ForeignKey 인스턴스를 abcd로 생성했다면 abcd_id로 만들어진다
- 하지만 명시적인 모델관계 파악을 위해 참조하는 클래스 이름의 소문자(단수형)으로 작성하는 것이 바랍직하다.(1: N)

#### 댓글 생성하기

1. ``` bash
   $python manage.py shell_plus
   ```

2. ``` bash
   comment = Comment()
   # Comment 클래스를 통해 comment라는 인스턴스 생성, 
   ```

3. ``` bash
   comment.content = 'First comment'
   comment.save()
   # NOT NULL cONSTARINT Failed(외래키 값이 비어있음)
   ```

4. ``` bash
   # 게시글 먼저 빠르게 생성
   article = Article.objects.create(title='title', content='content')
   ```

   - comment.article_id 와 같이 콕 찝을 경우, aomment.article_id = article.pk로 작성하여야 한다.
   - comment.article = article로 사용할 수 있다.

5. ``` bash
   comment.article = article
   ```

6. ``` bash
   comment.save()
   ```

7. ```bash
   comment.pk
   ```

8. ``` bash
   comment.article.content
   # 외래키를 가지고 있는 입장에서는 참조가 아주 간단하게 된다.
   ```



#### admin site에서 작성된 댓글 확인

``` python
# articles/admin.py

from .models import Article, Comment

admin.stie.register(Comment)
```

``` bash
$python manage.py createsuperuser
```



#### 1:N 관계 related manager

- 역참조('comment_set')
  - Article(1) - > Comment(N)
  - article.comment 형태로는 사용할 수  없고, article.comment_set manager가 생성된다.
    - 댓글을 출력할 수  있다.
  - 게시글에 몇 개의 댓글이 작성 되었는지 Django ORM이 보장할 수 없기 때문이다.
    - article은 comment가 있을 수도, 없을 수도 있다.
    - 실제로 Article 클래스에는 Comment 와의 어떠한 관계도 작성되어 있지 않다.
- 참조('article')
  - Comment(N) -> article(1)
  - 댓글의 경우 어떠한 댓글이든 반드시 자신이 참조하고 있는 게시물이 있으므로, comment.article과 같이접근할 수 있다.
  - 실제 ForeignKey 또한 Comment 클래스에서 작성된다.

#### ForeignKey arguments - 'related_name'

- 역 참조시 사용할 이름('model_set' manager)을 변경할 수 있는 옵션

  ``` python
  # articles/models.py
  
  class Comment(models.Model):
      article= models.ForeignKey(Article, on_delete=models.CASCADE, related_name='contents')
  ```

- 위와 같이 변경하면 article.comment_set은 더이상 사용할 수 없고, article.comments로 대체된다.

  - article.comment_set.all() -> article.comments.all()

- [주의] 역참조 시 사용할 이름 수정 후, migration 과정이 필요하다.

- [주의] 1:N에서 굳이 명칭을 바꾸지 않는다. (comments_set 그냥 사용하도록 하자.)





### Comment CREATE

### Comment READ

### Comment DELETE



## ✅ Customizing authentication in Django

### Substituting a custom User model

### Custom user & Built-in auth forms



