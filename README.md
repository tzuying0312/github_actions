# Github Actions

## Table of contents
- [Simple Example](#simple-example)
- [Slack Example](#slack-example)
  
## Simple Example

1. 先至 https://github.com/new 建立一個新的 Repo。
  <img src="https://i.imgur.com/eFZqLgl.png" alt="Graph" width="65%"/>

2. 點擊 `Actions > Configure` 獲得一個簡單的範例。
  ![](https://i.imgur.com/aFCr6dQ.png)

3. 會看到在 `.github/workflows/` 路徑底下會被建立一個 `.yml`。
  ![](https://i.imgur.com/sGBAl4w.png)

4. `.yml` 說明。
    ```yml
    # .github/workflows/blank.yml
    # 此 workflow 的名字叫 Get started
    name: Get started

    # 觸發條件
    on:
      # 當 push 到 main 分支時觸發
      push:
        branches: [ "main" ]

      # 是否可以手動觸發，如果有寫的話就可以在 github 頁面中手動執行，不要的話就不用寫
      workflow_dispatch:

    # 執行一項或多項任務
    jobs:
      # 此 jobs 中有個叫 build 的工作，也可以改成其他
      build:
        # run 在什麼 server
        runs-on: ubuntu-latest

        # 指定 job 要執行哪些步驟
        steps:
          # 很多預設都會寫 checkout@v3，可以想成是 github 的 API
          - uses: actions/checkout@v3

          # 任務名為 Run a one-line script
          - name: Run a one-line script
            # 執行 echo
            run: echo Hello, world!

          # 任務名為 Run a multi-line script
          - name: Run a multi-line script
            # 執行 echo
            run: |
              echo Add other actions to build,
              echo test, and deploy your project.

    ```

5. 完成之後先點擊 `Start commit`，再點擊 `Commit new file`。
  ![](https://i.imgur.com/RuZBxJW.png)

6. 可以再回到 `Actions` ，可以看到左方有個 `Get started`，這是在 `.yml` 中所建立的 `name`，圖中有個橘色的點，代表正在執行我們的 workflow，而 `Create blank.yml` 為我們此次的 commit。
  ![](https://i.imgur.com/pGPkoNI.png)

7. 執行成功後，原本的橘點會變成綠色。
  ![](https://i.imgur.com/1MI5RuA.png)

8. 可以點擊上圖中的 `Create blank.yml` 來查看執行結果。
  ![](https://i.imgur.com/bnEbT4Q.png)

9. 接下來只要我們每一次有 git push 新的內容，都會觸發這個 workflow。同時，剛在 `.yml` 中有設定 `workflow_dispatch:`，因此我們也可以手動來觸發。
  ![](https://i.imgur.com/lEv5del.png)

## Slack Example
在開發專案時，會由不同人執行，因此透過 Slack 來自動通知有人 push。

- slack-notify 官方文件 https://github.com/marketplace/actions/slack-notify

1. 首先要申請一個 slack 的 webhook，網址：https://slack.com/apps/A0F7XDUAZ--webhook?tab=more_info ，申請完後會獲得一組 Webhook Url，先將它記下來。
  ![](https://i.imgur.com/hCbyk8S.png)

2. 由於隱私問題，不適合將它直接放入至 `.yml` 中，因此可以透過 Github Secrets 來存。`Settings > Secrets > Action > New repository secret`。
  ![](https://i.imgur.com/Pj0h7ns.png)


3. 將剛剛獲得的 Webhook Url 貼到 `Secret` 中，`Name` 則是取名為 `SLACK_WEBHOOK`，完成後點擊 `Add secret` 就建立完成，未來在 `.yml` 中可以透過 `secrets.SLACK_WEBHOOK` 來取用。
  ![](https://i.imgur.com/pp3kkC6.png)

4. 事前準備都完成後，一樣建立一個 `.yml` ， 並放在 `.github/workflows/` 路徑底下。 `.yml` 說明。
    ```yml
    # .github/workflows/slack-notify.yml
    name: Slack Notification Demo
    on:
      # Triggers the workflow on push or pull request events but only for the "main" branch
      push:
        branches: [ "main" ]

    # 執行一項或多項任務
    jobs:
      slackNotification:
        name: Slack Notification
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Notification
          uses: rtCamp/action-slack-notify@v2
          env:
            # 這邊就是取用剛剛我們在 Github Secrets 建立的 Webhook
            SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
    ```

5. `git push` 推上至 github，就可以在 slack 中收到通知。
  ![](https://i.imgur.com/FCxfbsm.png)