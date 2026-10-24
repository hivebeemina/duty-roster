# S 值班排班表

一個放在 GitHub Pages 上的值班排班網頁。每週五 17:00 交班，一人值班一週，可以在網頁上調整人員順序、手動換班，並把結果存回 repo 的 `config.json`。

## 功能

- 顯示本週值班人、值班時段和距離交班的倒數時間
- 月曆檢視，每位人員有固定顏色，週五那格會標示 17:00 前後的兩位值班人
- 標示人事行政總處公告的放假日（紅色），並註明假日名稱
- 手動換班：選兩個值班週，互換值班人，其他週次不受影響
- 人員設定：新增、改名、調整順序、刪除，並可指定起點
- 在網頁上修改後，一鍵寫回 GitHub 的 `config.json`

## 檔案

| 檔案 | 說明 |
| --- | --- |
| `index.html` | 整個網頁（單一檔案，不需要建置） |
| `config.json` | 人員順序、起點、換班紀錄 |
| `README.md` | 本說明 |

## 值班規則

- 每週五 17:00 換班，例如 10/23(五) 17:00 到 10/30(五) 17:00 為同一位
- 預設順序是 `s4 → s7 → s6 → s1 → s2 → s5 → s3`，輪完一圈後回到 `s4`
- 起點：2026/10/23(五) 這一週由 `s6` 值班
- 換班只影響你選的兩週，換完之後其他週次仍依原本順序輪值

## 部署到 GitHub Pages

1. 建立一個新的 repository（Public）。
2. 把 `index.html` 和 `config.json` 放在 repository 的根目錄。
3. 到 **Settings → Pages**，Source 選 **Deploy from a branch**，Branch 選 `main`，資料夾選 `/ (root)`，按 **Save**。
4. 等一到兩分鐘，網址會是 `https://<你的帳號>.github.io/<repo 名稱>/`。

## 設定檔 `config.json`

```json
{
  "version": 1,
  "cycle": ["s4", "s7", "s6", "s1", "s2", "s5", "s3"],
  "anchor": 2,
  "swaps": [
    { "a": "2026-10-30", "b": "2026-11-27" }
  ]
}
```

| 欄位 | 說明 |
| --- | --- |
| `cycle` | 輪值順序，同一人可出現多次 |
| `anchor` | 2026/10/23 這一週對應 `cycle` 的第幾位，從 0 開始算。`2` 代表第三位（`s6`） |
| `swaps` | 換班紀錄，依序套用。`a`、`b` 是兩個值班週開始的週五日期，這兩週的值班人互換 |

上面的例子表示：10/30 那週原本是 `s1`，11/27 那週原本是 `s4`，兩週互換後，10/30 由 `s4` 值班，11/27 由 `s1` 值班。

可以直接編輯這個檔案，也可以在網頁上修改後儲存。

## 在網頁上修改並儲存

只有設定了 token 的人才能把修改寫回 GitHub，其他人看到的永遠是 repo 裡最新的 `config.json`。

1. 到 GitHub 的 **Settings → Developer settings → Personal access tokens → Fine-grained tokens**，新增一組 token。
2. **Repository access** 選 **Only select repositories**，只選這個 repo。
3. **Permissions** 的 **Contents** 設為 **Read and write**，產生 token。
4. 打開網頁，到「同步設定」貼上 token，按「儲存設定」。帳號與 repo 名稱在 `github.io` 網址下會自動帶入。
5. 修改人員或換班後，畫面下方會出現提示列，按「儲存到 GitHub」。repo 會多一筆 commit，GitHub Pages 約 1–2 分鐘後更新。

注意事項：

- token 只存在你自己的瀏覽器（localStorage），不會寫進 repo 或網頁程式碼，請勿把 token 放進任何檔案
- 儲存前會檢查 `config.json` 有沒有被別人改過，如果有，會要求先重新載入，避免覆蓋他人的修改
- 沒有設定 token 時，修改只是暫時預覽，重新整理後就會消失
- 如果 repo 是 Public，人員名稱與換班紀錄任何人都看得到

## 放假日資料

- 連網時，頁面會依你查看的年份，從開源專案 [TaiwanCalendar](https://github.com/ruyut/TaiwanCalendar) 載入該年度的政府行政機關辦公日曆。這份資料整理自政府資料開放平台，人事行政總處通常在每年 6 月公布下一年度的日曆
- 連不上時，會使用 `index.html` 內建的 2026、2027 年資料
- 這是政府行政機關的放假日，不等同勞基法規定的民間企業假日；假日只作標示，不會改變輪值

## 在本機預覽

頁面需要讀取 `config.json`，直接用瀏覽器開啟檔案可能讀不到，請用簡單的本機伺服器：

```bash
python3 -m http.server 8000
```

然後開啟 <http://localhost:8000>。

## 已知限制

- 日期與時間以瀏覽器所在時區計算，預設情境是台灣時區
- 同時有兩個人修改時，後儲存的人會收到衝突提示，需要重新載入後再改
- Private repo 使用 GitHub Pages 需要付費方案
