# 제 2장 Toy Application
 이번 장에서는, Rails의 강력한 기능 몇가지를 소개하기 위한 어플리케이션을 작성해봅니다. 많은 기능을 자동적으로 생성하는 *scaffold* 제네레이터라고 하는 스크립트를 사용하여, 어플리케이션 빠르게 생성하고, 그것을 기반으로 고도의 Rails 프로그래밍과 Web 프로그래밍의 개요를 배웁니다. 컬럼1.2에서 말씀드렸다시피, 본 튜토리얼의 이후 장에서는 기본적으로 이번 장에서 배우는 것과는 반대로 접근하여, 조금씩 어플리케이션을 만들어나가며 각 단계와 개념을 설명할 것입니다. *scaffold*는 Rails 어플리케이션의 전체적인 뼈대를 빠르게 작성하기에 좋기때문에 이번장에서만 일부러 사용해보겠습니다. 작성할 Toy 어플리케이션은 브라우저의 주소창에 URL을 입력하면 동작합니다. 이것을 이용하여 Rails 어플리케이션의 구조와 Rails에서 권장하는 *REST* 아키텍쳐에 대해 생각해보도록 하는 시간을 가지려 합니다.

Toy 어플리케이션은 나중에 작성할 샘플 어플리케이션과 비슷한, 유저와 유저에 관련된 microposts로 구성되어 있습니다. Toy 어플레케이션은 동작은 합니다만, 완성품은 아닐뿐더러 많은 절차들이 마치 마법처럼 느껴지실 지도 모르겠습니다. 제 3장 이후에 작성할 샘플 어플리케이션에서는 이번에 작성할 Toy 어플리케이션의 기능을 하나씩 수동으로 만들 것입니다. 그만큼 시간이 걸리긴 하겠지만, 아무쪼록 마지막까지 본 튜토리얼을 진행해주실 것이라 믿습니다. 이번 장에서의 목적은 *scaffold* 를 사용한 즉각적이고 표면적인 이해가 아닌, 그 것을 뛰어너머 Rails의 깊은 레벨까지 이해할 수 있게 하는 것입니다.

## 2.1 어플리케이션의 계획
시작하기에 앞서, Toy 어플리케이션을 어떠한 것으로 만들 것인지, 계획을 세워보도록 합시다. 1.3에서 설명드렸다시피, `rails new` 커맨드를 이용하여 Rails의 버전번호를 지정하고, 어플리케이션의 뼈대를 생성하는 곳 부터 해봅시다.
```
$ cd ~/environment
$ rails _5.1.6_ new toy_app
$ cd toy_app/
```

1.2.1 에서 추천드렸던 클라우드IDE를 사용하시는 경우에는, 이번 어플리케이션을 첫 번째 어플리케이션과 같은 워크스페이스에 작성되는 것에 대해 주의할 필요가 있습니다. 두 번째 어플리케이션을 위해 별도의 워크스페이스를 작성할 필요는 없습니다. 파일이 표시되기 위해서는 파일 네비게이의 톱니바퀴 아이콘을 클릭하여 `[Refresh File Tree]`를 클릭합니다.

다음으로 Bundler를 이용하여 `Gemfile` 를 텍스트에디터로 편집해봅시다. 아래의 내용으로 수정해주세요.
```ruby
source ‘https://rubygems.org’

gem ‘rails’,        ‘5.1.6’
gem ‘puma’,         ‘3.9.1’
gem ‘sass-rails’,   ‘5.0.6’
gem ‘uglifier’,     ‘3.2.0’
gem ‘coffee-rails’, ‘4.2.2’
gem ‘jquery-rails’, ‘4.3.1’
gem ‘turbolinks’,   ‘5.0.1’
gem ‘jbuilder’,     ‘2.7.0’

group :development, :test do
  gem ‘sqlite3’, ‘1.3.13’
  gem ‘byebug’,  ‘9.0.6’, platform: :mri
end

group :development do
  gem ‘web-console’,           ‘3.5.1’
  gem ‘listen’,                ‘3.1.5’
  gem ‘spring’,                ‘2.0.2’
  gem ‘spring-watcher-listen’, ‘2.0.1’
end

group :production do
  gem ‘pg’, ‘0.20.0’
end

# Windows環境ではtzinfo-dataというgemを含める必要があります
gem ‘tzinfo-data’, platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```
위 젬리스트는 1.13에서 말씀드린 리스트와 동일합니다.

1.5.1에서 설명드린대로, `--without production` 옵션을 추가하는 것으로, 실제 배포환경의 gem을 제외한 로컬gem을 인스톨 합니다.
`bundle install --without production`
혹시 실행되지 않는다면, 1.3.1에서 설명드린 것 처럼, `bundle update` 을 실행해보세요.

마지막으로 Git으로 이번 Toy 어플리케이션의 버전을 관리해줍니다.
```
$ git init
$ git add -A
$ git commit -m “Initialize repository”
```

다음으로 Bitbucket 에 [Create] 버튼을 클릭하여 새로운 레포지토리를 생성합니다. 이어서 생성한 파일을 새로운 리모트 레포지토리에 푸시합니다.
```
$ git remote add origin git@bitbucket.org:<username>/toy_app.git
$ git push -u origin —all
```
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/create_demo_repo_bitbucket.png)
마지막으로 1.3.4에서 소개해드린 *Hello world!* 와 같은 순서로 배포준비를 해주세요.
*application_controller.rb*
```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  def hello
    render html: “hello, world!”
  end
end
```

*config.rb*
```ruby
Rails.application.routes.draw do
  root ‘application#hello’
end
```

이어서 현재 변경을 커밋하고, Heroku에 푸시합니다.
```
$ git commit -am “Add hello”
$ heroku create
$ git push heroku master
```

1.5와 마찬가지로, 경고메세지가 표시될 수도 있습니다만, 무시하셔도 괜찮습니다. 자세한 것은 7.5에서 설명드리겠습니다. Heroku어플리케이션의 주소 이외에는 아래와 같은 상태가 되어 있을 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/heroku_app_hello_world.png)
이 것으로 어플리케이션 자체를 작성하기 위한 밑작업을 끝냈습니다. Web어플리케이션을 만들 때에는 어플리케이션의 구조를 표현하기 위한 데이터 모델을 맨 처음에 작성하는 것이 보통입니다.

이번 장의 Toy 어플리케이션에서는 유저와 짧은 Micropost(Twitter에서의 트윗) 만을 표현하는 microblog를 작성해보겠습니다. 일단은 어플리케이션에서의 유저를 사용하기 위한 모델을 작성하고, 그 다음으로 micropost에서 사용하기 위한 모델을 작성합니다.

### 2.1.1 유저 모델설계
Web에서의 유저등록 방법이 매우 많은 것에서부터 알 수 있듯, 유저 라고하는 개념을 데이터모델로 표현하기 위한 방법은 매우 많습니다. 본 장에서는 최소한의 표현방법을 사용해보겠습니다.

각 유저는, 중복이 없는 유일한 키가 되는 `integer`형의 ID번호(`id`라고 부르겠습니다.) 를 할당하고, 이 ID와 더불어 일반에 공개하는 `string`형의 이름(`name`), 그리고 `string` 형태의 이메일주소 (`email`)을 가집니다. 이메일 주소는 유저이름으로써 사용됩니다. 유저의 데이터모델의 개요는 아래와 같습니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_user_model.png)
상세한 설명은 6.1.1에서부터 말씀드리겠습니다만, 위 그림의 `user`는 데이터베이스의 테이블(table)에 해당됩니다. 또한 `id`, `name`, `email`  의 속성은 각각 테이블의 컬럼에 해당됩니다.

### 2.1.2 Micropost의 모델 설계
마이크로포스트의 데이터모델은 유저 모델보다도 좀 더 심플합니다. `id` 와 마이크로포스트의 텍스트 내용을 기록하는 `text` 형의 `context` 로 구성됩니다. 하지만 실제로는, 마이크로포스트를 유저와 관련짓게 할 필요가 있습니다. (associate)  그러기 위해서, 마이크로포스트의 포스트 작성자를 기록하기 위한 `user_id` 를 추가합니다. 이것으로 데이터 모델은 아래와 같습니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_micropost_model.png)
위 그림에서는 `user_id` 라고하는 속성을 이용하여 1명의 유저에게 여러개의 마이크로포스트를 관련짓게 할 수 있는 구조를 간결하게 설명하고 있습니다. 상세하게는 제13장에서 설명하겠습니다.

## 2.2 Users 리소스
여기서는 2.1.1에서 설명한 유저를 위한 데이터모델을 표시하기 위해 Web 인터페이스를 만들어봅니다. 이 유저 데이터모델과 Web인터페이스를 조합하여 *User*리소스 라고 부르며, 유저라고 하는 것은  [HTTP 프로토콜](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 을 경유하여 자유자재로 작성/조회/갱신/삭제 가 가능한 오브젝트로 구분할 수 있게됩니다.  앞에서 말씀드린 것 처럼 , 이 User리소스는 모든 Rails프로젝트에서 표준적으로 사용할 수 있는 *scaffold* 제네레이터로 생성해봅니다. scaffold로 생성된 많은 코드를 지금 상세하게 읽어볼 필요는 없습니다. 지금 단계에서는 분명 혼란스러울 테니까요.

Rails의 scaffold는 `rails generate` 스크립트에 `scaffold` 커맨드를 입력하는 것으로 작성할 수 있습니다. `scaffold` 커맨드의 파라미터로는 리소스이름의 단수형을 한 형태로 (이번 경우에는 `User`) 를 사용하여, 필요한만큼의 데이터모델의 속성을 옵션으로써 파라미터로 추가할 수 있습니다.
```
$ rails generate scaffold User name:string email:string
      invoke  active_record
      create    db/migrate/20160515001017_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml
      invoke  resource_route
       route  resources :users
      invoke  scaffold_controller
      create    app/controllers/users_controller.rb
      invoke    erb
      create      app/views/users
      create      app/views/users/index.html.erb
      create      app/views/users/edit.html.erb
      create      app/views/users/show.html.erb
      create      app/views/users/new.html.erb
      create      app/views/users/_form.html.erb
      invoke    test_unit
      create      test/controllers/users_controller_test.rb
      invoke    helper

      create      app/helpers/users_helper.rb
      invoke      test_unit
      invoke    jbuilder
      create      app/views/users/index.json.jbuilder
      create      app/views/users/show.json.jbuilder
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/users.coffee
      invoke    scss
      create      app/assets/stylesheets/users.scss
      invoke  scss
      create    app/assets/stylesheets/scaffolds.scss
```

`name:string`과 `email:string` 옵션을 추가하여 User모델의 내용이 위에서의 user모델그림과 같게 되도록 작성합니다. (또한, `id` 파라미터는 Rails에 의해 자동적으로 Primary Key로 데이터베이스에 추가되기 때문에, 추가할 필요는 없습니다.)

계속해서 Toy 어플리케이션의 개발을 진행하기 위해, `rails db:migrate`를 실행하여 데이터베이스를 migrate할 필요가 있습니다.
```
$ rails db:migrate
==  CreateUsers: migrating =================================
— create_table(:users)
   -> 0.0017s
==  CreateUsers: migrated (0.0018s) ========================
```
위 커맨드는, 단순히 데이터베이스를 갱신하여 `user` 데이터모델을 작성하기 위한 커맨드입니다. (데이터베이스의 마이그레이션의 상세한 설명은 6.1.1 이후에 설명하겠습니다.)

또한 Rails 5 이전의 버전에서는 `db:migrate` 커맨드는 `rails` 커맨드가 아닌, `rake` 커맨드가 사용되었습니다.  Rails 4 이전의 오래된 Rails 어플리케이션에서는 Rake의 사용방법을 알아둘 필요가 있습니다. 상세한 내용은 컬럼2.1에서 확인해주세요.

###### 컬럼 2.1 Rake
> Unix에서는, 소스코드에서 실행용 프로그램으로 빌드하기 위해 주로 *[Make](http://en.wikipedia.org/wiki/Make_(software))* 라고 하는 툴을 사용해왔습니다. *Rake*는 이른바 Ruby에서의 Make 라고도 할 수 있습니다.  
> Rails 4이전에는 Rake를 사용했기 때문에, 오래된 Rails 어플리케이션을 다루기 위해서는 Rake에 대해 배울 필요가 있습니다. 아마도 자주 사용되었던 Rake커맨드는, 데이터베이스의 데이터 모델을 갱신하기 위해 `rake db:migrate`  커맨드와, 자동화된 테스트케이스를 실행하기 위한 `rake test` 두가지 커맨드였을 것입니다.  
> 또한 Rails 4이전의 어플리케이션에서는, `rake` 커맨드의 버전을 `Gemfile` 에 정의하고 있기 때문에, Bundler의 `bundler exec` 커맨드를 이용하여 실행할 필요가 있습니다. 예를 들어, Rails 5에서의 마이그레이션 커맨드는 다음과 같이 할 수 있습니다  
> `rails db:migrate`  
> Rails 4이전에서는 다음과 같이 실행할 필요가 있습니다.  
> `bundle exec rake db:migrate`  

migration작업이 끝나면, 다음 커맨드로 로컬 Web서버를 실행할 수 있습니다.
`rails server`

이것으로 1.3.2에서 설명드린 대로, 로컬서버가 동작할 것입니다. (클라우드IDE로 작업하는 분은, IDE에 탑재된 간단한 브라우저가 아닌, 반드시 *Chrome*이나 *Firefox* 등의 범용브라우저의 별도 탭을 이용하여 development서버의 결과를 확인해주세요.)

### 2.2.1 Users 화면을 움직여보자.
브라우저에서 루트URL (/) 로 접속해보면, *hello world!* 페이지가 표시되겠지만, Users리소스를 scaffold로 생성하였기 때문에, 유저관리용 화면이 많이 추가되어있는 점이 이전과는 다른 점입니다. 예를 들어, /user를 표시하면 모든 유저의 리스트를 표시하며, /user/new를 표시하면 신규 유저 작성 페이지가 표시됩니다. 본 장에서는 유저에 관련된 페이지에 대해 간단하게 설명하겠습니다. 아래에 작성되어 있는 페이지와 URL의 관계를 참조한다면 알기 쉬울 것 입니다.
URL			アクション	用途
/users		index	すべてのユーザーを一覧するページ
/users/1		show	id=1のユーザーを表示するページ
/users/new	new		新規ユーザーを作成するページ
/users/1/edit	edit		id=1のユーザーを編集するページ

일단 유저 리스트를 표시하는 `index` 액션의 URL(/users)를 확인해봅시다. 물론 이 시점에는 아직 유저가 등록되어 있지는 않습니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_blank_user_index_3rd_edition.png)
유저를 새롭게 등록하기 위해선, 위 그림의 `new` 페이지를 표시해봅니다. 또한 제 7장에서는 이 페이지가 유저 등록용 페이지로 바뀔 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_new_user_3rd_edition.png)
텍스트 필드에 이름과 이메일을 입력하고 [Create User] 버튼을 눌러보세요. 아래와 같은 `show` 페이지가 표시됩니다. (녹색의 welcome 메세지는 7.4.2에서 설명드리는 *flash* 라고 하는 기능을 이용하여 출력하고 있습니다.) 여기서 URL이 /user/1 이라고 표시되고 있는 것에 대해 주목해주세요. 생각하시는 것 처럼, 여기서 *1* 이라고 하는 숫자는, id속성을 나타내는 것입니다. 7.1에서는 이 페이지를 유저의 프로필 페이지로 만들 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_show_user_3rd_edition.png)
이번에는 유저의 정보를 변경하기 위해, /users/1/의 `edit` 를 출력해봅시다. 이 편집용 페이지에서 유저에 관한 정보를 변경하고, [Update User] 버튼을 누르면, Toy 어플리케이션 내부의 유저정보가 변경됩니다. (상세한 것은 제 6장에서 설명드리겠습니다만, 이 유저 정보는 Web 어플리케이션의 뒷단에 있는 데이터베이스에 저장되어 있습니다.)  Sample 어플리케이션에서도 유저를 편집 혹은 업데이트하는 기능을 10.1에서 구현합니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_edit_user_3rd_edition.png)

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_update_user_3rd_edition.png)
여기서 /user/new 페이지로 되돌아가서 유저를 한 명 더 작성해봅시다. index페이지에 접속해보면 아래와 같이 유저가 추가되어 있을 것입니다. 7.1에서는 좀 더 본격적인 유저 리스트페이지를 작성해볼 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_user_index_two_3rd_edition.png)
유저의 등록, 출력, 업데이트 방법 등에 대해 설명했습니다. 이번에는 유저를 삭제해봅시다. [Destroy] 링크를 클릭하면 유저는 삭제되고, index페이지의 유저는 1명만 남게 됩니다. (만약 이 설명대로 되지 않는다면, 브라우저의 Javascript가 유효한 상태인지 아닌지 확인해주세요. Rails에서는 유저의 삭제하는 리퀘스트를 작성할 때, Javascript를 사용합니다.)
또한 10.4에서는 Sample 어플리케이션에 유저를 삭제하는 기능을 구현해보고, 관리권한(admin)을 가진 유저 이외에는 삭제할 수 없게끔 제한을 걸 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_destroy_user_3rd_edition.png)

##### 연습
	1. CSS를 알고 계신 분들께 : 새로운 유저를 등록하고, 브라우저의 HTML inspector를 사용하여 “User was successfully created.”의 부분을 알아봐주세요. 브라우저를 새로고침하면, 그 부분은 어떻게 되나요?
	2. email을 입력하지 말고, 이름만 입력하고 등록하려고 하면 어떻게 되나요?
	3. “@example.com” 과 같은 잘못된 메일주소를 입력하여 업데이트하려고 할 때, 어떻게 되나요?
	4. 위의 연습문제에서 작성한 유저를 삭제해주세요. 유저를 삭제할 때, Rails는 어떠한 메세지를 표시하나요?

### 2.2.2 MVC의 처리
이 것으로 Users리소스의 개요에 대해 설명은 끝났습니다. 여기서 1.3.3에서 말씀드린 MVC(Model, View, Controller) 패턴의 관점에서 이 리소스를 확인해봅시다. 구체적으로는 “/user에 있는 index페이지를 브라우저에서 접속해보자“ 라는 조작을 할 경우, 내부에서는 어떠한 처리가 일어나고 있는지 MVC의 관점에서 설명하겠습니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/mvc_detailed.png)
위 그림에서 설명하고 있는 순서의 개요는 아래와 같습니다.
	1. 브라우저로부터 /user 라고 하는 URL의 리퀘스트를  Rails의 서버로 송신합니다.
	2. /user 리퀘스트는 Rails의 라우팅처리에 의해 Users 컨트롤러 내부의 `index` 액션에 할당됩니다.
	3. `index` 액션이 실행되고, 거기서 User모델에게 “모든 유저를 출력해라” (`User.all`) 의 쿼리가 실행됩니다.
	4. User모델은 쿼리를 실행하여 모든 유저정보를 데이터베이스에 접근, 출력하게됩니다.
	5. 데이터베이스로부터 획득한 유저 정보를 User모델에서 컨트롤러로 넘깁니다.
	6. User컨트롤러는 유저 정보(리스트)를 `@users` 변수 (Ruby는 @를 인스턴스변수로서 표현) 에 저장, `index` 뷰에 넘깁니다.
	7. index뷰가 실행되어, ERB(Embedded RuBy: 뷰의 HTML에 기술되어 있는 Ruby코드) 를 실행하여 HTML을 생성(렌더링)합니다.
	8. 컨트롤러는, 뷰에서 생성된 HTML을 받아 브라우저에게 넘깁니다.

위와 같은 처리를 좀 더 자세하게 알아봅시다. 제일 처음에는 브라우저로부터 송신되는 리퀘스트를 확인해봅시다. 이 리퀘스트는 주소창에 URL을 입력한다거나, 링크를 클릭했을 때 발생합니다(1번). 리퀘스트는  Rails Routing에 도착(2번)하고, 여기서 URL(과 리퀘스트의 종류는 컬럼 3.2를 참고)을 기반으로하여 적절한 컨트롤러의 액션에 할당됩니다.(dispatch) 유저로부터 리퀘스트받은 URL을, User리소스에서 사용하는 컨트롤러와 액션에 할당하는 코드는, 아래와 같습니다. 이러한 mapping하는 코드를 Rails의 라우팅설정파일(config/routes.rb)에 작성합니다. Rails에서는 config/routes.rb에서 URL과 액션의 조합으로 알기 쉽게 설정하고 있습니다. (`:user` 라고 하는 조금 신기하게 보이는 표현은, Ruby언어 특유의 “심볼” 이라고 불리는 것입니다. 자세한 것은 4.3.3에서 서술합니다.)
```ruby
Rails.application.routes.draw do
  resources :users
  root ‘application#hello’
end
```

그렇다면, 이 라우팅파일을 변경해봅시다. 서버의 루트URL에 접근하면, 디폴트 페이지 대신에 유저 리스트를 출력하게 해봅시다. 즉 “/“ 로 접속하면 /user를 표시하도록 합니다. 우리의 디폴트 페이지로 접속하는 루트URL은 다음과 같습니다.
`root 'application#hello'`

이걸로 루트에 접속하면, Aplicaiton컨트롤러 내부의 `hello` 액션에 접속하게 됩니다. 이번에는 Users컨트롤러의 index액션으로 접속하도록, 아래처럼 수정해봅시다.
``` ruby
Rails.application.routes.draw do
  resources :users
  root ‘users#index’
end
```

2.21 이후로 소개하는 각 페이지는, User 컨트롤러 내의 액션에 제각각 대응하고 있습니다. 다음 리스트는 scaffold로 생성된 컨트롤러의 기본형태입니다. `class UsersController < ApplicationController`  라고 하는 표현은 Ruby의 클래스 상속 문법입니다. (상속에 대해서는 2.3.4, 4.4에서 설명합니다.
```ruby
class UsersController < ApplicationController
  .
  .
  .
  def index
    .
    .
    .
  end

  def show
    .
    .
    .
  end

  def new
    .
    .
    .
  end

  def edit
    .
    .
    .
  end

  def create
    .
    .
    .
  end

  def update
    .
    .
    .
  end

  def destroy
    .
    .
    .
  end
end
```

페이지의 숫자보다 액션의 수가 더 많은 것을 눈치채셨나요? `index`, `show`, `new`, `edit`  액션은 2.2.1에서 다루는 페이지에 해당합니다만, 그 외에도 `create`, `update`, `destroy`  액션이 있습니다. 보통 이 액션들은 페이지를 출력하지 않고 데이터베이스 상의 유저정보를 핸들링합니다. (물론 페이지를 출력하려하면 할 수 있습니다.)

아래의 표는 Rails에서 REST아키텍처 (컬럼2.2)를 구성하는 모든 액션의 리스트입니다. *REST*는, 컴퓨터과학자  [Roy Fielding](http://en.wikipedia.org/wiki/Roy_Fielding) 에 의해 제창된 *REpresentational State Transfer* 라고 하는 개념의 줄임말입니다. 아래 표의 URL중 몇개는 중복되어 있다는 것을 확인해주세요. 예를 들어 `show`과 `update`액션은 어느쪽이던 /user/1 이라고 하는 URL이 적혀있습니다. 이러한 액션들간의 차이는, 이것들의 액션에 대응하는  [HTTP request메소드](http://en.wikipedia.org/wiki/HTTP_request#Request_methods) 의 차이도 포함됩니다. HTTP request 메소드는 3.3에서 설명하겠습니다.

HTTPリクエスト	URL				アクション	用途
GET	/			users			index		すべてのユーザーを一覧するページ
GET				/users/1			show		id=1のユーザーを表示するページ
GET				/users/new		new			新規ユーザーを作成するページ
POST			/users			create		ユーザーを作成するアクション
GET				/users/1/edit		edit			id=1のユーザーを編集するページ
PATCH			/users/1			update		id=1のユーザーを更新するアクション
DELETE			/users/1			destroy		id=1のユーザーを削除するアクション

###### 컬럼 2.2 REpresentational State Transfer (REST)
> Rails관련 책들을 읽다보면, “REST” 라고 하는 용어를 자주 볼 수 있을 것입니다. 이것은 Representational State Transfer의 약자입니다. REST는 인터넷이나 Web 어플리케이션 등의, 분산/네트워크화된 시스템이나 어플리케이션을 구축하기 위한 아키텍쳐의 스타일 중 하나입니다. REST이론 그 자체는 꽤나 추성적입니다만, Rails 어플리케이션에서의 REST는 어플리케이션을 만들 때 컴포넌트(유저나 마이크로포스트)를 “리소스” 로 모델링하는 것을 칭합니다. 이 리소스는  [관계형 데이터베이스의 등록, 조회, 편집, 삭제(Create/Read/Update/Delete: CRUD) 조작](http://ja.wikipedia.org/wiki/CRUD) 과, 4개의 기본적인  [HTTP request메소드](http://ja.wikipedia.org/wiki/Hypertext_Transfer_Protocol%23.E3.83.A1.E3.82.BD.E3.83.83.E3.83.89) (Post/Get/Patch/Delete)에 대응합니다. (HTTP request메소드에 대해서는 3.3 혹은 컬럼 3.2에서 설명하겠습니다.)  
> Rails 개발자에게는 RESTful 스타일을 구현하는 것으로 작성해야만 하는 컨트롤러나 액션의 결정이 많이 편해집니다. 등록(C), 조회(R),  편집(U), 삭제(D)를 실행하는 리소스만으로도 어플리케이션 전체를 작성할 수도 있습니다. 유저나 마이크로포스트 등에 대해서는 자연스럽게 리소스화할 수 있습니다. 제 14장에서 “유저를 팔로우해보자” 라고 하는 꽤나 복잡한 내용에 대해 REST이론을 이용한 자연스럽고 효율적인 모델링을 해봅니다.  

Users컨트롤러와 User모델의 관계를 좀 더 알아보기위해, 아래의 리스트에서 `index` 액션을 정리해보았습니다. (전부 이해하진 못하더라도, 코드를 읽어보는 방법에 대해 배우는 것은 매우 중요합니다.)
```ruby
class UsersController < ApplicationController
  .
  .
  .
  def index
    @users = User.all
  end
  .
  .
  .
end
```

 `index` 액션에는 `@users = User.all`  이라고 하는 라인이 있습니다. (2.2.2의 그림에서 3번에 해당)  이로 인해 User모델로부터 모든 유저의 정보를 얻어 `@users` 라고하는 변수에 저장합니다(5번). User모델의 내용은 아래와 같습니다. 매우 놀랍게도 간단한 내용입니다만 상속(2.3.4 및 4.4) 에 의해 많은 기능을 갖추고 있습니다. 특히 *Active Record* 라고 하는 Ruby 라이브러리 덕분에 아래의 User모델은 `User.all` 이라고 하는 리퀘스트에 대해 DB의 모든 유저를 조회할 수 있습니다.

```ruby
class User < ApplicationRecord
end
```
`@users` 변수에 유저 리스트가 저장되면, 컨트롤러는 아래의 뷰를 호출합니다 (6번) `@`마크로 시작하는 변수는 Ruby에서는 *인스턴스 변수* 로 불리며, Rails의 컨트롤러 내부에서 선언한 인스턴스 변수는 뷰에서도 사용할 수 있습니다. 이 경우에는 아래 리스트의 `index.html.erb` 뷰는 `@user`의 리스트를 1줄씩 HTML의 1개의 라인으로 출력합니다. (현재는 이 코드가 무슨 뜻인지 잘 몰라도 괜찮습니다. 어디까지나 설명을 위한 것입니다.)
```html
<h1>Listing users</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th colspan=“3”></th>
    </tr>
  </thead>

<% @users.each do |user| %>
  <tr>
    <td><%= user.name %></td>
    <td><%= user.email %></td>
    <td><%= link_to ‘Show’, user %></td>
    <td><%= link_to ‘Edit’, edit_user_path(user) %></td>
    <td><%= link_to ‘Destroy’, user, method: :delete,
                                     data: { confirm: ‘Are you sure?’ } %></td>
  </tr>
<% end %>
</table>

<br>

<%= link_to ‘New User’, new_user_path %>
```
뷰는 그 내용을 HTML로 변환하여 (7번), 컨트롤러가 브라우저에게 HTML을 송신하여 브라우저에서 HTML 이 출력되도록 합니다.(8번)

##### 연습
1. 2.2.2의 그림을 참고하여 /users/1/edit  라고 하는 URL에 접근 하려고 할 때의 동작을 서술해보세요.
2. 그림을 확인해가며, scaffold에서 생성된 코드 중에 데이터베이스에서 유저 정보를 얻는 코드를 찾아보세요.
3. 유저 정보를 편집하는 페이지의 파일명은 무엇입니까?

### 2.2.3 User 리소스의 결함
scaffold에서 작성된 User리소스는, Rails의 개념을 매우 간단하게 설명하기에는 좋습니다만, 다음과 같은 문제점들이 있습니다. 
	- 데이터의 검증이 이루어지지 않는다.
		- scaffold의 기본 상태로는 유저이름이 빈칸이거나 대충 만든 이메일을 입력해도 데이터가 등록됩니다.
	- 유저 인증이 이루어지지 않는다.
		- 로그인, 로그아웃이 이뤄지지 않기 때문에 누구던지 무제한으로 조작할 수 있습니다.
	- 테스트 처리가 없다.
		- 엄밀히 말하면, 이것은 제대로된 표현은 아닙니다. 애당초 scaffold로 작성된 코드에는 매우 간단한 테스트코드가 포함되어 있습니다. 단, scaffold의 테스트 코드는 데이터 검증이나 유저인증, 그 외 필수적인 테스트 요구사항이 구현되어있지 않습니다.
	- 레이아웃이나 스타일이 제대로 되어있지 않다.
		- 사이트디자인이나 조작방법이 일반적이진 않습니다.
	- 이해하기 어렵다.
		- scaffold의 코드를 이해할 정도면, 애당초 본 튜토리얼을 읽을 필요도 없을 것입니다.

## 2.3 Microposts 리소스
User 리소스를 생성하여 내용을 확인해보았습니다. 이번에는 Microposts리소스에 대해 똑같이 내용을 확인해보겠습니다. 또한 이번 섹션 전체에 대해 Microposts 리소스를 이해할 때에는, 2.2의 user요소와 비교해가며 섹션을 진행하는 것을 권해드립니다. 실제로 이 2개의 리소스는 여러 많은 곳에서 닮아있습니다. Rails의 RESTful구조를 이해하기 위해선, 반복적인 학습이 제일 좋습니다. User리소스와 Microposts리소스의 구조의 닮은점을 이해하는 것이, 이번 섹션의 주된 내용입니다.

### 2.3.1 Microposts 화면을 움직여보자
User리소스와 마찬가지로, Microposts 리소스도 scaffold로 코드를 작성해봅시다. `rails generate scaffold` 커맨드를 사용하여 아래 그림처럼 데이터 모델을 작성해봅니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_micropost_model%202.png)
```
$ rails generate scaffold Micropost content:text user_id:integer
      invoke  active_record
      create    db/migrate/20160515211229_create_microposts.rb
      create    app/models/micropost.rb
      invoke    test_unit
      create      test/models/micropost_test.rb
      create      test/fixtures/microposts.yml
      invoke  resource_route
       route  resources :microposts
      invoke  scaffold_controller
      create    app/controllers/microposts_controller.rb
      invoke    erb
      create      app/views/microposts
      create      app/views/microposts/index.html.erb
      create      app/views/microposts/edit.html.erb
      create      app/views/microposts/show.html.erb
      create      app/views/microposts/new.html.erb
      create      app/views/microposts/_form.html.erb
      invoke    test_unit
      create      test/controllers/microposts_controller_test.rb
      invoke    helper

      create      app/helpers/microposts_helper.rb
      invoke      test_unit
      invoke    jbuilder
      create      app/views/microposts/index.json.jbuilder
      create      app/views/microposts/show.json.jbuilder
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/microposts.coffee
      invoke    scss
      create      app/assets/stylesheets/microposts.scss
      invoke  scss
   identical    app/assets/stylesheets/scaffolds.scss
```

(Spring에 관련된 에러가 발생한다면, 같은 코드를 한 번 더 입력해보도록 합시다.)
새로운 데이터모델로 데이터베이스를 업데이트하기 위해서는, 2.2와 같이 마이그레이션을 실행합니다.
```
$ rails db:migrate
==  CreateMicroposts: migrating =============================
— create_table(:microposts)
   -> 0.0023s
==  CreateMicroposts: migrated (0.0026s) ======================
```

이 것으로 Microposts를 만들 준비가 끝났습니다. 만드는 방법은 2.2.1과 동일합니다. 이미 눈치채셨을 지도 모르겠습니다만, scaffold 제네레이터에 의하여 Rails의 route파일이 업데이트되었습니다. 내용은 아래처럼, Microposts 리소스용의 라우팅규약이 추가되었습니다. User의 경우와 마찬가지로, `resource :microposts` 이라고 하는 라우팅규약은, microposts용의 URI를  microposts 컨트롤러 내부의 액션에 할당해줍니다.
```ruby
Rails.application.routes.draw do
  resources :microposts
  resources :users
  root ‘users#index’
end
```

HTTPリクエスト		URL					アクション		用途
GET					/microposts			index			すべてのマイクロポストを表示するページ
GET					/microposts/1		show			id=1のマイクロポストを表示するページ
GET					/microposts/new		new				マイクロポストを新規作成するページ
POST				/microposts			create			マイクロポストを新規作成するアクション
GET					/microposts/1/edit	edit				id=1のマイクロポストを編集するページ
PATCH				/microposts/1		update			id=1のマイクロポストを更新するアクション
DELETE				/microposts/1		destroy			id1のマイクロポストを削除する

Microposts의 컨트롤러 자체의 구조는 아래와 같습니다. `UsersController`가 `MicropostsController` 로 바뀌어있을 뿐, 그 외에는 2.2에서 확인한 Users 컨트롤러와 같다는 것에 주목해주세요. 이것은 REST아키텍쳐가 2개의 리소스에 똑같이 적용되어있는 것을 나타내고 있습니다.
```
class MicropostsController < ApplicationController
  .
  .
  .
  def index
    .
    .
    .
  end

  def show
    .
    .
    .
  end

  def new
    .
    .
    .
  end

  def edit
    .
    .
    .
  end

  def create
    .
    .
    .
  end

  def update
    .
    .
    .
  end

  def destroy
    .
    .
    .
  end
end
```

/microposts/new페이지를 브라우저에서 열고, 새로운 마이크로포스트의 정보를 입력하여 마이크로포스트를 몇 개정도 생성해보세요

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_new_micropost_3rd_edition.png)
여기서는 일단 마이크로포스트를 1개나 2개정도 작성하고, 적어도 한쪽의  `user_id`가 `1` 이 되도록하여 2.2.1에서 만든 제일 첫 유저의 id와 일치하도록 합니다. 결과는 아래처럼 될 것 입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_micropost_index_3rd_edition.png)
##### 연습
	1. CSS을 알고 계신 분이라면: 새로운 마이크로포스트를 작성하고 브라우저의 HTML inspector 기능을 사용하여 *Micropost was successfully created* 의 부분을 확인해보세요. 브라우저를 새로고침하면 그 부분은 어떻게 되나요?
	2. 마이크로포스트의 작성화면에서, Content와 User를 빈칸으로 하여 작성한다면 어떠한 결과가 되나요?
	3. 141문자 이상의 문자열을 Content에 입력하였을 때, 마이크로포스트를 작성하려고 하면 어떻게 되나요? (힌트 :  [Wikipedia의 Ruby](https://ja.wikipedia.org/wiki/Ruby)  1단락이 마침 150자정도인데, 어떻게되나요?)
	4. 위 연습으로 작성한 마이크로포스트를 삭제해봅시다.

### 2.3.2 Micropost를 micro하게 해보자
micropost의 *micro* 라는 이름처럼, 무엇인가의 방법으로 문자수제한을 생각해봅시다. Rails에서는 이럴 때, *validation* 을 사용하여 간단하게 제한할 수 있습니다. 예를 들면 *Twitter*처럼 140문자제한을 걸고 싶을때, *Length* 를 쓰면됩니다. 텍스트에디터나 IDE를 사용하여 `app/models/micropost.rb` 를 열고, 아래의 내용으로 수정해봅시다.
```ruby
class Micropost < ApplicationRecord
  validates :content, length: { maximum: 140 }
end
```
위 코드로 정말 움직이는 것인지 의문하시는 분들도 있으실 수도 있겠지만, 정말 제대로 움직입니다. (검증기능에 대해서는 6.2에서 설명합니다.) 141문자 이상의 새로운 마이크로포스트를 작성해보면 알게 됩니다. 아래의 그림과 같이 마이크로포스트의 내용이 너무 길다는 내용의 에러메세지가 Rails에 의해 출력됩니다. (에러 메세지에 대해서는 7.3.3에서 소개합니다.

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/micropost_length_error_3rd_edition.png)

##### 연습
	1. 방금 전 2.3.1.1에서 연습문제를 푼 것 처럼, 다시 한 번 더 Content의 내용을 141문자 이상 입력해봅시다. 어떻게 동작이 바뀌었나요?
	2. CSS을 알고 계신 분들께: 브라우저의 HTML Inspector를 이용하여 표시된 에러메세지가 무엇인지 확인해주세요.

### 2.3.3 유저는 많은 마이크로포스트를 가지고 있다.
여러가지 다른 데이터모델 간의 관계를 만드는 것은, Rails의 강력한 기능입니다. 여기서는 한 명의 유저에 대해 여러개의 마이크로 포스트가 존재한다고 가정합니다. User모델과 Micropost모델을 제각각 아래처럼 수정하는 것으로 관계를 표현할 수 있습니다.
```ruby
class User < ApplicationRecord
  has_many :microposts
end
```

```ruby
class Micropost < ApplicationRecord
  belongs_to :user
  validates :content, length: { maximum: 140 }
end
```

이 데이터모델 간의 관계를 그림으로 나타낸 것이 아래의 그림입니다. `microposts` 테이블에는 `user_id`  컬럼을 선언해놓았기 때문에, Rails와 Active Record가 마이크로포스트와 유저 간의 관계를 생성해주는 것입니다.
![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/micropost_user_association.png)

제 13장과 제 14장에서는 관계가 맺어진 유저와 마이크로포스트를 동시에 표시하여 Twitter와 같은 마이크로포스트의 피드를 작성해볼 계획입니다. 여기선 Rails의 *console* 을 이용하여 유저와 마이크로포스트의 관계를 확인해봅니다..
Rails의 console은 Rails 어플리케이션과 커뮤니케이션을 할 수 있는 일종의 프로그램입니다. 일단 터미널에서 `rails console` 커맨드를 입력합니다. 이어서 `User.first` 라고 입력하여 데이터베이스에서 제일 앞의 유저 정보를 조회하여 `first_user` 변수에 저장합니다.
```
$ rails console
>> first_user = User.first
=> #<User id: 1, name: "Michael Hartl", email: "michael@example.org",
created_at: "2016-05-15 02:01:31", updated_at: "2016-05-15 02:01:31">
>> first_user.microposts
=> [#<Micropost id: 1, content: "First micropost!", user_id: 1, 
created_at: "2011-11-03 02:37:37", updated_at: "2011-11-03 02:37:37">,
"2016-05-15 02:37:37", updated_at: "2016-05-15 02:37:37">,
#<Micropost id: 2, content: "Second micropost", user_id: 1,
created_at: "2016-05-15 02:38:54", updated_at: "2016-05-15 02:38:54">]
>> micropost = first_user.microposts.first
=> #<Micropost id: 1, content: "First micropost!", user_id: 1, created_at:
"2016-05-15 02:37:37", updated_at: "2016-05-15 02:37:37">
>> micropost.user
=> #<User id: 1, name: "Michael Hartl", email: "michael@example.org",
created_at: "2016-05-15 02:01:31", updated_at: "2016-05-15 02:01:31">
>> exit
```

(마지막 줄처럼 `exit` 를 입력하면, 실행하고 있는 rails console을 종료할 수 있습니다. 많은 시스템에서는 Ctrl+d키를 눌러 종료하기도 합니다.)
`first_user.microposts`  라고 하는 코드를 실행하면, 그 유저에 관련된 마이크로포스트에 액세스할 수 있습니다. 이 때 Active Record는 `user_id`가 `first_user` 의 id (여기서는 1) 과 같은 마이크로포스트를 자동적으로 조회합니다.  Active Record의 관계맺기 기능에서는 제 13장과 제 14장에서 상세히 설명합니다.

##### 연습
	1. 유저의 show페이지를 수정하여 유저의 제일 첫 마이크로포스트를 표시하도록 해봅시다. 같은 파일 안에서 다른 코드로부터 문법을 추측해보세요. 제대로 되었는지 /users/1에 접속하여 확인해보세요.
	2. `validates :content, length: { maximum: 140 }` 이 코드는 마이크로포스트의 Content가 존재하는지의 여부를 검증하는 validation입니다. 마이크로포스트가 아무것도 들어 있지 않을 때 검증되는지 실제로 확인해봅시다. (아래의 첫번째 캡쳐와 같이 된다면, 성공입니다.)
	3. 아래에 `FILL_IN` 이라고 하는 부분을 수정하여, User모델의 name과 email이 존재하는 것을 검증해봅시다.
```ruby
class Micropost < ApplicationRecord
  belongs_to :user
  validates :content, length: { maximum: 140 },
                      presence: true
end
```

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/micropost_content_cant_be_blank.png)
```ruby
class User < ApplicationRecord
  has_many :microposts
  validates FILL_IN, presence: true    # 「FILL_IN」をコードに置き換えてください
  validates FILL_IN, presence: true    # 「FILL_IN」をコードに置き換えてください
end
```

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/user_presence_validations.png)

### 2.3.4 상속의 계층
마지막으로, Toy 어플리케이션에서 사용하고 있는 Rails의 컨트롤러와 모델의 클래스 계층에 대해 간단히 설명하겠습니다. 이번 섹션을 이해하기 위해서는, 조금이나마 오브젝트지향프로그래밍 (OOP) 의 경험이 필요합니다. (특히 *class* 에 대해 이해하고 있을 필요가 있습니다.) “아직 클래스를 몰라요” 라는 분들도 걱정하진 마세요. 4.4에서 클래스의 개념에 대해 설명해드리겠습니다. 여기서는 설명이 이해되지 않아도 큰 문제는 없습니다.

일단 모델의 상속구조에 대해 설명하겠습니다. 아래의 리스트를 비교해보면, User모델과 Micropost모델은 둘다 `ApplicationRecord` 라고 하는 클래스를 상속받고 있습니다. (Ruby에서는 상속관계를 `<` 로 표기합니다.) 또한 `ApplicationRecord`  클래스는 Active Record가 제공하는 기본 클래스인 `ActiveRecord::Base` 를 상속하고 있습니다. 아래의 그림에서는 이 클래스들의 관계를 정리하고 있습니다. `ActiveRecord::Base`  라고 하는 기본 클래스를 상속받는 것으로 인해, 작성한 모델 오브젝트는 데이터베이스에 접근할 수 있게 되고, 데이터베이스의  컬럼을 Ruby 속성처럼 다룰 수 있습니다.
```ruby
class User < ApplicationRecord
  .
  .
  .
end
```

```ruby
class Micropost < ApplicationRecord
  .
  .
  .
end
```

 컨트롤러의 상속구조도, 모델의 상속구조도 본질적으로는 같습니다. 아래의 리스트를 비교하면 Users컨트롤러와 Microposts컨트롤러도 둘다 AplicationController를 상속받고 있습니다. 또한 `ApplicationController` 또한 `ActionController::Base` 라고 하는 클래스를 상속받는 것을 알 수 있습니다. 이 클래스는 Rails의 Action Pack 이라고 하는 라이브러리가 제공하는 컨트롤러의 기본 클래스 입니다.
```ruby
class UsersController < ApplicationController
  .
  .
  .
end
```

```ruby
class MicropostsController < ApplicationController
  .
  .
  .
end
```

```ruby
class ApplicationController < ActionController::Base
  .
  .
  .
end
```

![](%EC%A0%9C%202%EC%9E%A5%20Toy%20Application/demo_controller_inheritance.png)

모델의 상속관계와 마찬가지로, User컨트롤러와 Microposts컨트롤러는 결국에는 `ActionController::Base` 라고 하는 기본 클래스를 상속받고 있습니다. 그 때문에 어떤 컨트롤러라도 오브젝트의 조종이나 HTTP request의 필터링, 뷰를 HTML로 변환하여 출력하는 등의 다채로운 기능을 실행할 수 있습니다. Rails 컨트롤러는 반드시 `ApplicationController` 를 상속하고 있기 때문에, Application 컨트롤러에서 정의한 규칙은 어플리케이션의 모든 액션에 반영됩니다. 예를 들어 9.1에서는 로그인과 로그아웃용의 헬퍼 메소드를 Sample 어플리케이션의 모든 컨트롤러에서 사용할 수 있게 하고 있습니다.

##### 연습
	1. Application컨트롤러 파일을 열고 `ApplicationController` 가 `ActionController::Base` 를 상속하고 있는 부분의 코드를 찾아보세요.
	2. `ApplicationRecord` 가 `ActiveRecord::Base` 를 상속하는 코드는 어디에 있을까요? 찾아보세요. (힌트: 컨트롤러와 본질적으로 같은 구조이기 때문에 `app/models` 디렉토리안에 있는 파일을 찾아본다면…?)

### 2.3.5 어플리케이션을 배포해보자.
micro posts 리소스의 설명이 끝났습니다. 여기서 레포지토리를 Bitbucket에 등록해봅시다.
```git
$ git status
$ git add -A
$ git commit -m “Finish toy app”
$ git push
```

Git 커밋은 되도록이면 자주 해주세요. 편집한 이력이 많이 쌓이지 않는 것이 바람직하나 본 장에서는 끝맺음의 뜻으로 변경이력이 제일 큰 커밋을 한 번 진행해주면, 문제는 없을 것입니다.

여기서 Toy 어플리케이션을 1.5와 같이 Heroku에 배포해주세요.
`git push heroku`
(위 커맨드는, 2.1에서 Heroku 어플리케이션을 만들었다는 것을 전제로 합니다. 어플리케이션을 만들지 않았다면, 먼저 `heroku create`, `git push heroku master` 를 실행한 후에 위 커맨드를 진행하세요.)

어플리케이션의 데이터베이스를 움직이게 하기 위해서는, 다음의 `heroku run` 커맨드를 실행하여 실제 배포환경의 데이터베이스의 마이그레이션을 할 필요가 있습니다.
`heroku run rails db:migrate`
이 커맨드를 실행해보면, 아까 전 정의한 유저와 마이크로포스트의 데이터모델을 사용하여 Heroku의 데이터베이스가 업데이트됩니다. 마이그레이션이 완료되면,  Toy 어플리케이션을 PostgreSQL 데이터베이스를 사용하는 실제 배포환경에서 이용할 수 있을 것입니다.

마지막으로 혹시 2.3.3.1의 연습문제를 했다면, *제일 처음의 유저의 마이크로포스트를 표시한다* 라는 코드를 삭제할 필요가 있으니 주의하세요. 삭제하는 걸 까먹은 경우라도, 당황하지말고 해당하는 코드를 삭제하고 다시 한 번 더 커밋 한 후에, Heroku에 push해주세요.

##### 연습
	1. 실제 배포환경에서 2~3명의 유저를 작성해봅시다.
	2. 실제 배포환경에서 제일 첫 번째 유저의 마이크로포스트를 작성해봅시다.
	3. 마이크로포스트의 Content에 141 문자 이상을 입력한 상태로 작성해봅시다. validation이 실제 배포환경에서도 제대로 동작하는지를 확인해봅시다.

## 2.4 마지막으로
매우 간단했습니다만, Rails 어플리케이션을 제대로 완성해보았습니다. 이번 장에서 작성한 Toy 어플리케이션에 대해선 좋은 부분도 많습니다만 여러가지 안좋은 점도 있습니다.

좋은점
	- Rails의 전체를 매우 높은 레벨에서 둘러보았다.
	- MVC 모델을 확인해보았다.
	- REST 아키텍처에 대해 알 수 있게 되었다.
	- 데이터 모델을 작성해보았다.
	- 데이터베이스를 가진 실제 배포환경의  Web 어플리케이션을 움직여보았다.

안좋은 점
	- 레이아웃과 스타일이 하나도 설정되어 있지 않았다.
	- “Home”이나 “About” 과 같은 정석의 정적 페이지가 없다.
	- 유저가 패스워드 설정을 할 수 없다.
	- 유저가 영상이나 이미지를 올릴 수 없다.
	- 로그인 기능이 없다.
	- 보안 기능이 없다.
	- 유저와 마이크로포스트의 자동 관계짓기가 이루어지지 않았다.
	- Twitter와 같은 팔로우 기능이나 팔로잉 기능이 없다.
	- 마이크로포스트를 피드할 수 없다.(댓글을 달 수 없다)
	- 제대로 된 테스트를 하지 않았다.
	- **이해하기 어렵다.**

본 튜토리얼에서는 이후, Toy 어플리케이션의 좋은 점을 유지해가며 안좋은 점을 하나씩 극복해나갈 것입니다.

### 2.4.1 2장의 정리
	- Scaffold 기능에서 코드를 자동생성하면 Web의 어지간한 부분부터 모델데이터에 접근하여 조작까지 할 수 있다.
	- Scaffold 는 무엇보다도 간단하고 손쉽지만 이것을 바탕으로 Rails를 이해하기에는 적절하지 않다.
	- Rails에서는 Web 어플리케이션의 구성에 MVC (Model - View - Controller) 라고 하는 모델을 채용하고 있다.
	- Rails가 해석하는 REST에는 표준적인 URL 세트와 데이터모델을 조작하기 위한 컨트롤러액션이 포함되어 있다.
	- Rails는 데이터의 Validation(유효성)  검사 기능을 제공하고 있으며 데이터모델의 속성값을 제한하는 것이 가능하다.
	- Rails에서는 여러가지 데이터 모델간의 관계를 정의할 수 있으며, 그를 위한 많은 함수가 준비되어 있다.
	- Rails 콘솔을 사용하면, 커맨드 라인을 이용하여 Rails 어플리케이션을 조작할 수 있다.

 
#Ruby On Rails/Ruby on Rails Tutorial PJ#