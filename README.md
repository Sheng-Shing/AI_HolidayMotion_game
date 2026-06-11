# AI Holiday Motion Game

勝行科技智慧團康 AI 的網頁接球遊戲。玩家控制擋板接住落下的彩色球，可使用鍵盤左右鍵操作，也可透過「快樂動」BLE 控制器操作。

## 功能

- 彩色接球遊戲，分數、關卡、生命即時顯示
- 支援鍵盤左右鍵控制擋板
- 支援 Web Bluetooth 連接快樂動控制器
- 左側壓力資料控制左移
- 右側三軸加速度資料控制右移
- 隨關卡提升增加球速、掉落頻率與同時球數

## 使用方式

直接用支援 Web Bluetooth 的瀏覽器開啟 `index.html`。

建議使用：

- Chrome
- Edge

注意事項：

- Web Bluetooth 通常需要 HTTPS 或 localhost 環境。
- iOS 裝置的 Web Bluetooth 支援有限，請依實際瀏覽器能力測試。
- 連接快樂動時，畫面提示「請勿握壓軟球」期間請保持裝置靜止，方便校正初始值。

## 操作

鍵盤操作：

- 左移：`ArrowLeft`
- 右移：`ArrowRight`

快樂動操作：

- 點擊「連接快樂動」
- 連線成功後，狀態顯示為「已連接」
- 握壓紅色球左移
- 搖動右側三軸感測器右移

## BLE 設定

目前使用的 BLE UUID：

```text
Service UUID:        0000fff0-0000-1000-8000-00805f9b34fb
Characteristic UUID: 0000fff2-0000-1000-8000-00805f9b34fb
```

初始化流程：

1. 連接 BLE 裝置
2. 啟用 characteristic notifications
3. 傳送 `BFWLX`
4. 收到 `255` 後傳送 `ST`

## 感測資料格式

一般資料封包中：

- `data[2..4]`：左側壓力值，24-bit big-endian
- `data[5..10]`：右側三軸加速度

三軸加速度解析：

```js
ax = (data[5] * 256 + data[6]) / 2048.0;
ay = (data[7] * 256 + data[8]) / 2048.0;
az = (data[9] * 256 + data[10]) / 2048.0;
```

若任一軸大於等於 `16.0`，會扣除 `32.0` 轉為負值。右側搖動強度會由三軸合成量換算，目前靈敏度比例為 `/ 1000.0`。

## 專案檔案

```text
index.html   遊戲畫面與控制元件
script.js    遊戲邏輯、BLE 連線與感測資料解析
style.css    版面與視覺樣式
todo.md      開發備忘
```

## 開發

此專案不需要建置流程。若要用 localhost 測試，可在專案目錄啟動任一靜態伺服器，例如：

```bash
python3 -m http.server 8000
```

然後開啟：

```text
http://localhost:8000
```
