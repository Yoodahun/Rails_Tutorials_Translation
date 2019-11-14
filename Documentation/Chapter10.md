# 제 10장 유저의 수정, 표시, 삭제

이번 챕터에서는 User리소스용의 REST액션 중, 지금까지 구현하지 않았던 `edit`, `update`, `index`, `destroy` 액션을 구현해보고, REST 액션을 완성할 것 입니다. 이번 챕터에서는 우선 유저가 자신의 프로필을 자신이 수정할 수 있도록 해보겠습니다. 여기서 [제 8장](Chapter8.md) 에서 구현한 인증용 코드를 사용해볼텐데요, *인증 모델(Authorization Model)* 에 대해 설명할 수 있는 자연스러운 계기가 될 것 입니다. 그 다음으로, 모든 유저의 리스트를 볼 수 있게 할 것입니다. (물론 이것도 인증이 필요합니다.) 이것은 샘플 데이터로써 페이지네이션 (pagination) 을 도입해보는 계기가 될 것입니다. 유저의 삭제는 모든 유저에게 권한이 있는 것이 아니기 때문에, 관리유저라고 하는 특별한 클래스를 작성하고, 이 유저만이 삭제를 할 수 있도록 해볼 것입니다.

## 10.1 유저를 수정해보자

유저의 정보를 편집하는 패턴은, 신규 유저의 생성과 매우 닮아있습니다. ([제 7장](Chapter7.md)) 신규 유저용의 뷰를 출력하는 `new` 액션과 마찬가지로, 유저를 수정하기 위한 `edit` 액션을 생성하면 될 것입니다. POST 리퀘스트에 응답하는 `create` 액션 대신에, PATCH 리퀘스트에 응답하는 `update` 액션을 생성합니다. ([컬럼 3.2](Chapter3.md#컬럼32-GET이나-그-외-다른-HTTP-메소드에-대해)) 제일 큰 차이점은, 유저 등록은 누구나 할 수 있습니다만, 유저의 정보를 수정하는 것은 해당 유저 자신에게만 한정되어있다는 점입니다. [제 8장](Chapter8.md) 에서 구현한 인증기능을 사용하면, *before filter* 를 사용하여 이러한 액세스 제어를 구현할 수 있습니다.



그럼 일단 언제나처럼 `updating-users` 라고하는 토픽 브랜치를 생성해봅시다.

`$ git checkout -b updating-users`



###  유저 정보 수정 Form

일단 유저의 정보를 수정하는 Form부터 생성해봅시다. 목업은 아래의 그림과 같습니다. 아래 목업을 움직이는 페이지로 만들기 위해서는, User 컨트롤러에 `edit` 액션을 추가하고, 해당 액션에 대응하는 `edit` 뷰를 구현할 필요가 있습니다. `edit` 액션의 구현부터 시작해보겠습니다. 여기서는 데이터베이스로부터 적절한 유저 데이터를 읽어들일 필요가 있습니다. 여기서 주의해야할 점으로는, 이전 7장에서는, 유저 정보 수정 페이지의 올바른 URL이 /users/1/edit 라고 하는 점입니다. (유저의 id가 1일 경우.) 유저의 id는 `params[:id]` 변수로 읽어들일 수 있습니다. 즉, 아래의 코드를 사용하면 유저를 지정할 수 있습니다.

![](../image/Chapter10/edit_user_mockup_bootstrap.png)

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
    @user = User.new(user_params)
    if @user.save
      log_in @user
      flash[:success] = "Welcome to the Sample App!"
      redirect_to @user
    else
      render 'new'
    end
  end

  #추가
  def edit
    @user = User.find(params[:id])
  end
  #추가

  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end
```

유저 정보 수정 페이지에 대응하는 뷰는 아래와 같습니다. (이 파일은 수동으로 생성할 필요가 있습니다.) 이 코드가 이전 7장에서 보았던 코드와 매우 닮아있는 것을 떠올려보세요. 중복된 곳이 많다는 것은, 이러한 코드의 반복을 파셜로 한꺼번에 묶을 수 있다는 점입니다. 이번에는 파셜에 정리하는 작업은 연습문제로 내보겠습니다.

```erb
<!--  app/views/users/edit.html.erb -->

<% provide(:title, "Edit user") %>
<h1>Update your profile</h1>

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

      <%= f.submit "Save changes", class: "btn btn-primary" %>
    <% end %>

    <div class="gravatar_edit">
      <%= gravatar_for @user %>
      <a href="http://gravatar.com/emails" target="_blank">change</a>
    </div>
  </div>
</div>
```

위 코드는, 이전 [7.3](Chapter7.md#73-유저-등록-실패) 에서 도입한 `error_messages` 파셜을 재이용합니다. 여기서 Gravatar로의 링크에서, `target="_blank"` 가 쓰이고 있는데, 이것을 사용하면 링크가 새로운 탭(혹은 윈도우) 로 열리기 때문에, 별도의 Web사이트로의 링크를 만들 때 유용합니다. (단, `target="_bank"` 에는 보안상의 작은 문제가 있습니다. 자세하게는 연습문제에서 다루어보겠습니다.)



`edit` 액션의 `@user` 인스턴스변수를 사용하면, 유저 정보 수정 페이지가 제대로 표시될 것입니다. "Name" 이나 "Email" 의 부분을 보면, Rails에 의하여 이름이나 메일주소의 필드에 값이 자동적으로 입력되어있는 것을 알 수 있습니다. 이것은 `@user` 변수의 속성정보로부터 읽어들인 값입니다.

![](../image/Chapter10/edit_page_3rd_edition.png)

위 화면의 HTML 소스를 확인해보면, 조금은 다른 부분도 있습니다만 대부분의 코드는 보통의 form태그와 마찬가지로 표시됩니다.

```html

<form accept-charset="UTF-8" action="/users/1" class="edit_user"
      id="edit_user_1" method="post">
  <input name="_method" type="hidden" value="patch" />
  .
  .
  .
</form>
```

다음 입력 필드에 hidden 속성을 주목해주세요.

```html
<input name="_method" type="hidden" value="patch" />
```

Web 브라우저는 네이티브에서는 PATCH 리퀘스트 (REST 관습으로서 요구되는) 를 송신할 수 없기에, Rails에서는 POST 리퀘스트와 hidden input 필드를 이용하여 PATCH 리퀘스트를 "위조" 하고 있습니다.



여기서 한 가지 미묘한 점을 지적하고자 합니다. `form_for(@user)` 의 코드는, 이전 7장에서 보았던 코드와 *완전하게*  일치합니다. 그렇다고 한다면, Rails에서는 어떻게 신규 유저용의 POST 리퀘스트와 기존 유저의 정보 편집용의 PATCH 리퀘스트를 구별할 수 있는 것일까요? 그 답으로는, Rails에서는 유저가 신규 유저인지, 데이터베이스에 존재하는 기존 유저인지를 Active Record의 `new_record?` 논리값 메소드를 사용하여 구별하기 때문입니다.

```ruby
$ rails console
>> User.new.new_record?
=> true
>> User.first.new_record?
=> false
```

Rails에서는 `form_for(@user)` 를 사용하여 폼을 구성하면, `@user.new_record?` 가 `true` 가 되어 POST를, 그렇지 않으면 `false` 가 되어 PATCH를 사용하게 됩니다.



이ㄴ 작업의 마지막으로, 네비게이션바에 있는 유저 정보 설정으로의 링크를 수정해봅시다. `edit_user_path` 라고 하는 named root와, 9장에서 정의한 `current_user` 라고하는 헬퍼 메소드를 사용하면, 간단하게 구현할 수 있습니다.

`<%= link_to "Settings", edit_user_path(current_user) %>`

완전한 어플리케이션 코드는 아래와 같습니다.

```erb
<!-- app/views/layouts/_header.html.erb -->

<header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", root_path, id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home", root_path %></li>
        <li><%= link_to "Help", help_path %></li>
        <% if logged_in? %>
          <li><%= link_to "Users", '#' %></li>
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown">
              Account <b class="caret"></b>
            </a>
            <ul class="dropdown-menu">
              <li><%= link_to "Profile", current_user %></li>
 <!-- 추가하는 코드 -->
              <li><%= link_to "Settings", edit_user_path(current_user) %></li>
<!-- 추가하는 코드 -->             
              <li class="divider"></li>
              <li>
                <%= link_to "Log out", logout_path, method: :delete %>
              </li>
            </ul>
          </li>
        <% else %>
          <li><%= link_to "Log in", login_path %></li>
        <% end %>
      </ul>
    </nav>
  </div>
</header>
```

##### 연습

1. 앞서 언급했다시피, `target="_blank"` 는 새로운 페이지로 접속할 때에는 보안상의 작은 문제가 있습니다. 링크 사이트가 HTML 문서의 `window` 오브젝트를 핸들링한다는 점입니다. 구체적으로는 [*피싱 (Phising)*](https://ja.wikipedia.org/wiki/フィッシング_(詐欺)) 사이트와 같은, 악의적인 컨텐츠가 우리 컴퓨터에 들어올 가능성이 있습니다. Gravatar와 같은 유명한 사이트에서는 이러한 사태를 방지하고 있을 것이라 생각합니다만, 만에 하나를 대비하여 [이러한 보안상의 위험](http://lmgtfy.com/?q=target+_blank+security) 을 배제해봅시다. 대처방법으로는 링크용의 `a` 태의 `rel`(relationship) 속성에 `"noopener"` 를 설정하면 됩니다. 그럼 바로 Gravatar의 편집용 페이지 링크에 설정해봅시다.

2. 파셜을 이용하여  `new.html.erb` 뷰와 `edit.html.erb` 뷰를 리팩토링해봅시다. (코드의 중복을 삭제해봅시다.) *힌트*: [3.4.3](Chapter3.md#343-레이아웃과-html에-직접-쓰는-Ruby-Refactor) 에서 사용한 `provide` 메소를 사용하면, 중복을 제거할 수 있습니다. (관련된 7장의 연습 문제를 이미 풀었다면, 이 연습 문제를 제대로 풀지 못할 가능성이 있습니다. 제대로 풀지 못한다면, 기존의 코드의 어느 부분에 차이가 있는지 생각해가면서 코드를 확인해봅시다. 예를 들어 필자라면 아래의 코드에서 사용한 변수를 넘기는 테크닉을 사용하여 아래 두 번째 코드나 세 번째 코드에 필요한 URL을 아래 첫 번째 코드에서 입력해보도록 합시다.)

   ```erb
   <!-- app/view/users/_form.html.erb -->
   <%= form_for(@user) do |f| %>
     <%= render 'shared/error_messages', object: @user %>
   
     <%= f.label :name %>
     <%= f.text_field :name, class: 'form-control' %>
   
     <%= f.label :email %>
     <%= f.email_field :email, class: 'form-control' %>
   
     <%= f.label :password %>
     <%= f.password_field :password, class: 'form-control' %>
   
     <%= f.label :password_confirmation %>
     <%= f.password_field :password_confirmation, class: 'form-control' %>
   
     <%= f.submit yield(:button_text), class: "btn btn-primary" %>
   <% end %>
   ```

```erb
<!-- app/views/users/new.html.erb -->
<!-- signup페이지의 파셜 -->
<% provide(:title, 'Sign up') %>
<% provide(:button_text, 'Create my account') %>
<h1>Sign up</h1>
<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= render 'form' %>
  </div>
</div>
```

```erb
<!-- app/views/users/edit.html.erb -->
<!-- edit페이지의 파셜 -->
<% provide(:title, 'Edit user') %>
<% provide(:button_text, 'Save changes') %>
<h1>Update your profile</h1>
<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= render 'form' %>
    <div class="gravatar_edit">
      <%= gravatar_for @user %>
      <a href="http://gravatar.com/emails" target="_blank">Change</a>
    </div>
  </div>
</div>
```

### 10.1.2 유저 정보 수정의 실패

이번 섹션에서는 [7.3](Chapter7.md#73-유저-등록-실패) 의 유저 등록에 실패했을 때와 비슷한 방법으로, 정보 수정에 실패했을 경우에 대해서 다루어 보도록 하겠습니다. 우선은 `update` 액션을 작성해보겠습니다. 이것은 6장에서 살짝 다루어 보았습니다만, `update_attributes` 를 사용하여 송신되어진 `params` 해시값을 기반으로하여 유저 정보를 갱신합니다. 무효한 정보가 송신되어졌을 경우, 유저 정보 갱신의 결과로써 `false` 가 리턴되고, `else` 분기에 의해 정보 수정 페이지로 리다이렉트합니다. 이러한 패턴은 이전에도 다루어본 적이 있을 것입니다. 이 구조는 `create` 액션의 7장에서의 제일 첫 번째 버전과 매우 닮아 있습니다.

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
    @user = User.new(user_params)
 # update 액션은 이 부분과 매우 닮아 있습니다.
    if @user.save
      log_in @user
      flash[:success] = "Welcome to the Sample App!"
      redirect_to @user
    else
      render 'new'
    end
  # update 액션은 이 부분과 매우 닮아 있습니다.   
  end

  def edit
    @user = User.find(params[:id])
  end
#추가
  def update
    @user = User.find(params[:id])
    if @user.update_attributes(user_params)
      # 정보 수정에 성공 했을 때의 처리를 기술한다.
    else
      render 'edit'
    end
  end
#추가
  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end
```

`update_attributes` 을 호출할 때, `user_params` 를 사용하고 있는 것을 확인해주세요. [7.3.2](Chapter7.md#732-Strong-Parameters) 에서 설명드렸다시피, 여기서는 Strong Parameters를 사용하여 불필요한 정보의 침입을 방지하고 있습니다.



User 모델의 Validation과 에러 메세지의 Partial은 이미 존재하기 때문에, 무효한 정보를 송신하면 도움되는 에러 메세지가 표시되도록 되어 있습니다.

![](../image/Chapter10/edit_with_invalid_information_3rd_edition.png)

##### 연습

1. 정보 수정 Form으로부터 유효하지 않은 유저 이름이나 메일주소, 패스워드를 사용하여 송신한 경우, 수정에 실패하는 것을 확인해봅시다.



### 10.1.3 정보 수정 실패시의 테스트

[10.1.2](#1012-유저-정보-수정의-실패) 에서는 정보 수정 Form의 실패 시 처리를 구현해보았습니다. 그 다음으로는 [컬럼 3.3](Chapter3.md#컬럼33-결국-테스트는-언제-하는-것이-좋은가) 에서 설명한 테스트의 가이드라인에 따라, 에러를 검출하기 위한 결합 테스트 코드를 작성해봅시다. 언제나처럼 결합 테스트를 생성하는 부분부터 시작해봅시다.

```
$ rails generate integration_test users_edit
      invoke  test_unit
      create    test/integration/users_edit_test.rb
```

제일 처음으로는 정보 수정 실패 시의 간단한 테스트를 추가해보겠습니다. 아래 테스트 코드에서는 우선 정보 수정 페이지로 접속하여, edit 뷰가 제대로 출력되는지를 체크하고 있습니다. 그 후, 무효한 정보를 송신하여 edit뷰로 다시 리다이렉트되는지를 체크합니다. 여기서 `PATCH` 리퀘스트를 보내기 위해 patch 메소드를 사용하고 있는 것을 확인해주세요. 이것은 get이나 post, delete 메소드와 마찬가지로, HTTP 리퀘스트를 송신하기 위한 메소드입니다.

```ruby
# test/integration/users_edit_test.rb
require 'test_helper'

class UsersEditTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "unsuccessful edit" do
    get edit_user_path(@user)
    assert_template 'users/edit'
    patch user_path(@user), params: { user: { name:  "",
                                              email: "foo@invalid",
                                              password:              "foo",
                                              password_confirmation: "bar" } }

    assert_template 'users/edit'
  end
end
```

위 코드로 테스트를 실행해보면 통과할 것입니다.

`$ rails test`

##### 연습

1. 위 테스트 코드에서 단 한 줄을 추가하여 올바른 *갯수* 의 에러 메세지가 표시되는지 테스트를 해봅시다. *힌트* : 이전 5장에서 소개해드린 `assert_select` 를 사용하여 `alert` 클래스의 `div` 태그를 찾아내어, "The form contains 4 errors." 라고 하는 텍스트를 확인해봅시다.



### 10.1.4 TDD로 정보 수정을 성공시켜보자.

이번에는 정보 수정 Form이 제대로 동작하게끔 해봅시다. 프로필 이미지의 수정은, 이미지 업로드를 Gravatar에 맡기고 있기에 이미 동작하고는 있습니다. 이미지 옆에 [change] 링크를 클릭하면, 아래와 같이 Gravatar를 수정할 수 있습니다. 그럼 이 외의 기능을 구현해봅시다.

![](../image/Chapter10/gravatar_cropper_new.png)

이즈음 되면, 보다 더 쾌적하게 테스트를 하기 위해서는 어플리케이션 용의 코드를 "구현 하기 전에" 결합 테스트 코드를 작성하는 것이 편리하다고 느낀 독자들이 있을 수도 있습니다. 실제로 그러한 테스트는 *Acceptance Test* 라고 불리기도 하며, 어떠한 기능의 구현을 완료하고 유저에게 납품가능한 상태인지 아닌지를 결정하는 테스트로써 알려져 있습니다. 이 것을 경험해보기 위해서 이번에는 테스트 주도 개발을 통해 유저의 편집 기능을 구현해보도록 하겠습니다.



우선 `users_edit_test.rb` 코드를 참고하여, 유저 정보를 수정하는 올바른 동작 및 순서를 테스트에서 정의합니다. (이번에는 유효한 정보를 송신하도록 수정해봅니다.) 다음으로는 flash 메세지가 비어있는지 아닌지와, 프로필 페이지에 리다이렉트되는지 아닌지를 체크합니다. 또한 데이터베이스 내의 유저 정보가 제대로 변경되는지를 검증해봅니다. 작성한 코드는 아래와 같습니다. 이 때 아래 코드의 패스워드와 패스워드확인이 비어있는 것을 확인해주세요. 유저 이름과 메일 주소를 편집할 때에 매번 패스워드를 입력하는 것은 불편한 일이기 때문에 (패스워드를 변경할 필요가 없는 때에는) 패스워드를 입력하지 않고 업데이트할 수 있으면 편리할 것입니다. 또한 [6.1.5](Chapter6.md#615-User-Object를-수정해보자) 에서 소개한 `@user.reload` 를 사용하여 데이터베이스로부터 최신의 유저 정보를 읽어 들이고, 제대로 갱신되었는지를 확인할 수 있는 점도 확인해주세요. (이러한 올바른 처리는 보통 까먹는게 보통이긴 하지만, UAT에는 먼저 테스트를 작성하기 때문에, 효과적인 UX에 대해 생각해볼 수 있는 기회가 됩니다.)

```ruby
# test/integration/users_edit_test.rb
require 'test_helper'

class UsersEditTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end
  .
  .
  .
  test "successful edit" do
    get edit_user_path(@user)
    assert_template 'users/edit'
    name  = "Foo Bar"
    email = "foo@bar.com"
    patch user_path(@user), params: { user: { name:  name,
                                              email: email,
                                              password:              "",
                                              password_confirmation: "" } }
    assert_not flash.empty?
    assert_redirected_to @user
    @user.reload
    assert_equal name,  @user.name
    assert_equal email, @user.email
  end
end
```

테스트를 통과할 필요가 있는 아래의 코드에서의 `update` 액션은, `create` 액션과 거의 비슷합니다.

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  .
  .
  .
  def update
    @user = User.find(params[:id])
    if @user.update_attributes(user_params)
      flash[:success] = "Profile updated"
      redirect_to @user
    else
      render 'edit'
    end
  end
  .
  .
  .
end
```

사실 위 코드는 아직 테스트를 통과하지 못하는 상태입니다. 패스워드의 길이에 대한 validation이 있기 때문에, 패스워드나 패스워드 확인란을 비워놓은 채로 테스팅을 하면, 이 validation에 걸립니다. 테스트를 성공시키기 위해서는 패스워드 validation에 대해서, 패스워드가 입력되지 않았을 때의 예외처리를 더할 필요가 있습니다. 이러한 때에 편리한 `allow_nil:true` 라는 옵션이 있습니다. 이것을 `validates` 에 추가해봅시다.

```ruby
# app/models/user.rb

class User < ApplicationRecord
  attr_accessor :remember_token
  before_save { self.email = email.downcase }
  validates :name, presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6 }, allow_nil: true #추가
  .
  .
  .
end
```

위 코드에 의해서 신규 유저 등록 시의 빈 패스워드가 유효하게 되어버리는 것은 아닌지 걱정하는 분도 있을 수도 있습니다만, 그럴 일은 없습니다. [6.3.3](Chapter6.md#633-비밀번호의-최소-문자수) 에서 설명했듯이, `has_secure_password` 에서는 (추가한 validation과 별도로) 오브젝트 생성 시의 존재여부를 검증하게끔 되어 있기 때문에, 빈 패스워드 (`nil`) 이 신규 유저 등록 시에 유효하게 될 일은 없습니다. (빈 패스워드를 입력하면 존재성의 validation과 `has_secure_password` 에 의한 validation이 서로 실행되어, 2개의 같은 에러 메세지가 표시되는 버그가 있습니다만,([7.3.3](Chapter7.md#733-에러-메세지)) 이 것으로 해결되었습니다.)



이 코드를 추가한 것으로 인하여, 유저 정보 수정 페이지가 정상적으로 동작하게 되었습니다. 모든 테스트를 실행시켜 통과하는 것을 확인해보세요.

![](../image/Chapter10/edit_form_working_new.png)

##### 연습

1. 실제로 유저 정보의 수정이 제대로 되는지 확인해봅시다.
2. 만약 Gravatar와 연결되지 않은, 적당한 메일주소(foobar@example.com 등) 으로 변경하는 경우, 프로필 영상은 어떻게 표시됩니까? 실제로 수정 Form에 메일 주소를 변경하여 확인해봅시다.

## 10.2 권한 부여 (Authorization)

