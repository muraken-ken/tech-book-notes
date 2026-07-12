# Rubyスクレイピング
## 動作環境
```
 - Ubuntu 20.04 LTS on Windows10 WSL2
 - Ruby 3.0.2
```
## セットアップ 
### Google Chromeのインストール  
リポジトリの追加する  
1. `$ sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'`  
公開鍵をダウンロードして登録する
1. `$ sudo wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -`  
リポジトリの更新とインストール  
1. `$ sudo apt update`  
1. `$ sudo apt install google-chrome-stable`  

### chromedriverのインストール  
- [公式サイト](https://sites.google.com/a/chromium.org/chromedriver/downloads)  
インストールされているchromeのバージョンに合わせDLする  
1. `$ google-chrome -version`  
1. `$ wget https://chromedriver.storage.googleapis.com/86.0.4240.22/chromedriver_linux64.zip`  
unzipで解凍する  
1. `$ unzip chromedriver_linux64.zip`  
以下のフォルダに移動する  
1. `$ sudo mv chromedriver /usr/bin`  
移動されたことを確認する  
1. `$ which chromedriver`  
DLしたファイルを削除する  
1. `$ rm chromedriver_linux64.zip`  

## gemのセットアップ
### Gemfileの作成とbundle install  
作業フォルダに移動する  
1. `$ bundle init`  
1. 作成されたgemファイルに以下を記載する  
`Gemfile`  
```
  source "https://rubygems.org"  
      
  git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }  
      
  gem 'capybara'
  gem 'selenium-webdriver'
  gem 'launchy'
```  
1. capybaraの初期設定    
`scrape.rb`  
```
  require 'capybara'
  require 'selenium-webdriver'

  Capybara.register_driver :selenium do |app|
    Capybara::Selenium::Driver.new(app,
      browser: :chrome,
      desired_capabilities: Selenium::WebDriver::Remote::Capabilities.chrome(
        chrome_options: {
          args: %w(headless disable-gpu window-size=1280,800),
          w3c: false
        },
      )
    )
  end
  Capybara.javascript_driver = :selenium
```
1. 使用例  
`scrape.rb`  
```
  def start_scraping(url, &block)
    Capybara::Session.new(:selenium).tap { |session|
      session.visit url
      session.instance_eval(&block)
    }
  end

  start_scraping 'https://www.google.com/' do
    # ここにスクレイピングのコードを書く
    p title #=> "Google"
    save_page
  end
```