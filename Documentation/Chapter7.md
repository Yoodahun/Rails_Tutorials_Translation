# 제 7장 유저를 생성해보자

User모델이 완성되었습니다. 드디어 유저 생성(등록) 기능을 추가해봅시다. 7.2에서는 HTML입력폼을 사용하여 Web어플리케이션에 등록정보를 송신해봅니다. 이어서 7.4에서는 유저를 신규작성하고, 그 정보를 데이터베이스에 저장합니다. 유저 생성의 마지막은 생성된 유저의 신규 프로필을 표시할 수 있게 하기 위해, 유저를 표시하기위한 페이지를 작성하고, 유저용의 REST 아키텍처를 구현해볼 것입니다. ([2.2.2](Chapter2.md#222-MVC의-처리)) 더불어서 [5.3.4](Chapter5.md#534-링크의-테스트) 에서 구현한, 간단하면서도 매우 뛰어난 통합 테스트 코드에 대해 몇가지 케이스를 추가해볼 것입니다.



이번 챕터에서는 [제 6장](Chapter6.md) 에서 작성한 User모델의 검증 테스트를 믿고, 유효한 메일주소를 가지고 있는 (가능성이 있는) 신규 유저를 추가해볼 것입니다. 제 11장에서는 메일주소가 *정말로* 유효한 것인지 확인해보기 위해, *고객 ID를 유효화하는* 기능을 신규 유저 등록 시에 동작할 수 있도록 추가해보겠습니다.



본 튜토리얼은 프로레벨의 내용을 다루면서도, 되도록이면 간단하게 설명할 수 있도록 노력하고 있습니다만, 그럼에도 불구하고 Web개발이라고 하는 것은 복잡하면서 어려운 주제임에는 틀림없습니다. 실제로 이번 [7장](Chapter7.md) 부터는 난이도가 조금씩 올라갑니다. 그렇기 때문에 이번 챕터부터는 지금까지 공부해왔던 것 그 이상으로 시간을 더욱 투자해서 천천히 읽어보세요. 필요하다면 복습도 하시길 바랍니다. (일부 독자들은 각 챕터를 최소 2번이상 읽고 있을텐데, 그것도 좋은 방법이라고 생각합니다.) 혹은, 만일 Rails 튜토리얼을 진행해나가는 중에 기초지식이 모자라다고 느낄 때에는 다음의 자료들을 읽어보시는 것도 도움이 되실 겁니다.

- [Learn Enough Society](http://learnenough.com/story) 구독
- [*Learn Enough Ruby to Be Dangerous*](http://learnenough.com/ruby-tutorial)
- [*Learn Enough Sinatra to Be Dangerous*](http://learnenough.com/sinatra-tutorial)
- [*Learn Enough Rails to Be Dangerous*](http://learnenough.com/ruby-on-rails-tutorial)



## 7.1 유저를 표시해보자

이번 섹션에서는, 일단 유저의 이름과 프로필 사진을 표시하기 위한 페이지를 작성해볼 것입니다. 목업의 이미지는 아래 첫 번째 이미지와 같습니다. 유저 프로필 페이지의 최종적인 목표는 아래 두 번째 페이지와 같은 유저의 프로필 사진과 기본 유저 데이터, 마이크로포스트의 리스트를 표시하는 것입니다. (두 번쨰 이미지에서는, 유명한 lorem ipsum 더미 텍스트를 사용하고 있습니다. 이 텍스트의 탄생과정에는 [재미있는 에피소드](http://www.straightdope.com/columns/read/2290/what-does-the-filler-text-lorem-ipsum-mean) 가 있으니 시간이 괜찮으시면 읽어보세요.) 이 페이지를 작성한 후, 제 14장의 샘플 어플리케이션에서 본격적으로 사용해볼 것입니다.



버전관리를 하고 있으니, 언제나처럼 토픽 브랜치를 생성해봅시다.

`$ git checkout -b sign-up`

![](../image/Chapter7/profile_mockup_profile_name_bootstrap.png)



![](../image/Chapter7/profile_mockup_bootstrap.png)



### 7.1.1 Debug와 Rails 환경

이번 섹션에서 생성하는 프로필은, 우리의 어플리케이션에서의 첫 동적인 페이지가 될 것입니다. 뷰 그 자체는 한 페이지의 코드입니다만, 어플리케이션의 데이터베이스로부터 얻은 정보를 사용하여 각 프로필의 표시를 커스터마이즈할 것입니다. Sample 어플리케이션에 동적인 페이지를 추가하는 준비작업으로써, 여기서는 Web사이트의 레이아웃에 디버그 정보를 추가해보도록 하겠습니다. 이것으로, Rails에서 기본으로 제공하는 `debug` 메소드와 `params` 변수를 사용하여, 각 프로필 페이지의 디버그용의 정보를 표시할 수 있게 되었습니다. (자세한 설명은 7.1.2에서 설명하겠습니다.)

```erb
<!-- app/views/layouts/application.html.erb -->

<!DOCTYPE html>
<html>
  .
  .
  .
  <body>
    <%= render 'layouts/header' %>
    <div class="container">
      <%= yield %>
      <%= render 'layouts/footer' %>
      <%= debug(params) if Rails.env.development? %> <!-- 추가한 코드 -->
    </div>
  </body>
</html>
```

실제 배포 환경에서 전개한 어플리케이션은 디버그 정보를 표시하게 하고싶진 않습니다. 그렇기 때문에 위 코드에서는 아래와 같은 표현을 하고 있습니다.

`if Rails.env.development?`

이렇게 해놓으면, 디버그정보는 Rails의 세 가지의 디폴트 환경 중, *개발환경(Development)* 에서만 표시될 것입니다.(컬럼 7.1) 특히 `Rails.env.development?` 가 `true` 일 경우는 개발 환경일 때만 이기때문에 다음의 Ruby 코드는

`<%= debug(params) if Rails.env.development? %>`

실제 배포환경 어플리케이션이나 테스트에서 사용할 수는 없습니다. ( 테스트 환경에서 디버그 정보가 표시되어도 문제될 것은 없습니다만, 그렇다고 좋을 것도 없습니다. 디버그 정보는 개발환경 이외에서는 표시해선 안되는 것입니다.)

###### 컬럼 7.1 Rails의 3가지 환경

> Rails에서는 테스트환경(test), 개발환경(development), 실제 배포환경(production) 의 세 가지 환경이 기본으로 준비되어있습니다. Rails console의 기본 환경은 development입니다.

>

> $ rails console
>   Loading development environment

> ​	Rails.env
> ​	  => "development"
> ​	Rails.env.development?
> ​	  => true
> ​	Rails.env.test?
> ​	  => false

>

> 위와 같이, Rails에서는 `Rails` 라고 하는 오브젝트가 존재하며, 해당 오브젝트의 환경의 논리값을 다루는 `env` 라는 속성이 있습니다. 예를 들어 `Rails.env.test?` 라고 하면 테스트 환경에서는 `true` 를 리턴하고, 그 외의 환경에서는 `false` 를 리턴합니다.

>

> 테스트 환경에서의 디버그 등, 다른 환경에서 console을 실행하는 필요가 생겼을 때는 환경을 파라미터로써 console 스크립트에 직접 입력할 수 있습니다.

>

> $ rails console test
>   Loading test environment

> ​	Rails.env
> ​	  => "test"
> ​	Rails.env.test?
> ​	  => true

>

> 마찬가지로, Rails 서버도 기본 옵션으로는 `development` 가 사용되고 있습니다만, 다음과 같은 다른 환경을 명시적으로 실행하는 것도 가능합니다.

>

> `$ rails server --environment production`

>

> 어플리케이션을 실제 배포환경에서 실행하는 경우, 배포환경의 데이터베이스를 이용하지 않으면 어플리케이션을 실행할 수 없습니다. 때문에 `rails db:migrate` 를 배포환경에서 실행하여 배포환경 데이터베이스를 생성합니다.

>

> `$ rails db:migrate RAILS_ENV=production`

>

> (console, server, migrate의 세 가지 커맨드는, 기본이외의 환경을 지정하는 방법이 제각각 다르기 때문에 혼동스러울 수 있습니다. 때문에 세 가지의 경우의 모든 방법을 칼럼에서 설명했습니다. 또한 `RAILS_ENV=<env>` 를 커맨드의 앞 부분에 놓아도 상관 없습니다. 예를 들면, `RAILS_ENV=production rails server` 등의 문장을 입력해도 됩니다.)

>

> Sample 어플리케이션을 기존에 Heroku 상에 배포(디플로이) 한 경우에는, `heroku run rails console` 이라고 하는 커맨드를 입력하여 실제 배포환경을 확인해볼 수 있습니다.

>  $ heroku run rails console

> ​	Rails.env
> ​	  => "production"
> ​	Rails.env.production?
> ​	  => true

>

> 당연한 이야기지만, Heroku는 실제 배포용 플랫폼이기 때문에, 실행되는 어플리케이션은 모두 실제 배포환경입니다.

디버그출력을 예쁘게하기 위해, [제 5장](Chapter5.md) 에서 생성한 커스텀 스타일 시트를 아래와 같이 추가합니다.

```scss
/* app/assets/stylesheets/custom.scss */

@import "bootstrap-sprockets";
@import "bootstrap";

/* mixins, variables, etc. */

$gray-medium-light: #eaeaea;

@mixin box_sizing {
  -moz-box-sizing:    border-box;
  -webkit-box-sizing: border-box;
  box-sizing:         border-box;
}
.
.
.
/* miscellaneous */
.debug_dump {
  clear: both;
  float: left;
  width: 100%;
  margin-top: 45px;
  @include box_sizing;
}
```

여기서 Sass의 Mix_in 기능 (여기서는 `box_sizing` )을 사용하고 있습니다. mix_in기능을 사용함으로써 CSS의 그룹을 패키지화하여 여러개의 요소에 적용할 수 있습니다. 다음의 코드는

```scss
.debug_dump {
  .
  .
  .
  @include box_sizing;
}
```

아래와 같이 변환됩니다.

```scss
.debug_dump {
  .
  .
  .
  -moz-box-sizing:    border-box;
  -webkit-box-sizing: border-box;
  box-sizing:         border-box;
}
```

mix_in은 7.2.1에서도 사용합니다. 이번 경우에는 아래의 그림과 같이 될 것 입니다.

![](../image/Chapter7/home_page_with_debug_3rd_edition.png)

위 그림에서 디버그 출력은, 화면에 표시되는 페이지의 상태를 파악하기 위해 도움이 될 정보를 가지고 있습니다.

```
controller: static_pages
action: home
```

이것은  `params` 가 가지고 있는 내용으로써, `YAML` 이라고 하는 형식으로 작성되어져 있습니다. YAML은 기본적으로 해시의 형태이며 컨트롤러와 페이지의 액션을 유니크하게 지정합니다. 7.1.2에서는 다른 형식의 예시도 소개하겠습니다.

##### 연습

1. 브라우저로부터 /about에 접속하여 디버그정보가 표시되는 것을 확인해주세요. 해당 페이지를 표시할 때, 어떠한 컨트롤러와 액션이 사용되었습니까? `params` 의 내용으로부터 확인해보세요.
2. Rails의 콘솔을 동작시키고, 데이터베이스로부터 제일 처음의 유저정보를 조회하여 변수 `user`에 저장해보세요. 그 후, `puts user.attributes.to_yaml`을 실행하면 어떠한 내용이 표시됩니다. 여기서 표시된 결과와, `y` 메소드를 사용한 `y user.attributes`의 실행결과를 비교해봅시다.



### 7.1.2 User Resource

유저 프로필 페이지를 생성하기 위해서는 그 전에, 데이터베이스에 유저를 등록해놓을 필요가 있습니다. 이것은 이른바 "달걀이 먼저냐 닭이 먼저냐" 의 문제입니다. 이 Web사이트에서는 등록페이지가 없는 상태에서 어떻게하면 유저를 생성할 수 있을까요? 다행히도 이 문제는 이미 해결한 적이 있습니다. [6.3.4](Chapter6.md#유저의-생성과-인증) 에서 Rails 콘솔을 사용하여 유저 레코드를 등록해놓았습니다. 따라서 데이터베이스 안에는 한 명의 유저가 존재할 것 입니다.

```ruby
$ rails console
>> User.count
=> 1
>> User.first
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com",created_at: "2016-05-23 20:36:46", updated_at: "2016-05-23 20:36:46",password_digest: "$2a$10$xxucoRlMp06RLJSfWpZ8hO8Dt9AZX...">
```

(만약, 아직 데이터베이스에 유저가 한 명이라도 없는 경우에는, [6.3.4](Chapter6.md#유저의-생성과-인증) 으로 돌아가 작성해주세요.) 방금전 콘솔의 출력결과롸부터, 유저의 ID가 `1` 이라는 것을 확인하였습니다. 그 다음 목표로는 이러한 유저 정보를 Web 어플리케이션 상에서 표시하는 것입니다. 여기서는 Rails 어플리케이션에서 권장되고 있는 REST 아키텍쳐 ([컬럼 2.2](Chapter2.md#컬럼-22-REpresentational-State-Transfer-REST) )의 습관을 따르도록 합니다. 즉, 데이터의 생성, 표시, 수정, 삭제를 *리소스 (Resource)* 로써 다룬다는 것입니다. [HTTP표준](https://ja.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 에서는 위 리소스에 대응하기 위한 4가지의 기본 조작 (POST, GET, PATCH, DELETE) 가 정의되어 있습니다. 이러한 기본 조작들을 각 액션에 할당하여 사용해봅니다.([컬럼 3.2](Chapter3.md#컬럼-32-GET이나-그-외-다른-HTTP-메소드에-대해)) 



REST의 원칙을 따르는 경우, 리소스로의 참조는 리소스의 이름과 유니크한 ID를 사용하는 것이 일반적입니다. 유저를 리소스라고 가정할 경우, id=`1` 의 유저를 참조한다는 것은 /users/1 이라고 하는 URL에 대해 GET 리퀘스트를 발행한다는 의미입니다. 여기서 `show` 라고 하는 액션은, *암묵* 의 리퀘스트입니다. Rails의 REST 기능이 유효한 경우라면, GET 리퀘스트는 자동적으로 `show` 액션으로써 다뤄질 것입니다



[2.2.1](Chapter2.md#221-Users-화면을-움직여보자) 에서 설명한 것 처럼, id=`1` 의 dㅠ저에 접근하기 위한 페이지의 URL은 /users/1 입니다. 단 현 시점에서는 이 URL을 사용해도 에러가 출력될 것입니다. 

![](../image/Chapter7/profile_routing_error_4th_edition.png)

/users/1의 URL을 유효하게 하기 위해, routes파일 (`config/routes.rb`) 에 다음 한 줄을 추가합니다.

`resources :users`

작성한 코드는 아래와 같습니다.

```ruby
# config/routes.rb

Rails.application.routes.draw do
  root 'static_pages#home'
  get  '/help',    to: 'static_pages#help'
  get  '/about',   to: 'static_pages#about'
  get  '/contact', to: 'static_pages#contact'
  get  '/signup',  to: 'users#new'
  resources :users
end
```

지금 작성하는 간단하고 학습용도로의 Web페이지 상에서의 유저를 표시하는 것이긴 하지만, `resources :users` 라고 하는 줄은, 유저정보를 표시하는 URL(/users/1) 를 추가하기 위한 것만은 아닙니다. Sample 어플리케이션에 이 한 줄을 추가하는 것으로, 유저의 URL 을 생성할 때에 여러개의 이름이 붙어있는 루트 ([5.3.3](Chapter5.md#533-이름이-붙은-Path)) 와 더불어 RESTful적인 Users리소스에서 필요한 모든 액션을 이용할 수 있게 됩니다. 이 코드 한 줄에 대응하는 URL 이나 액션, 이름이 붙은 루트는 아래의 표와 같습니다. 다음 3개의 챕터에 걸쳐 아래 표의 다른 항목도 이용하여 User리소스를 완전한 RESTful한 리소스로 만들기 위해 필요한 액션을 모두 작성해볼 것입니다.

| HTTP 리퀘스트 | **URL**       | **액션**  | **Named Root**         | **용도**                                    |
| ------------- | ------------- | --------- | ---------------------- | ------------------------------------------- |
| `GET`         | /users        | `index`   | `users_path`           | すべてのユーザーを一覧するページ            |
| `GET`         | /users/1      | `show`    | `user_path(user)`      | 特定のユーザーを表示するページ              |
| `GET`         | /users/new    | `new`     | `new_user_path`        | ユーザーを新規作成するページ (ユーザー登録) |
| `POST`        | /users        | `create`  | `users_path`           | ユーザーを作成するアクション                |
| `GET`         | /users/1/edit | `edit`    | `edit_user_path(user)` | `id= 1`의 유저를 수정하는 페이지            |
| `PATCH`       | /users/1      | `update`  | `user_path(user)`      | 특정 유저를 수정하는 액션                   |
| `DELETE`      | /users/1      | `destroy` | `user_path(user)`      | ユーザーを削除するアクション                |

위 코드를 사용하면, 라우팅이 유효하게 됩니다. 그러나 라우팅을 하여 접속하려는 페이지가 아직 존재하지 않습니다. 이 문제를 해결하기 위해선 7.1.4에서 최소한의 프로필을 표시하는 페이지를 작성해볼 예정입니다.

![](../image/Chapter7/user_show_unknown_action_3rd_edition.png)

유저를 표시하기 위해 표준적인 Rails의 기능을 사용해보도록 합시다. Rails에서의 표준 출력 기능이란 `app/views/users/show.html.erb` 를 의미합니다. 그러나 `rails generate controller Users new` 로 생성했을 때와는 다르게, (`new.html.erb` 뷰를 생성했을 때와는 달리)이번에는 제네레이터를 사용하지 않고 `show.html.erb` 를 생성할 필요가 있습니다. 때문에 `app/views/users/show.html.erb` 파일을 수동으로 생성하고, 아래의 코드를 붙여 넣어주세요.

```erb
<!-- app/views/users/show.html.erb -->

<%= @user.name %>, <%= @user.email %>
```

이 뷰에서 루비코드를 작성하여 유저이름과 메일 주소를 표시해봅니다. 인스턴스 변수 `@user` 가 존재한다는 것을 전제로 합니다. 물론 유저표시페이지의 최종적인 상태는 이것과는 매우다르며, 해당 메일주소가 그대로 일반에 공개되도록 하진 않을 것이빈다.



유저표시용 뷰가 정상적으로 동작하기 위해서는, Users컨트롤러내의 `show` 액션에 대응하는 `@user` 변수를 정의할 필요가 있습니다. 상상하시는 대로, 여기서는 User모델의 `find` 메소드를 사용하여 데이터베이스로부터 유저를 조회합니다. 아래와 같이 수정해보세요.

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController

  def show
    @user = User.find(params[:id])
  end

  def new
  end
end
```

유저의 id를 읽어들일 때에는 `params`를 사용합니다. Users 컨트롤러에 리퀘스트가 정상적으로 송신되면, `params[:id]` 부분은 유저 아이디의 1이 입력될 것입니다. 즉 이 부분은 [6.1.4](Chapter6.md#614-User-Object를-검색해보자) 에 배운 `find` 메소드의 `User.find(1)` 과 같은 결과가 될 것입니다. (*기술적인 보충설명*: `params[:id]` 는 문자열형태의 `"1"` 입니다만, `find` 메드에서는 자동적으로 정수형으로 변환됩니다.)



유저의 뷰와 액션을 정의했습니다. /users/1 은 완전하게 동작할 것입니다. 이 때, 만일 bcrypt gem을 추가하고 나서 한 번도 Rails 서버를 동작하지 않은 경우라면, 여기서 재부팅을 해보세요. ([컬럼 1.1](Chapter1.md#컬럼-11-숙련-이라고-하는-것은)) 재부팅을 하면 /users/1에 접속하여 디버그정보로부터 `params[:id]` 의 값을 확인할 수 있을 겁니다.

```
---
action: show
controller: users
id: '1'
```

이 `id: '1'` 는 /users/`:id` 로부터 얻은 값입니다. 이 값을 사용하여

```ruby
User.find(params[:id])
```

위 코드에 id=1 의 유저를 검색할 수 있는 구조가 된 것입니다.

![](../image/Chapter7/user_show_3rd_edition.png)

##### 연습

1. `.erb` 의 루비코드를 사용하여, 매직컬럼 (`created_at`과 `updated_at`) 의 값을 show페이지에 출력해봅시다.
2. `.erb` 의 루비코드를 사용하여 `Time.now` 의 결과를 show 페이지에 출력해봅시다. 페이지를 새로고침하면 그 결과가 어떻게 되는지도 확인해봅시다.

### 7.1.3 Debugger 메소드

[7.1.2](Chapter7.md#712-User-Resource), 어플리케이션의 동작을 이해하기 위해 `debug` 메소드가 도움이된다는 것을 배워보았습니다. 그러나 좀 더 직접적인 디버그를 하는 방법이 있습니다. `byebug` gem 을 설치하면 사용할 수 있는 `debugger` 메소드입니다. 어떻게 디버그할 수 있냐하면, `debugger` 메소드를 실제로 어플리케이션에 작성하여 확인해볼 수 있습니다.

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController

  def show
    @user = User.find(params[:id])
    debugger #추가한 코드
  end

  def new
  end
end
```

`debugger` 메소드를 작성하면 브라우저에서 /users/1 에 접속하여 Rails 서버를 기동할 시에 터미널을 확인해봅시다. `byebug` 프롬프트가 표시될 것입니다.

`(byebug)`

이 프롬프트에서는 Rails 콘솔에서처럼 커맨드를 출력하는 것이 가능하며, 어플리케이션의 `debugger` 가 호출되었을 순간의 상태를 확인할 수 있습니다.

```
(byebug) @user.name
"Example User"
(byebug) @user.email
"example@railstutorial.org"
(byebug) params[:id]
"1"
```

또한 Ctrl+D 를 누르면, 프롬프트로부터 텍스트를 얻을 수 있습니다. 디버그가 끝나면 `show` 액션 내부의 `debugger` 메소드는 삭제해줍시다.

```ruby
# app/controllers/users_controller.rb

class UsersController < ApplicationController

  def show
    @user = User.find(params[:id])
  end

  def new
  end
end
```

앞으로 Rails 어플리케이션에서 내부를 알고 싶거나 궁금한 부분이 있다면, 위와 같이 `debugger`메소드를 사용해봅시다. 트러블이 발생할 것 같은 코드 가까이에 작성해놓는다면 매우 좋을 것입니다. byebug gem 을 사용하여 시스템의 상태를 조사할 때는 어플리케이션 내부의 에러를 추적하거나 디버그를 할 때에 매우 도움될 것입니다.

##### 연습

1. `show` 액션 내부에 `debugger` 를 작성하고, 브라우저에서 /users/1 에 접속해봅시다. 그 다음 콘솔에서 `puts` 메소드를 사용하여 `params` 해시의 내부를 *YAML 형식* 으로 표현해봅시다. *Hint* : [7.1.1](Chapter7.md#711-Debug와-Rails환경) 의 연습문제를 참고해봅시다. 해당 연습문제에서는 `debug` 메소드를 표시한 디버그 정보를 어떻게 YAML형식으로 표시했었나요?
2. `new` 액션의 내부에 `debugger` 를 작성하고, /users/new에 접속해봅시다. `@user` 의 내용은 어떻게 되어 있습니까? 확인해봅시다.

### 7.1.4 Gravatar 이미지와 사이드 바

이전 섹션에서 기본적인 유저 페이지의 정의를 해보았습니다. 이번에는 각 유저의 프로필 사진을 간단하게라도 표시할 수 있도록 해보고, 사이드바도 만들어 보겠습니다. 여기서는 [Gravatar (Globally Recognized AVATAR)](http://gravatar.com/) 를 프로필에 도입해보겠습니다. Gravatar는 무료서비스이며 프로필 사진을 업로드하여 지정한 메일주소와 관련지을 수 있습니다. 그 결과, Gravatar는 프로필사진을 업로드할 때 귀찮은 작업이나 사진을 빼먹는 등의 오류, 이미지의 저장소 등의 고민을 해결할 수 있습니다. 애당초 유저의 메일주소를 사용한 Gravatar전용의 이미지 주소를 구성하는 것만으로도 Gravatar의 이미지가 자동적으로 표시됩니다. (커스텀 이미지를 사용하는 방법에 대해서는 13.4에서 다룹니다.)



여기는 아래의 코드와 같이, `gravatar_for` 헬퍼 메소드를 사용하여 Gravatar의 이미지를 이용해볼 수 있습니다.

```ruby
# app/views/users/show.html.erb

<% provide(:title, @user.name) %>
<h1>
  <%= gravatar_for @user %>
  <%= @user.name %>
</h1>
```

기ㄴ으로는 헬퍼파일에서 정의하는 메소드는 자동적으로 모든 뷰 파일 내부에서 이용할 수 있습니다. 여기서는 편의성을 생각하여 `gravatar_for` 를 User 컨트롤러에 대응되어있는 헬퍼파일에 작성해봅시다. [Gravatar의 홈페이지](http://ja.gravatar.com/site/implement/hash/) 에도 기술되어 있듯, Gravatar의 URL은 유저의 메일주소를 [MD5](https://ja.wikipedia.org/wiki/MD5) 라고하는 구조로 해시화합니다. Ruby에는 `Digest` 라이브러리의 `hexdigest` 메소드를 사용하면, MD5의 해시화를 구현할 수 있습니다.

```ruby
>> email = "MHARTL@example.COM"
>> Digest::MD5::hexdigest(email.downcase)
=> "1fda4469bcbec3badf5418269ffc5968"
```

메일 주소는 대문자와 소문자를 구별하지 않습니다만, ([6.2.4](Chapter6.md#624-포맷을-검증해보자)) MD5 해시에서는 대문자와 소문자를 구별합니다. Ruby의 `downcase` 메소드를 사용하여 `hexdigest` 의 파라미터를 소문자로 변환합니다. (본 튜토리얼에서는 [6.2.5](Chapter6.md#625-유니크성을-검증해보자) 에서의 콜백처리에서 소문자변환을 한 메일주소를 사용하기 때문에, 여기서 소문자변환을 하지 않아도 결과는 똑같습니다. 단, 추후 `gravatar_for` 메소드가 다른 장소에서부터 호출될 가능성을 고려한다면, 여기서 소문자변환을 해주는 것도 좋다고 생각합니다.) 

`gravatar_for` 헬퍼를 작성한 결과는 아래와 같습니다.

```ruby
# app/helpers/users_helper.rb

module UsersHelper

  # 파라미터로 주어진 유저의 Gravatar 이미지를 리턴합니다.
  def gravatar_for(user)
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}"
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end
```

위 코드는, Gravatar 이미지태그에 `gravatar` 클래스와 유저이름의 alt 텍스트를 추가한 것을 리턴합니다. (alt 텍스트를 추가하면, 시각장애가 있는 유저들에게 스크린리더를 사용할 수 있게해줍니다.)



프로필 페이지는 아래와 같이 됩니다. 여기서 기본 Gravatar이미지를 출력하고 있으나, 이것은 기본 메일주소 `user@example.com` 이 진짜 메일주소가 아니기 때문입니다. (example.com 이라는 도메인이름은, 예제로써 사용하기 때문에 특별한 도메인입니다.)

![](../image/Chapter7/profile_with_gravatar_3rd_edition.png)

어플리케이션에서 Gravatar를 이용할 수 있게 하기 위해서는 일단 `update_attributes`([6.1.5](Chapter6.md#615-User-Object를-수정해보자)) 사용하여 데이터베이스 상의 유저 정보(메일주소) 를 갱신해봅시다.

```ruby
$ rails console
>> user = User.first
>> user.update_attributes(name: "Example User",
?>                        email: "example@railstutorial.org",
?>                        password: "foobar",
?>                        password_confirmation: "foobar")
=> true
```

여기서 유저의 메일주소에 `example@railstutorial.org` 를 사용하였습니다. Gravatar상의 이 메일주소와 Rails Tutorial의 로고를 이미 연결시켜놓았기 때문에, 위와 같은 정보 수정을 하면 아래와 같이 출력됩니다.

![](../image/Chapter7/profile_custom_gravatar_3rd_edition.png)

목업 이미지 파일과 비슷하게 만들기 위해, 유저의 사이드바의 첫 버전을 만들어봅시다. 여기서는 `aside` 태그를 사용하여 구현하고 있습니다. 이 태그는 사이드바 등의 보완 컨텐츠를 표시하기 위해 사용합니다만, 단독으로 출력하는 것도 가능합니다. `row` 클래스와 `col-md-4` 클래스를 추가해놓습니다. 이 클래스들은 Bootstrap의 일부입니다. 유저 표시페이지를 변경한 결과는 아래와 같습니다.

```erb
<!-- app/views/users/show.html.erb -->

<% provide(:title, @user.name) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <h1>
        <%= gravatar_for @user %>
        <%= @user.name %>
      </h1>
    </section>
  </aside>
</div>
```

HTML 요소와 CSS 클래스를 추가한 덕분에 프로필 페이지(와 사이드바와 Gravatar) 에 SCSS로 아래의 스타일을 적용할 수 있게 되었습니다. (테이블 CSS의 계층구조로 되어있습니다만, 이게 가능한 경우는 Asset Pipline에서 SASS 엔진이 사용되는 경우에만 한합니다.) 페이지의 변경의 결과는 아래의 그림과 같습니다.

```scss
/* app/assets/stylesheets/custom.scss */

/* sidebar */

aside {
  section.user_info {
    margin-top: 20px;
  }
  section {
    padding: 10px 0;
    margin-top: 20px;
    &:first-child {
      border: 0;
      padding-top: 0;
    }
    span {
      display: block;
      margin-bottom: 3px;
      line-height: 1;
    }
    h1 {
      font-size: 1.4em;
      text-align: left;
      letter-spacing: -1px;
      margin-bottom: 3px;
      margin-top: 0px;
    }
  }
}

.gravatar {
  float: left;
  margin-right: 10px;
}

.gravatar_edit {
  margin-top: 15px;
}
```

![](../image/Chapter7/user_show_sidebar_css_3rd_edition.png)



##### 연습

1. (임의) Gravatar에서 아이디를 작성하고, 여러분의 메일주소와 적당한 이미지를 연결시켜보세요. 메일주소를 MD5 해시화하여 연결지은 이미지가 제대로 표시되는지 확인해보세요.
2. [7.1.4](Chapter7.md#714-Gravatar-이미지와-사이드-바) 에서 정의한 `gravatar_for` 헬퍼를 아래의 코드와 같이 변경하여 `size` 를 옵션 파라미터로 받을 수 있도록 해봅시다. 제대로 변경이 된다면,  `gravatar-for user, size: 50` 과 같은 호출이 가능합니다. *중요* : 여기서 수정한 헬퍼를 10.3.1에서 실제로 사용합니다. 잊지말고 꼭 구현해봅시다.
3. 옵션 파라미터는 지금도 Ruby 커뮤니티에서 일반적으로 사용되고 있습니다만,   Ruby 2.0에서부터 도입된 신기능 "*키워드 파라미터(Keyword Arguments)*" 에서도 구현할 수 있습니다. 앞서 변경한 아래 첫 번째 코드를, 아래 두 번째 코드와 같이 수정하여 제대로 동작하는지를 확인해봅시다. 이 두 가지 구현방법은 어떠한 차이점이 있는 것일까요?

```ruby
# app/helpers/users_helper.rb
module UsersHelper

  def gravatar_for(user, options = { size: 80 })
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    size = options[:size]
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}?s=#{size}"
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end
```

```ruby
# app/helpers/users_helper.rb
module UsersHelper

  def gravatar_for(user, size: 80)
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}?s=#{size}"
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end
```

