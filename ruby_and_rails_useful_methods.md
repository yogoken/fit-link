Ruby文法例

```
Array#map
Array#select
Array#find
Array#empty?
Array#<<
ActiveRecord#find
ActiveRecord#find_by
ActiveRecord#map(&:method)
ActiveRecord#pluck
Object#try
Object#||=
Object#nil?
Object#blank?
Object#present?
三項演算子
倒置のif、unless
Enumerable#index_by
Enumerable#each_with_index
Enumerable#each_with_object
Enumerable#any?
new_record?
persisted?
```

参考文献
junichi ito: http://qiita.com/jnchito/items/dedb3b889ab226933ccf#nil%E3%83%81%E3%82%A7%E3%83%83%E3%82%AF%E3%81%AE%E4%BB%A3%E3%82%8F%E3%82%8A%E3%81%ABobjecttrymethod_name%E3%82%92%E4%BD%BF%E3%81%86

qiita: http://qiita.com/kidach1/items/a00558cfb0a6a3e23f4b

ちだっち: http://qiita.com/hc0208/items/45cce0f3f3c843c03c01

reference: http://ref.xaio.jp/ruby/classes/array/append

## Array#map

ある配列から別の配列を作る
```
names = users.map{ |user| user.name}
names = users.map(&:name)
```

```
def user_names(users)
  names = []
  users.each do |user|
    names << user.name
  end
  names
end
```
↓

```
def user_names(users)
  users.map(&:name)
end
```

## Array#select

```
def find_admins(users)
  admins = []
  users.each do |user|
    admins << user if user.admin?
  end
  admins
end
```
↓

```
def find_admins(user)
  users.select(&:admin?)
end
```

selectとは逆にfalseだけ返すのはreject
```
def find_admins(user)
  users.reject(&:admin?)
end
```




## Array#find

```
def find_admin(users)
  users.each do |user|
    return user if user.admin?
  end
  nil
end
```
↓
```
def find_admin(users)
  users.find(&:admin?)
end
```

## Array#empty?
一見もなければtrueを返す

```
puts "empty!" if users.size == 0
↓
puts "empty!" if users.empty?
```

## Array#<<
追加

```
animals = ["dog", "cat", "mouse"]
animals << "pig" << "duck"
p animals

["dog", "cat", "mouse", "pig", "duck"]

```


## ActiveRecord#find



## ActiveRecord#find_by

## ActiveRecord#map(&:method)

## ActiveRecord#pluck
pluckを使うと必要なカラムだけをデータベースから取得するので処理効率が良くなります。

```
[2] pry(main)> User.where(admin: true).map(&:id)
  User Load (0.5ms)  SELECT `users`.* FROM `users`  WHERE `users`.`admin` = 1
=> [1, 2]
[3] pry(main)> User.where(admin: true).pluck(:id)
   (4.7ms)  SELECT `users`.`id` FROM `users`  WHERE `users`.`admin` = 1
=> [1, 2]
[4] pry(main)>
```

## Object#try

```
if parent.children && parent.children.singleton?
  singleton = parent.children.first
  send_mail_to(singleton)
end
↓

# childrenがnilならtry(:singleton?)はnilを返す
# nilでなければ、children.singleton?が普通に呼ばれる
if parent.children.try(:singleton?)
  singleton = parent.children.first
  send_mail_to(singleton)
end
```



## Object#||=
nilだったら初期化、の代わりに||=を使う

```
def twitter_client
  @twitter_client = Twitter::REST::Client.new if @twitter_client.nil?
  @twitter_client
end
↓
def twitter_client
  @twitter_client ||= Twitter::REST::Client
end
```


## Object#nil?
## Object#blank?
## Object#present?
```
nil.blank? # => true

# String
name = nil
name.blank? # => true
name = ""
name.blank? # => true
name = " "
name.blank? # => true
name = "Tom"
name.blank? # => false

# Array
numbers = nil
numbers.blank? # => true
numbers = []
numbers.blank? # => true
numbers = [1, 2, 3]
numbers.blank? # => false

# Hash
params = nil
params.blank? # => true
params = {}
params.blank? # => true
params = { name: "Tom", email: "hoge@hoge.com" }
params.blank? # => false

# String
name = ""
name.present? # => false
name = "Tom"
name.present? # => true
```

## 三項演算子

```
if user.admin?
  "I appreciate for that."
else
  "Thanks."
end
```
↓
```
user.admin? ? "I appreciate for that." : "Thanks"
```


## 後置のif、unless

```
if user.active?
  send_mail_to(user)
end
↓
send_mail_to(user) if user.active?

```

```
user.destroy if !user.active?
↓
user.destroy unless user.active?
```




## Enumerable#index_by
参考: http://qiita.com/QUANON/items/c3582a55949d1e6a1e84



#### Railsでオブジェクトの配列を簡単にハッシュ化したい

```
User.all.inject({}) {|hash, user| hash[user.nickname] = user; hash }
```
↓

```
[3] pry(main)> user_info = User.all.index_by {|user| user.nickname}
  User Load (0.4ms)  SELECT `users`.* FROM `users`
{
       "yogoken" => #<User:0x007fc2737e0058> {
                            :id => 1,
                         :email => "qwerty@gmail.com",
            :encrypted_password => "$2a$11$FwyLCSY.yznUQexyYigUk.mO/uIMSV/EJ1IJmaRqifFnCS8g5C1sq",
          :reset_password_token => nil,
        :reset_password_sent_at => nil,
           :remember_created_at => nil,
                 :sign_in_count => 16,
            :current_sign_in_at => Wed, 14 Dec 2016 07:42:23 UTC +00:00,
               :last_sign_in_at => Tue, 29 Nov 2016 08:03:56 UTC +00:00,
            :current_sign_in_ip => "::1",
               :last_sign_in_ip => "::1",
                    :created_at => Tue, 08 Nov 2016 07:15:34 UTC +00:00,
                    :updated_at => Thu, 15 Dec 2016 05:23:06 UTC +00:00,
                      :nickname => "yogoken",
                        :avatar => #<AvatarUploader:0x007fc272e83cd0 @model=#<User id: 1, email: "qwerty@gmail.com", created_at: "2016-11-08 07:15:34", updated_at: "2016-12-15 05:23:06", nickname: "yogoken", avatar: "yogoken.png", admin: 1>, @mounted_as=:avatar, @storage=#<CarrierWave::Storage::File:0x007fc272e83aa0 @uploader=#<AvatarUploader:0x007fc272e83cd0 ...>>, @file=#<CarrierWave::SanitizedFile:0x007fc272e83488 @file="/Users/kenta/div/oriapp/pic/public/uploads/user/avatar/1/yogoken.png", @original_filename=nil, @content_type=nil>, @versions={}>,
                         :admin => 1
    },
         "messi" => #<User:0x007fc27336fcf8> {
         
```

or 

```
User.all.index_by(&:nickname)
```

## Enumerable#each_with_index

```
[7] pry(main)> %w(messi xavi boku).each_with_index{|magica, i| puts "#{i+1} #{magica}"}
1 messi
2 xavi
3 boku
=> ["messi", "xavi", "boku"]
[8] pry(main)> %w(messi xavi boku).each.with_index(1){|magica, i| puts "#{i} #{magica}"}
1 messi
2 xavi
3 boku
=> ["messi", "xavi", "boku"]
```

## Enumerable#each_with_object
参考文献: http://qiita.com/satoruk/items/40bb97ca410e5ca10532


```
months = {
  jan: "1月", feb: "2月", mar: "3月", apr: "4月", may: "5月", jun: "6月",
  jul: "7月", aug: "8月", sep: "9月", oct: "10月", nov: "11月", dec: "12月"
}

### Enumerable#each_with_object
  keys = [:jan, :jun, :jul]
  keys.each_with_object({}){|k, hash| hash[k] = months[k] if months.key?(k) }
  # => {:jan=>"1月", :jun=>"6月", :jul=>"7月"}
### Hash#select
  months.select {|k,_| [:jan, :jun, :jul].include?(k) }
  # => {:jan=>"1月", :jun=>"6月", :jul=>"7月"}
### Hash#slice : active_support
  months.slice(:jan, :jun, :jul)
  # => {:jan=>"1月", :jun=>"6月", :jul=>"7月"}
```


## Enumerable#any?
参考文献: http://qiita.com/kidach1/items/a00558cfb0a6a3e23f4b

```
> [true, true, false].any?
=> true
> [false, false, false].any?
=> false
```

## new_record? & persisted?
参考文献: http://ruby-rails.hatenadiary.com/entry/20150101/1420049679

DBに保存されていないか(new_record?)、DBに保存されているか(persisted?)
```
# DBに保存されていない値
u = User.new
u.new_record? # => true
u.persisted?  # => false

# DBに保存されている値
u = User.first
u.new_record? #=> false
u.persisted?  #=> true
```
