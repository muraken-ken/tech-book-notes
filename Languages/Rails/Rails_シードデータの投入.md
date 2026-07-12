# シードデータの投入
## 手順
1. seeds.rbへの記載
2. シードデータの記載
3. 実行

## 1. `seeds.rb`への記載
- `app/db/seeds.rb`
```
  tabel_names = %w(foo bar baz)

  tabel_names.each do |tabel_name|
    path = Rails.root.join('db', 'seeds', Rails.env, "#{tabel_name}.rb")
    if File.exist?(path)
      puts "Creating #{tabel_name}...."
      require(path)
    end
  end
```

## 2. シードデータの記載
- `app/db/development/foo.rb`
  - 1件の場合
  ```
  Foo.create!(
    email:            'taro@example.com',
    family_name:      '山田',
    given_name:       '太郎',
    family_name_kana: 'ヤマダ',
    given_name_kana:  'タロウ',
    password:         'password',
    start_date:       Date.today
  )
  ```

  - 複数の組み合わせの場合
  ```
    family_names = %w{
      佐藤:サトウ:sato
      鈴木:スズキ:suzuki
      高橋:タカハシ:takahashi
      田中:タナカ:tanaka
    }

    given_names = %w{
      二郎:ジロウ:jiro
      三郎:サブロウ:saburo
      松子:マツコ:matsuko
      竹子:タケコ:takeko
      梅子:ウメコ:umeko
    }

    20.times do |n|
      fn = family_names[n % 4].split(':')
      gn = given_names[n % 5].split(':')

      StaffMember.create!(
        email: "#{fn[2]}.#{gn[2]}@example.com",
        family_name: fn[0],
        given_name: gn[0],
        family_name_kana: fn[1],
        given_name_kana: gn[1],
        password: 'password',
        start_date: (100 - n).days.ago.to_date,
        end_date: n == 0 ? Date.today : nil,
        suspended: n == 1
      )
    end
  ```

## 3. 実行
```
$ bin/rails db:seed
$ bin/rails db:reset  # rails db:drop + db:setup + db:seed => schema.rbから生成
$ bin/rails db:migrate:reset  # データベースの初期化（マイグレーション再実行）
```
