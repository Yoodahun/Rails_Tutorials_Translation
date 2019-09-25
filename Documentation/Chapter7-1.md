7.4에서도 설명드리겠습니다만, 유저 작성에서 중요한 것은 `input` 마다 있는 특수한 `name` 속성입니다.

```html
<input id="user_name" name="user[name]" - - - />
.
.
.
<input id="user_password" name="user[password]" - - - />
```

Rails는 `name` 을 사용하여 초기화한 해시(`params` 변수 경유)로 구성합니다. 이 해시는 입력된 값을 기반으로하여 유저를 생성할 때 사용됩니다. (상세히는 7.3에서 설명합니다.)



다음으로 중요한 요소는, `form` 태그입니다. Rails에서 `form` 태그를 작성할 때에는 `@user` 오브젝트를 사용합니다. 모든 Ruby 오브젝트는 자신의 클래스 정보를 가지고 있기 때문에 ([4.4.1](Chapter4.md#441-Constructor)) Rails는 `@user` 의 클래스가 User라는 것을 이미 알고 있습니다. 또한 `@user` 는 *새로운* 유저이기 때문에, Rails는 `post` 메소드를 사용하여 폼을 구축해야한다고 판단합니다. 새로운 오브젝트를 작성하기 위해 필요한 HTTP 리퀘스트는 POST이기 때문에, 해당 메소드는 RESTful 아키텍쳐로써 올바른 리퀘스트입니다.([컬럼 3.2](Chapter3.md#컬럼-32-GET이나-그-외-다른-HTTP-메소드에-대해)) 

```HTML
<form action="/users" class="new_user" id="new_user" method="post">
```

이 때, 위 코드에서의 `class`와 `id` 속성은 아키텍쳐로써의 기본적으로 관계없습니다. 여기서 중요한 속성은 `action="/users"` 와 `method="post"` 두 개입니다. 이 두 가지 속성은 /user에게 HTTP의 POST 리퀘스트를 보내는 지시를 내립니다.



`form` 태그 내부에 다음과 같은 HTML이 생성되어있는 것을 눈치채셨나요?

```html
<input name="utf8" type="hidden" value="&#x2713;" />
<input name="authenticity_token" type="hidden"
       value="NNb6+J/j46LcrgYUC60wQ2titMuJQ5lLqyAbnbAUkdo=" />
```

이 코드는 브라우저상에서는 아무것도 하지 않습니다만, Rails의 내부에서 사용되는 특별한 코드입니다. 따라서 어떠한 의도로 생성되었는지는 현 시점에서 알 필요가 없없습니다. ([컬럼 1.1](Chapter1.md#컬럼-11-숙련-이라고-하는-것은)) 간단히 정리하자면, Unicode 문자의 "`&#x2713`"(체크 마크) 를 사용하여 브라우저가 제대로된 문자코드를 송신하고 있는지, 혹은 *Cross-Site Request Forgery (CSRF)* 을 호출하는 공격을 막기 위해 신뢰할 수 있는 토큰을 포함하거나 합니다.

##### 연습

1. [*Learn Enough HTML to Be Dangerous*](http://learnenough.com/html-tutorial) 에서 HTML을 모두 수동으로 작성하고 있습니다. 어째서 `form` 태그를 사용하지 않은 것일까요?



## 7.3 유저 등록 실패

폼의 HTML이 어떻게 되어있는지 간단히 설명해보았습니다. 폼을 이해하기 위해선 *유저 등록(생성)을 실패할 때* 가 제일 효과적입니다. 이번 섹션에서는 무효한 데이터송신을 받는 유저 등록폼을 작성하고, 유저 등록 폼을 수정하여 에러 리스트를 표시해보겠습니다. 에러 리스트를 표시하는 디자인은 아래의 목업과 같습니다.

![](../image/Chapter7/signup_failure_mockup_bootstrap.png)

### 7.3.1 올바른 Form

[7.1.2](#712-User-Resource) 에서 `resouces :user` 를 `routes.rb` 파일을 추가하여 자동적으로 Rails 어플리케이션이 특정 RESTful URI로 매칭되도록 한 것을 기억하고 계시나요? 특히 /user로의 POST 리퀘스트는 `create` 액션으로 보내집니다. 이번 설명에서는 `create` 액션에서 Form의 데이터를 수신받고, `User.new` 를 사용하여 새로운 유저 오브젝트를 생성한 후, 유저를 저장 (혹은 저장에 실패) 하고, 다시 리퀘스트 송신용 유저 등록 페이지를 표시하는 방법으로 기능을 구현하고자 합니다. 일단 유저 등록용 폼의 코드를 다시 확인해봅시다.

`<form action="/users" class="new_user" id="new_user" method="post">`

[7.2.2](#722-Form-HTML) 에서 설명드린 것 처럼, 위 HTML은 POST 리퀘스트를 /user 라고하는 URL에 보냅니다.



유저 등록 폼을 동작시키기 위해, 우선 아래의 코드를 추가해봅시다. 이 코드는 [5.1.3](Chapter5.md#513-파셜-Partial) 의 "Partial" 에서 사용한 `render` 메소드를 또 다시 사용하고 있습니다. `render` 는 컨트롤러의 액션 안에서도 정상적으로 동작합니다. 여기서 예전에 설명한 `if-else` 분기구조를 떠올려보세요. if문을 사용하여 저장해 성공했는지의 여부에 따라 `@user.save` 의 값이 `true` 나 `false` ([6.1.3](Chapter6.md#613-User-Object-를-검색해보자)) 이 될 때, 각각 성공시의 처리와 실패시의 처리를 나눌 수 있습니다.

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController

  def show
    @user = User.find(params[:id])
  end

  def new
    @user = User.new
  end

  def create
    @user = User.new(params[:user])    # 아직 구현이 끝나지 않았습니다.
    if @user.save
      # 저장 성공 시의 결과를 여기에 작성합니다.
    else
      render 'new'
    end
  end
end
```

코멘트를 보면 알 수 있듯이, 위 코드는 아직 구현이 완려되지 않았기 때문에 주의해주세요. 그러나 구현의 출발점으로서는 충분합니다. 최종적인 구현은 7.3.2에서 완료합니다.



위 코드의 동작을 이해하기 위한 제일 좋은 방법으로는, 실제로 무효한 유저 등록용 데이터를 송신(*submit*) 해보는 것입니다. 송신해본다면 결과는 아래와 같이 됩니다. 또한 이 때의 정보는 아래 두 번째 캡쳐와 같습니다.

![](../image/Chapter7/signup_failure_4th_edition.png)

![](../image/Chapter7/signup_failure_debug_4th_edition.png)

Rails가 리퀘스트를 핸들링하는 방법을 보다 더 깊게 이해하기 위해서는 디버그 정보에서의 파라미터 해시의 `user` 부분을 상세히 확인해볼 필요가 있습니다.

```ruby
"user" => { "name" => "Foo Bar",
            "email" => "foo@invalid",
            "password" => "[FILTERED]",
            "password_confirmation" => "[FILTERED]"
          }
```

이 해시는 User 컨트롤러에 `params` 로써 입력됩니다. [7.1.2](#712-User-Resource) 에 설명드린 것과 같이, `params` 해시에는 각 리퀘스트의 정보가 포함되어 있습니다. 유저 등록 정보의 리퀘스트의 경우, `params` 에 여러개의 해시에 대한 해시(*hash-of-hashes* : 부모와 자식관계의 해시) 가 포함되어 있습니다. (또한 [4.3.3](Chapter4.md#433-Hash와-Symbol) 에서는 hash-of-hashes의 설명과 함께 콘솔세션에서 사용하기 위해 일부러 `params` 라고 하는 이름의 변수를 사용했습니다.) 위 디버그 정보에는 폼의 리퀘스트 송신 결과가, 송신된 값에 대응하는 속성과 함께 `user` 해시에 저장되어 있습니다. 이 해시의 키가 `input` 태그에 있는 `name` 속성의 값입니다.([7.2.2](#722-Form-HTML) 에서 보여드린 HTML 코드를 확인해주세요.) 예를 들어 다음과 같이

`<input id="user_email" name="user[email]" type="email" />`

`"user[email]"` 라고하는 값은 `user` 해시의 `:email` 키의 값과 일치합니다.

해시 키의 형태는, 디버그 정보에서는 문자열의 형태입니다만 Rails에서는 무나졍ㄹ이 아닌, `params[:user]`와 같이 "심볼" 로써 Users 컨트롤러에 입력되고 있음을 주의해주세요. 이 성질에 의해, [4.4.5](Chapter4.md#445-User-Class) 나 위 `create` 액션의 코드에서 처럼, `User.new` 의 파라미터에서 필요한 데이터와 완전히 일치합니다. 즉 지금까지 봐온 다음과 같은 코드는

`@user = User.new(params[:user])`

실제로는 아래와 같은 코드와 같다고 할 수 있습니다.

`@user = User.new(name: "Foo Bar", email: "foo@invalid", password: "foo", password_confirmation: "bar")`

단, 이전 버전에서의 Rails에서는 다음의 코드여도 동작했습니다만,

`@user = User.new(params[:user])`

실제로는, 나쁜 뜻이 있는 유저에 의해 어플리케이션의 데이터베이스를 해킹당할 수도 있기 때문에, 신중하게 대책을 세울 필요가 있습니다. 게다가 그 대책이 다른 에러를 일으킬 위험성도 있기 때문입니다. 그리하여 Rails 4.0 이후에서는 위 코드를 에러를 일으키는 것으로 보안성을 강화하고, *Strong Parameter* 라고 하는 기술을 표준으로 삼았습니다.

### 7.3.2 Strong Parameters

[4.4.5](Chapter4.md#445-User-Class) 에서 아래의 코드를 간단히 설명한 적이 있습니다. 해시를 사용하여 Ruby의 변수와 초기화합니다.

`@user = User.new(params[:user])    # 구현이 아직 끝난 것이 아닙니다.`

위 코드는 최종적인 형태는 아닙니다. 그 이유는 `params` 해시 전체를 초기화하는 행위는 보안상 매우 위험하기 때문입니다. 유저가 보낸 데이터 전체를 `User.new` 에 전달하고 있는 것입니다. 여기서 User모델에 `admin` 속성이라고 하는 것이 있다고 해봅시다. 이 속성은 Web사이트의 관리자인지 아닌지를 나타내는 값입니다. (사실 이 속성을 구현하는 것은 10.4.1 에서 구현합니다.) `admin='1'` 이라고 하는 값을 `params[:user]` 의 일부와 함께 넘긴다면, 이 속성을 `true` 로 하는 것이 가능합니다. 이것은 *curl* 등의 커맨드를 사용하면 간단하게 실현할 수 있습니다. `params` 해시값 전체를 `User.new` 에 넘겨버리면, 어떤 유저라도 `admin='1'` 을 Web 리퀘스트에 같이 포함하는 것으로 Web 사이트의 관리자 권한을 뺏을 수 있게 되어버립니다.



이전 버전의 Rails에서는 모델에서 *attr_accessible* 메소드를 사용하는 것으로 위와 같은 위험을 방지할 수 있었습니다만, Rails 4.0 이후에서는 컨트롤러층에서 *Strong Parameter* 라는 기술을 사용할 것이 권장되고 있습니다. Strong Parameter 를 사용하는 것으로 *필수* 의 파라미터와 *허가받은* 파라미터를 지정할 수 있습니다. 게다가 위와같이 `params` 해시를 전부 한 번에 넘기면 에러가 발생할 수 있습니다. 때문에 Rails에서는 디폴트 옵션으로 위와 같은 구현을 하게 된 것입니다.



이 경우, `params` 해시는 `:user` 속성을 필수로하고, 이름, 메일주소, 비밀번호, 비밀번호의 확인 속성을 각각 허가하고, 그 외의 값은 허가하지 않으려 할때, 다음과 같이 기술합니다.

`params.require(:user).permit(:name, :email, :password, :password_confirmation)`

이 코드의 리턴값은 허가된 속성만을 포함한 `params` 의 해시값입니다. (`:user` 속성이 없는 경우에는 에러가 발생합니다.)



위 파라미터들을 사용하기 쉽게 하기위해, `user_params` 라고 하는 외부 메소드를 사용하는 것이 관습입니다. 이 메소드는 적절히 초기화한 해시값을 리턴하여 `params[:user]` 대신 사용할 수 있게 해줍니다.

`@user = Uesr.new(user_params)`

이 `user_params` 메소드는 Users 컨트롤러 내부에서만 실행되고, Web 경유로 외부 유저로 하여금 실행시킬 필요가 없기 때문에, 아래와 같이 Ruby의 `private` 키워드를 사용하여 *외부에서는 사용할 수 없게* 합니다. (`private` 키워드는 11.1.2에서 자세히 소개합니다.)

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  .
  .
  .
  def create
    @user = User.new(user_params)
    if @user.save
      # 保存の成功をここで扱う。
    else
      render 'new'
    end
  end

  private

    def user_params # 추가한 코드
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end
```

여담으로, `private` 키워드 이후의 코드를 강조하기 위해, `user_params` 의 들여쓰기를 1단계 더 깊게 들여쓰고 있습니다. (경험적으로는 이것은 현명한 판단이라고 생각합니다. 클래스 내부에 많은 메소드가 있는 경우, `private` 메소드의 위치를 간단히 파악할 수 있기 때문입니다. 들여쓰기가 없는 경우와 비교해보면 어디서부터 `private` 처리가 되는지 고민할 필요가 없어질 것입니다.)



이 시점에서 (송신버튼을 눌러도 에러가 출력되지 않는다는 의미로) 유저 등록 폼은 동작할 수 있게 되었습니다. 단, 아래의 캡쳐와 같이 (개발자용의 디버그 영역을 제외하고) 잘못된 정보를 송신해도 어떠한 피드백이 돌아오지 않습니다. 이것은 유저가 당황하는 원인이 됩니다. 또한 유효한 유저 정보를 송신해도 새로운 유저가 실제로 작성되었는지 알 방법이 없습니다. 전자의 경우를 7.3.3에서, 후자의 경우를 7.4에서 해결해보겠습니다.

![](../image/Chapter7/invalid_submission_no_feedback_4th_ed.png)

##### 연습

1. /signup?admin=1 에 접속하여 `params` 내부에 `admin` 속성이 포함되어 있는지 디버그 정보로부터 확인해봅시다.

### 7.3.3 에러 메세지

유저 등록에 실패한 경우, 제일 마지막 수단으로서, 문제가 발생해서 유저 등록이 안되었다는 것을 유저에게 아릭 쉽게 전달하기 위한 에러메세지를 추가해보도록 합시다. Rails에서는 이러한 메세지를 User모델의 검증 시에 자동적으로 생성해줍니다. 예를 들어, 유저 정보의 메일주소가 무효하고, 패스워드ㅡ이 길이가 너무 짧은 상태에서 저장하려고 해봅시다.

```ruby
$ rails console
>> user = User.new(name: "Foo Bar", email: "foo@invalid",
?>                 password: "dude", password_confirmation: "dude")
>> user.save
=> false
>> user.errors.full_messages
=> ["Email is invalid", "Password is too short (minimum is 6 characters)"]
```

[6.2.2](Chapter6.md#622-존재성을-검증해보자) 에서 살짝 다루어본 `error.full_messages` 오브젝트는, 에러메세지의 배열을 가지고 있습니다.



위 콘솔세션에서 표시되고 있는 것 처럼, 저장(등록)이 실패하게되면, `@user` 오브젝트에 고나련된 에러메세지의 리스트가 생성됩니다. 이 메세지를 브라우저에 표시하기 위해서는 유저의 `new` 페이지에서 에레머세지의 파셜(*partial*) 을 출력해봅시다. 이 때, `form-control` 이라고하는 CSS 클래스도 같이 추가하여 Bootstrap이 제대로 동작하는지 확인해봅시다. 수정 결과는 아래와 같습니다. 여기서 사용하고 있는 에러메세지의 파셜은, 어디까지나 시작품인 것을 알아주세요. 최종버전은 13.3.2 에서 다룹니다.

```erb
<!-- app/views/users/new.html.erb -->

<% provide(:title, 'Sign up') %>
<h1>Sign up</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@user) do |f| %>
      <%= render 'shared/error_messages' %>

      <%= f.label :name %>
      <%= f.text_field :name, class: 'form-control' %>

      <%= f.label :email %>
      <%= f.email_field :email, class: 'form-control' %>

      <%= f.label :password %>
      <%= f.password_field :password, class: 'form-control' %>

      <%= f.label :password_confirmation, "Confirmation" %>
      <%= f.password_field :password_confirmation, class: 'form-control' %>

      <%= f.submit "Create my account", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```

