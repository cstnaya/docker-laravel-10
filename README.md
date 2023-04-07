# Docker with laravel 10

您不須再去下載任何軟體及套件，僅需建置此開發環境即可開始開發您的 Laravel 專案。

## 關於此專案

建置 Laravel 10 + Nodejs v18 環境的 dockerfile

1. PHP 8.1
2. Laravel 10
3. Composer
4. Node v18
5. PostgreSQL

## 為什麼需要這份專案

為了避免各工程師在本地電腦開發時發生套件版本衝突問題，以及避免本地與正式環境差異造成程式碼預期外錯誤，故設計此 Docker 建置專案來協助各位在本地建立雲端輔導平台專案的開發環境。

## 什麼是 Docker

Docker 是一個讓開發者不須去各產品公司下載套件與語言包，只需輸入一行指令便能自動生產開發環境系統的工具，若您想更了解 Docker，可於[此閱讀官方文件](https://docs.docker.com/get-started/)，或是閱讀[此入門文章](https://cstnaya33.notion.site/Docker-c5faf77ca8274335a896aeb06fbcd531)。

以下說明該如何使用此專案

## 我們所建立的測試環境

在此 Docker 專案中，我們已為你提供了以下的開發工具：

1. PHP 8.1
2. Node v18
3. PostgreSQL
4. PGAdmin
5. Nginx
6. Laravel 10

請依照以下步驟操作進行環境建置：

## 安裝 WSL2 + Docker Desktop Application (Windows 用戶限定)

若您是 win10, win11 用戶

1. 安裝 wsl，並升級至 version 2
   ```bash
   $ wsl.exe --install
   $ wsl.exe --set-default-version 2
   $ wsl.exe --update

   # 請注意由於 windows 版本變動過於快速，此指令可能往後不再生效，得尋找其他指令來安裝 WSL
   # 若有任何安裝不順，還請以微軟官方教學文件為主
   ```
2. 請至[官網](https://www.docker.com/)安裝 Docker，並重新開機
3. <span style="color: #c22;">注意，若您的作業系統版本過舊 (win7, win8, win10 家用版)，您無法使用 WSL</span>

## 下載此專案

1. 使用 Git clone
   ```bash
   $ git clone <this-project-address>
   ```

## 部署容器

1. 建立 Image
   ```
   $ docker-compose build --build-arg DB_NAME=<db-name-you-want>
   ```
2. 執行 docker-compose 運行容器 (Container)
   ```bash
   $ docker-compose up -d
   ```
   - 如果不加上 `-d`，可以看到正在執行的 log，但 terminal 會變得無法控制
   - `-d` 是讓 docker 在背景執行的意思

## Laravel 專案 前置設定步驟

1. 進入到容器系統中
   ```bash
   $ docker exec -it laravel-10 /bin/ash
   ```
   - `exec`: 表示要對容器下命令
   - `-it`: 表示要與容器互動，通常搭配 `/bin/bash` 一起用
   - `laravel-10`: 容器的名稱，這是我自取的。輸入 `$ docker ps` 可以看所有容器的名稱
   - `/bin/ash`: 若想透過終端跟容器互動，我們需要 Almquist Shell (類似 bash 的東西)；輸入此訊息表示開始使用
2. 安裝依賴套件
    ```bash
    composer install  # 安裝後端依賴套件
    ```
    ```bash
    npm ci  # 安裝前端依賴套件
    ```
3. 確認一下有沒有安裝成功、版本是否正確
   ```bash
   $ php -v              # 若顯示 php 7.4 訊息表示成功
   $ node -v             # 若顯示 node v16 訊息表示成功
   $ composer -v
   $ php artisan list    # 若顯示 Laravel 8 訊息表示成功
   ```
4. 生成 .env 檔
   ```bash
   $ cp .env.example .env
   ```
5. 生成專案金鑰
   ```bash
   $ php artisan key:generate
   $ php artisan jwt:secret          # 如果有用 JWT
   ```
6. 建立資料庫與資料
   ```bash
   php artisan migrate
   ```
7. 匯入假資料
   ```bash
   # pgsql 容器只能接收 /pgsql/data 裡面的檔案，為了匯入假資料，需要把假資料檔放進此資料夾
   # 1. 你現在會在根目錄看到 /pgsql/data 資料夾，以及 db_backup.sql 備份檔 (如果有人準備了假資料)
   # 2. 請複製 db_backup.sql 到 /pgsql/data (用指令或手動複製皆可)

   # 剛剛我們在 laravel 容器底下，現在要移動到 pgsql 容器底下
   $ exit
   $ docker exec -it pgsql /bin/bash
   $ pgsql -U postgres lara-db < db_backup.sql   
   ```

## pgadmin 前置步驟

1. 打開瀏覽器，輸入網址 http://localhost:8005/ 打開 pgadmin
2. 輸入帳號密碼 (寫在 docker-compose 裡)
3. 對著左邊的 Servers 按下右鍵 > Register > Server
4. Name: server; 
5. Connention > Host name: pgsql; user: `postgres`; password: 0000 > 創建

## 開始開發

1. 進入 src 資料夾中
   ```bash
   $ code src
   ```
2. 現在，你可以專注開發這份專案了

## 測試

1. 打開瀏覽器，輸入網址 `localhost:8001` 確認網站是否部署成功
   - 因為我設定 nginx port 是 `8001`，所以才需要輸入 8001
   - nginx 會自動幫我們對應 port 到 Application Server 的 port

## 如果需要進入 nginx 容器

```bash
$ docker exec -it nginx /bin/sh
```

## 注意

- <span style="color: #c22;">這只是開發用 docker，請不要拿去正式部署<span style="color: #c22;"> (因為有很多環境變數是直接 hardcode 在 docker 設定檔內)
- 應該還有很多 Bug 待處理，請求後續維護
- 在此環境建置中使用了 8001, 5432, 8005, 9000 PORT，請確認你的電腦沒有網站佔用這些 PORT，否則會有衝突
- 為了讓它跑得快一點，使用了 alpine 作業系統，`apt` 參數無法使用，請使用 `apk`

## 資料參考

- [How to create Dockerfile for laravel](https://www.youtube.com/watch?v=gZfCAIGsz_o)
- [Github Repo of the video above](https://github.com/hanieas/Docker-Laravel)
- [Github of Laravel 10](https://github.com/laravel/laravel)

專案是參考上述教學建立的，若中途有建置錯誤可看影片除錯。