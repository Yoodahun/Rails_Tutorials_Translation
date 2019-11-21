# 제 11장 Account의 유효화

현 시점에서 어플리케이션은, 신규 등록한 유저가 처음부터 모든 기능에 접근할 수 있게 되어 있습니다.([제 7장](Chapter7.md)) 이번 챕터에서는, account를 유효화시키는 스텝을 유저 신규 등록의 도중에 집어넣을 것 입니다. 정말로 해당 메일 주소의 주인인지 아닌지를 확인해볼 것이빈다. 이것을 실현시키기 위한 순서로는 

1. 유효화 토큰이나 digest를 관련지은 상태에서
2. 유효화 토큰을 포함한 링크를 유저의 메일로 송신하고
3. 유저가 해당 링크를 클릭하면 유효화되게끔 하는

순서가 될 것이빈다. 또한 제 12장에서도 비슷한 흐름으로 유저가 패스워드를 잊어버렸을 때, 패스워드를 재설정하는 기능을 구현해볼 것 입니다. 이러한 기능들은 새로운 리소스로 생성하고, 컨트롤러, 라우팅, 데이터베이스로의 이행에 대해 하나씩 배워볼 것 입니다. 마지막으로 Rails의 개발환경이나 실제 배포환경에서 실제로 메일을 송신해볼 것 입니다.



account를 유효화하는 방법은 유저 로그인([8.2](Chapter8.md#82-Login)), 유저를 기억하는 기능 ([9.1](Chapter9.md#91-Remember-me-기능)) 과 비슷합니다. 기본적인 순서는 다음과 같습니다.

1. 유저의 초기 상태로는 "유효화 되어있지 않은(*unactivated*)" 상태를 해놓는다.
2. 유저 등록 중에 유효화 토큰과, 그것에 대응하는 유효화 digest를 생성한다.
3. 유효화 digest는 데이터베이스에 저장하고, 유효화 토큰은 메일주소와 함께 유저에게 보내는 유효화용 메일의 링크에 같이 기재한다.
4. 유저가 메일의 링크를 클릭하면, 어플리케이션은 메일 주소를 키로하여 유저를 찾고, 데이터베이스 내부에 저장해놓은 유효화 digest와 비교하여 토큰을 인증한다.
5. 유저를 인증했다면 유저의 상태를 "유효화 되어있지 않은" 상태에서 "유효화 된 *(activated)*" 상태로 변경한다.

매우 감사하게도, 이번에 구현하는 account 유효화와 패스워드 재설정의 구조는, 이전에 구현해본 패스워드나 Remember 토큰의 구조와 매우 닮아 있기 때문에, 많은 아이디어를 재활용할 수 있습니다. (구체적으로는 `User.digest` 나 `User.new_token` , 개조한 `user.authenticated?` 메소드 등) 다음표에 각각의 구조의 닮은 점을 정리해보았습니다. (제 12장에서 소개할 메소드도 포함되어 있습니다.)

| 검색 Key | **string**         | **digest**          | **authentication**                   |
| -------- | ------------------ | ------------------- | ------------------------------------ |
| `email`  | `password`         | `password_digest`   | `authenticate(password)`             |
| `id`     | `remember_token`   | `remember_digest`   | `authenticated?(:remember, token)`   |
| `email`  | `activation_token` | `activation_digest` | `authenticated?(:activation, token)` |
| `email`  | `reset_token`      | `reset_digest`      | `authenticated?(:reset, token)`      |

 이번 11.1에서는 account 유효화에 필요한 리소스나 데이터 모델을 생성해볼 것입니다. 또한 11.2에서는 메일러(*mailer*) 를 사용하여 account 유효화 시의 메일 송신 부분을 작성해볼 것 입니다. 마지막으로 위 표에서 소개한 개량버전의 `authenticated?` 메소드를 사용하여 실제로 account를 유효화하는 부분을 구현해볼 것 입니다. (11.3)



## 11.1 Account Activations Resource

세션기능([8.1](Chapter8.md#81-Session)) 을 사용하여, account의 유효화라는 작업을 "리소스" 라는 단위로 모델화해보겠습니다.  account의 유효화 리소스는 Active Record의 모델과는 관계가 없기 때문에, 양쪽을 관련짓는 것은 하지 않겠습니다. 대신에 이 작업에 필요한 데이터 (유효화 토큰이나 유효화 스테이터스 등)을 User 모델에 추가해보겠습니다.



또한 Account 유효화도 리소스로 다루고 싶습니다만, 다른 때와는 조금 다르다는 점을 미리 알아두시길 바랍니다. 예를 들어 유효화용의 링크에 액세스하여 유효화 상태를 변경하는 부분에서는, REST의 규칙을 따른다면 PATCH 리퀘스트와 `update` 액션을 사용해야할 것 입니다. 그러나 유효화 링크는 메일로 유저에게 보내진다는 것을 떠올려주세요. 유저가 이 링크를 클릭하면, 그것은 브라우저에서 보통 클릭했을 때와 같은 것이며, 그때에는 브라우저에서 송신되어 지는 것은 (`update` 액션에서 사용하는 PATCH 리퀘스트가 아닌) GET 리퀘스트가 되어버립니다. 때문에 유저로부터 GET 리퀘스트를 받기 위해 (원래라면 `update` 이겠지만) `edit` 액션으로 변경하여 사용해볼 것입니다.



언제나처럼 Git에서 새로운 기능을 위한 토픽 브랜치를 생성해봅시다.

`$ git checkout -b account-activation`

### 11.1.1 Account Activations Controller

User 리소스나 Sessions 리소스때와 마찬가지로, AccountActivations 리소스를 만들기 위해, 우선 AccountActivations 컨트롤러를 생성해봅시다.

`$ rails generate controller AccountActivations`

11.2.1 에서 좀 더 상세히 설명하겠습니다만, 유효화 메일에는 다음의 URL을 포함할 것 입니다.

```
edit_account_activation_url(activation_token, ...)
```

 이것은 `edit` 액션으로의 named root가 필요하다는 것입니다. 우선은 named 루트를 다룰 수 있게 하기 위해, 아래 표처럼 라우팅에 account 유효화용 `resources` 행을 추가합니다.

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root   'static_pages#home'
  get    '/help',    to: 'static_pages#help'
  get    '/about',   to: 'static_pages#about'
  get    '/contact', to: 'static_pages#contact'
  get    '/signup',  to: 'users#new'
  get    '/login',   to: 'sessions#new'
  post   '/login',   to: 'sessions#create'
  delete '/logout',  to: 'sessions#destroy'
  resources :users
  resources :account_activations, only: [:edit] #추가
end
```

일단 여기까지 해놓고, account 유효화용 데이터 모델과 메일러를 생성해볼 것 입니다만, 그게 끝나면 여기서 생성한 리소스를 기반으로하여 `edit` 액션을 정의해볼 것 이빈다. (11.3.2)

##### 연습

1. 현 시점에서 테스트 코드를 실행하면 통과하는 것을 확인해보세요.
2. 위 표에서 Named Root에서는 `_path` 가 아닌 `_url` 이 생성되어 있습니다. 왜일까요? *Hint* : 이것을 메일에서 사용할 것 입니다.

### 11.1.2 Account Activation DataModel

앞서 살짝 말씀드렸다시피, 유효화의 메일에는 유니크한 유효화토큰이 필요합니다. 바로 생각나는거정도는 송신메일과 데이터베이스 각각에 같은 문자열을 준비하는 방법입니다. 그러나 이 방법은 만에 하나 데이터베이스의 내용이 유출된다면 막대한 피해로 이어질 수 있습니다. 예를 들어 공격자가 데이터베이스로의 액세스에 성공했을 경우, 새롭게 등록하는 유저 계정의 유효화 토큰을 훔쳐내어, 유저가 사용하기 전에 해당 토큰을 사용해버리는  (그리고 해당 유저라고 속이고 로그인해버립니다) 케이스를 생각해볼 수 있습니다.



이러한 사태를 막기 위해, 패스워드의 구현 ([제 6장](Chapter6.md)) 이나 Remember 토큰 ([제 9장](Chapter9.md)) 과 같이 가상의 속성을 사용하여 해시화한 문자열을 데이터베이스에 저장해볼 것 입니다. 구체적으로는 다음과 같이 가상의 유효화 토큰에 액세스하여

`user.activation_token`

아래와 같은 코드로 유저를 인증할 수 있게 해볼 것 입니다.

`user.authenticated?(:activation, token)`

(위 코드를 하기 위해서는 이전 9장에서의 `authenticated?` 메소드를 리팩토링할 필요가 있습니다.)



이어서 `activated` 속성을 추가하여 논리값을 다루도록 해봅시다. 이것으로 [10.4.1](Chapter10.md#1041-관리자) 에서 설명했던 자동생성되는 논리값을 다루는 메소드와 비슷한 느낌으로 유저가 유효한 유저인지 아닌지를 테스트해볼 수 있을 것 입니다.

`if user.activated?`

마지막으로 본 튜토리얼에서 사용하는 것은 아니지만, 유저를 유효하게 했을 때의 시간도 기록해두도록 해봅시다. 변경후의 데이터 모델은 아래와 같습니다.

![](../image/Chapter11/user_model_account_activation.png)

다음으로 마이그레이션을 커맨드라인에서 실행하여, 데이터 모델을 추가하면, 3개의 속성이 새롭게 추가될 것 입니다.

```
$ rails generate migration add_activation_to_users \
> activation_digest:string activated:boolean activated_at:datetime
```

([8.2.4](Chapter8.md#824-레이아웃의-변경을-테스트해보자) 에서도 말씀드렸습니다만, 위의 2번째 줄에 있는 `>` 는 개행을 나타내기 위해 쉘이 자동적으로 입력한 문자입니다. 수동으로 입력하지 않도록 주의해주세요.) 다음으로 `admin` 속성을 추가할 때와 마찬가지로, `activated` 속성의 디폴트 논리값도 `false` 으로 해놓습니다.

```ruby
class AddActivationToUsers < ActiveRecord::Migration[5.0]
  def change
    add_column :users, :activation_digest, :string
    add_column :users, :activated, :boolean, default: false
    add_column :users, :activated_at, :datetime
  end
end
```

언제나처럼 마이그레이션을 실행합니다.

`$ rails db:migrate`

#### Activation token의 Callback

유저가 새롭게 등록을 완료하기 위해서 반드시 account의 유효화를 할 필요가 있기 때문에, 유효화 토큰이나 유효화 digest는 유저 오브젝트가 생성되기 전에 만들어놓을 필요가 있습니다. 이것과 비슷한 상황을 [6.2.5](Chapter6.md#625-유니크성을-검증해보자) 에서도 설명한 적이 있습니다. 메일 주소를 데이터베이스에 저장하기 전에, 메일 주소를 전부 소문자로 변환할 필요가 있었습니다. 그 때에는 `before_save` 콜백에 `downcase` 메소드를 지정하였습니다. 오브젝트에 `before_save` 콜백을 준비해놓으면, 오브젝트가 저장되기 직전, 오브젝트의 생성시나 수정 시의 해당 콜백이 호출됩니다. 그러나 이번에는 오브젝트가 생성되었을 떄만 콜백을 호출하고 싶습니다. 그 외의 때에는 호출하고 싶지 않습니다. 여기서 `before_create` 콜백이 필요하게 됩니다. 이 콜백은 다음과 같이 정의할 수 있습니다.

```
before_create :create_activation_digest
```

위 코드는 *메소드 참조* 라고 불리는 것인데, 이렇게 해놓으면 Rails는 `create_activation_digest` 라고하는 메소드를 찾아 유저를 생성하기 전에 실행하게 됩니다. (6장에서는 `before_save` 에 명시적으로 블록을 전달하고 있습니다만, 사실 메소드를 참조하는 편을 추천합니다.)

`create_activation_digest` 메소드 자체는 User 모델 내부에서밖에 사용하지 않기 때문에, 외부에 공개할 필요는 없습니다. [7.3.2](Chapter7.md#732-Strong-Parameters) 와 마찬가지로 `private` 키워드를 지정하여 해당 메소드를 은폐합니다.

```ruby
private

  def create_activation_digest
    # 유효화 토큰과 Digest의 생성 및 대입을 한다.
  end
```

클래스 안에서의 `private` 키워드로 인해 아래 작성한 메소드는 자동적으로 비공개가 됩니다. 이것은 콘솔에서도 바로 확인할 수 있습니다.

```ruby
$ rails console
>> User.first.create_activation_digest
NoMethodError: private method `create_activation_digest' called for #<User>
```

이번 `before_create` 콜백을 사용하는 목적은, 토큰과 그것에 대응하는 Digest를 할당하기 위해서입니다. 실제로 할당은 아래와 같습니다.

```ruby
self.activation_token  = User.new_token
self.activation_digest = User.digest(activation_token)
```

이 코드에서는 Remeber 토큰과 Remember Digest를 위해 작성한 메소드를 재활용합니다. 9장에서의 `remember` 메소드와 비교해봅시다.

```ruby
# 영속적인 세션을 위해 유저를 데이터베이스에 저장한다.
def remember
  self.remember_token = User.new_token
  update_attribute(:remember_digest, User.digest(remember_token))
end
```

주된 차이점은, 후자의 `update_attribute`의 사용법에 있습니다. 이 차이는, Remember 토큰과 Digest는 이미 데이터베이스에 존재하는 유저를 위해 생성하는 것에 비해, `before_create`  콜백은 유저가 생성되기 *전* 에 호출되기 때문입니다. 이 콜백의 존재로 인하여 `User.new` 로 새로운 유저를 정의하면, `activation_token` 속성이나 `activation_digest` 속성을 확인할 수 있게 되는 것입니다. 또한 후자의 `activation_digest` 속성은 이미 데이터베이스의 컬럼과 관련지어져있기 때문에, 유저가 저장될 때 같이 저장됩니다.

