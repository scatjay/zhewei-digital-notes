# 交接文件 — 清大工作坊配套網站（張哲維老師）

> 寫於 2026-09-19 11:46（12:00更新），工作坊Day2現場即時協作中。**楊老師明確交代：接手的機器
> 「幾乎是空的」——不是同一台電腦換一個session，是換一台新筆電。**這代表：
> - ✅ **git repo本身會活下來**：`git clone https://github.com/scatjay/zhewei-digital-notes.git`
>   拿得到已經部署上線的一切（開幕式~圓桌一四場完整內容、NBLM三套件、本檔）。
> - ❌ **`E:/Downloads/`底下沒進git的東西全部不會跟著走**：faster-whisper的GPU/CUDA環境、
>   原始錄音檔、中繼轉錄JSON、`E:/Downloads/歷史上的今天`(CDP瀏覽器工具)、
>   `E:/Downloads/nblm-orchestrator`(NBLM管線)、rclone、LibreOffice。
> - **因此這份文件已經把所有還沒進repo的關鍵產出搬進了`_work_in_progress/`並commit——
>   接手時第一件事是`git pull`，不是去找E槽的舊路徑。**
>
> 開場先讀這份，再看要不要讀`_docs/學者與專有名詞總檢查_0918.md`（Day1名詞查證報告）跟
> 本檔最下面的「常用指令」區塊跟「新機器要重建什麼」清單。

## 0. 這是什麼專案

清大「歷史學的智慧人文轉向：運用生成式AI優化歷史研究流程工作坊」（2026-09-18~19，國科會人文處歷史
學門主管會議）的配套學習單網站。講者是**張哲維老師**（國立成功大學歷史學系助理教授）。網站：
**https://scatjay.github.io/zhewei-digital-notes/**，GitHub Pages自動部署，楊智傑老師（使用者，
以下稱「楊老師」）在工作坊現場即時看網站、即時回報問題。

核心工作流程（每一場都走一遍，已寫成skill `~/.claude/skills/zhewei-session-processing/SKILL.md`）：
Drive下載錄音 → faster-whisper large-v3本機GPU轉錄 → 簡轉繁(opencc s2twp) → 投影片頁碼對齊+潤飾
(agent視覺判讀) → splice進index.html → 部署驗證(tag balance+node --check+curl輪詢+CDP截圖) →
NBLM三套件(語音摘要/資訊圖表/簡報摘要)生成 → embed進網站。

## 1. 議程對照表（官方，來自`_slides_raw/2026年國科會歷史學門主管會議_行前手冊.pdf`）

**Day1 (09-18)**：開幕式(09:50)→第一場「歷史研究的開端-寫筆記」(10:30,實機)→第二場「從數位
筆記到數位思維」(13:30,實機)→第三場「AI時代下的學術倫理」(15:20,演講,林文源老師，現場自己
改題為「研究規範」)→圓桌論壇一(15:50,引言人林文源/李卓穎,主持人陳登武)→晚宴

**Day2 (09-19，進行中)**：第四場「從提示詞工程了解生成式AI的本質」(09:00-10:30,實機)→第五場
「打造AI史料/資料對話機器人」(10:40-12:10,實機)→第六場「利用生成式AI工具優化史學研究工作流程」
(13:10-14:40,演講,張哲維獨講,含OpenClaw)→第七場「AI如何輔助人社領域學生學習和探索」(15:00-15:30,
演講,王道維)→圓桌論壇二(15:40-16:50,引言人王道維/張哲維/陳登武/廖咸惠,主持人李卓穎)

⚠ **重要落差**：官方議程把「提示詞工程/Function Calling/AI Agent/MCP/Agent Skill」五個名詞列在
第四場，但**第四場投影片實際完全沒教這五個東西**，走的是程式語言→編譯器→Transformer→Token→
訓練→注意力機制→解碼器的底層原理路線+Colab實作demo。這五個名詞的正式定義在**第六場p5-p30**。
已有一份完整的科普轉譯草稿處理這個落差，見第4節。

## 2. 目前完成度（截至11:46）

| 場次 | 逐字稿 | Splice進站 | NBLM三套件 | 備註 |
|---|---|---|---|---|
| 開幕式 | — | ✅ stub | — | 純資訊卡 |
| 第一場 | ✅ v2完整 | ✅ | ✅ 語音+圖表+簡報全齊 | PDF.js單頁瀏覽器 |
| 第二場 | ✅ 完整校對+去口語化 | ✅ | ✅ 全齊 | 同上框架 |
| 第三場 | ✅ 完整 | ✅ 全文article,單層7章節pill | ✅ 全齊 | 無投影片，scrollspy導覽 |
| 圓桌一 | ✅ 完整 | ✅ 全文article,單層8章節pill | ✅ 全齊 | 無投影片，同上 |
| **第四場** | ✅ v2完整（127頁，112頁有內容） | ✅ 12:xx上站 | ❌ 未enqueue | 科普轉譯7節已修完14條must_fix並splice，見第3/4節 |
| 第五~七場、圓桌二 | ❌ 錄音尚未上傳 | ✅ stub(投影片先行) | — | 第五/六/未來展望投影片已上站 |

**NBLM簡報顯示模糊問題**：已解決，09-19把4個場次的NBLM簡報從Office Online Viewer換成
LibreOffice headless轉出的原生PDF嵌入（瀏覽器原生渲染），4份pptx→pdf都已轉檔並上站
(`_nblm_artifacts/*/slides.pdf`)。

## 3. 第四場逐字稿＋科普轉譯（**已完成並上站**，commit `b30e9ff`）

- 頁碼對齊agent跑完：127頁，112頁有內容，15頁（p35/92/115-127）誠實標記查無對應逐字稿
  （教師口頭跳過，未勉強拼湊），22處ASR修正已套用。原始產出檔：
  `E:/Downloads/zhewei-pa-aa-dossier/_recordings/第四場逐字稿_頁碼對齊與潤飾_0919.json`
  （**這個路徑在新機器上不存在**——已用來splice進index.html，資料本身活在index.html裡的
  `window.PAGE_TRANSCRIPT_S4`/`window.PAGE_TIME_S4`，不需要靠這個原始JSON存活）。
- 科普轉譯7節（起點/01提示詞工程/02 Function Calling/03 AI Agent/04 MCP/05 Agent Skill/結語）
  已依`review.must_fix_before_publish`14條逐項修正並splice進`tab-s4`：
  04節「昨天下午」→「昨天上午」、01節術語表拆成提示詞/上下文兩列、起點節p22引文補回第七條、
  04節重寫（不再誤把Obsidian掛資料夾講成MCP解決的事，改標明是延伸對照＋補p16原話）、
  結語Harness層級圖修正、起點symlegend擴成7卡（加Token/Vibe Coding）、移除未溯源的
  「條子」比喻、01/03節標記延伸比喻、起點節p11圖顏色描述修正（深藍字非紅字）、結語p93/94
  框錯位置修正、03節h2語氣還原成可能式、02節JSON範例加註「投影片原樣如此」。
- **有一條是判斷題、不是修正**：起點節h2「議程上那五個名詞，第四場一個都沒教」，紅隊複核
  建議請楊老師二選一（原版 vs「要到下午第六場才正式登場——第四場先打地基」）。**時間緊迫，
  這輪先採用後者（較軟的版本）上站**，如果楊老師覺得不對可以再換回去，兩個版本都在git歷史裡。
  另一條紅隊沒下決定的是「02-05四節要不要整體搬到tab-s6」——這次維持放在tab-s4，理由跟h2一樣。
- 部署驗證：JS語法(`node --check`)過、`<section>`/`<div>`標籤配平、git push成功、GitHub Pages
  curl輪詢確認`PAGE_TRANSCRIPT_S4`已上線。**視覺走查未完整完成**——卡在網站自己的Gmail登入閘門
  （Firebase OAuth，會開一個MCP工具看不到的瀏覽器彈窗），沒有輸入任何密碼就中止了，之後有機會
  請楊老師或哲維老師本人肉眼過一次tab-s4。
- **NBLM三套件尚未enqueue**（第4節保留原本的用法說明，材料是`_work_in_progress/session4/`
  底下的`full_text_plain.txt`）。

## 4.5 科普轉譯是Day2往後每一場的標準做法（不只第四場）

楊老師明確澄清（09-19 11:49）：**從第四場開始，Day2的內容都偏技術/偏難**（第五場RAG/向量檢索、
第六場工作流程優化含OpenClaw、第七場AI輔助教學），科普轉譯不是第四場的單次任務，是**後面每一場
splice進站時都要比照辦理的標準流程**。做法沿用第4節的模式：讀該場投影片全部內容+萃取前面場次
老師自己的教學語氣與比喻手法(可累積參考第一~四場)+紅隊逐條查證grounding，不憑空發明比喻。

## 5. NBLM三套件（第四場尚未enqueue）

管線在`E:/Downloads/nblm-orchestrator`，該線自己的規則檔`E:\Downloads\nblm-orchestrator\CLAUDE.md`。
用法：
```bash
python E:/Downloads/nblm-orchestrator/nblm_enqueue.py --project zhewei-workshop-0918 \
  --paper-key session4-transcript --pdf "<第四場逐字稿txt路徑>" \
  --artifacts audio,infographic,slides --focus "第四場說明" --priority 2
```
來源txt要放在`E:/Downloads/nblm-orchestrator/sources/zhewei-workshop-0918/`（參考已有的
`session1-transcript.txt`等檔案格式：標題行+講者+來源說明+全文）。**別直接餵原始ASR，要用
校對完成的版本**（跟前四場一致的做法）。

daemon健檢：`cd E:/Downloads/nblm-orchestrator && python daemon_status.py`
（daemon跑在背景，不用手動啟動，enqueue後會自動被daemon抓去處理，通常要等數十分鐘）

下載完成後的檔案固定路徑：`E:/Downloads/zhewei-digital-notes/_nblm_artifacts/<paper_key>/`
下的`audio.m4a`/`infographic.png`/`slides.pptx`（**還要另外用LibreOffice headless轉一份
slides.pdf**，因為Office Online Viewer嵌入會模糊，見第2節）：
```bash
"C:/Program Files/LibreOffice/program/soffice.exe" --headless --convert-to "pdf:impress_pdf_Export" \
  --outdir <暫存目錄> "<slides.pptx路徑>"
```
embed的HTML寫法照`index.html`裡`nblmSlotEthics`或`nblmSlotPanel1`那兩個`<details>`區塊複製（
audio+img+iframe指向slides.pdf+pptx下載備援連結+模糊警語）。

## 6. 已知未解決事項

1. **API憑證外洩需要rotate**：今天稽核CLI損壞事件時，某支代理人不慎印出了
   `GOOGLE_API_KEY`/`GEMINI_API_KEY`/`CLOUDFLARE_API_TOKEN`明文（違反了我給它的明確指示）。
   這個必須由楊老師本人在一般終端機/瀏覽器操作rotate，**不能在任何Claude session裡執行會
   印出金鑰的指令**。狀態未知是否已處理，接手時要主動問。
2. **CLI（PowerShell的`claude`指令）今天早上崩潰過又修復**：09:39自我更新器因兩個npm prefix
   不一致（`.npmrc`指到E:但更新器在C:改名舊檔），導致`C:\Users\user\AppData\Roaming\npm\
   node_modules\@anthropic-ai\claude-code\bin\claude.exe`被改名成`.old.<epoch>`且沒放回新版。
   已用「改名回來+關自動更新」修復，楊老師確認`claude --version`回報2.1.126成功。**但
   `$env:DISABLE_AUTOUPDATER='1'`只在那個PowerShell session有效**，若楊老師開新視窗，
   建議提醒他先設`[Environment]::SetEnvironmentVariable('DISABLE_AUTOUPDATER','1','User')`
   做永久設定，否則同一個坑下次啟動可能再踩一次（雖然這次prefix已經沒有不一致，理論上
   更新器就算開火也會裝對地方，但沒有實測驗證過）。
3. **蔣中正日記1937-1945新材料入庫**：這是「歷史上的今天」那條線的工作（不是zhewei這條），
   跟工作坊網站無關，但同一session在同時處理。施工計畫已就緒（kb-builder產出、hist-effect-auditor
   等效角色把關過，verdict=可執行），**等楊老師核准才會執行`apply_ingest.py --apply`寫入
   `unified_index.db`**。腳本在`E:/Downloads/CKS/_incoming_楊子震_1937-1945/_ingest/`。
   跟zhewei這條完全獨立，接手時不用管，除非楊老師主動問起。
4. **spawn額度**：今天用override機制連續放寬三次（8→20→25→60，最後一次12:46(+08)過期）。
   若過期後又被擋，正常操作是先判斷是不是真的需要Claude工具，需要的話用
   `python C:/Users/user/.claude/hooks/spawn_cap_override_set.py <新上限> <分鐘> "<理由>"`
   臨時放寬，並用`mcp__ccd_session_mgmt__send_message`發訊給治理線session（session_id
   `local_dac71bf9-7e97-4ebe-8426-33fe0c6005c3`，顯示名「***MCP+CDP+代理人0913」）報備。

## 7. 這輪對話裡修正過的人名/名詞（已上線，不用重查）

張哲維（不是張哲瑋，行前手冊為準）、李卓穎院長、陳登武、王道維（不是道偉）、盧曼(Niklas Luhmann,
不是魯曼)、楊維真教授（不是楊瑞珍/楊偉珍）、黃仁勳（不是黃環君，五層蛋糕論其實是他的梗）、
楊智傑（不是楊士傑，本人被誤聽）、卓穎院長（不是卓雲/卓穎所長混用）。完整查證報告：
`_docs/學者與專有名詞總檢查_0918.md`（Day1範圍，Day2尚未做過同等規模查證）。

## 8. 常用指令備忘

**部署驗證（每次改完index.html）**：
```bash
python -c "
import re
h = open('index.html', encoding='utf-8').read()
for tag in ['div','script','p','button','details','section']:
    o = len(re.findall(r'<'+tag+r'(?:\s[^>]*)?>', h))
    c = len(re.findall(r'</'+tag+r'>', h))
    print(tag, o, c, 'OK' if o==c else 'MISMATCH')
"
git add index.html && git commit -m "..." && git push
# curl輪詢確認deploy（每15秒一次，最多5-6次）：
curl -s "https://scatjay.github.io/zhewei-digital-notes/?v=$RANDOM" | grep -c "關鍵字"
```

**外部CDP瀏覽器視覺驗證（絕對不用內建瀏覽器面板，會讓桌面App當機）**：
```bash
cd "E:/Downloads/歷史上的今天" && python _系統/05_開源工具/mcp_servers/cdp_browser/cli.py --port 9240 list_tabs
python _系統/05_開源工具/mcp_servers/cdp_browser/cli.py --port 9240 navigate "<tabId>" "https://scatjay.github.io/zhewei-digital-notes/?nc=$RANDOM$RANDOM"
python _系統/05_開源工具/mcp_servers/cdp_browser/cli.py --port 9240 screenshot "<tabId>" --out-name <檔名>
```
**⚠ navigate一定要帶cache-busting query string，光用`location.reload()`會讀到瀏覽器快取的舊版**。

**Drive錄音檔查詢**：
```
parentId = '1LKZyLOzNOTvFT-sihyq1hChouuLooWtq' and modifiedTime > '<今天UTC日期>T00:00:00Z'
```

**rclone下載（注意copyto只吃兩個位置參數，檔名接在gdrive:後面當source）**：
```bash
"D:/Downloads/rclone/rclone.exe" copyto "gdrive:<檔名>.zip" --drive-root-folder-id <parentId> "<本機路徑>.zip"
```

## 9. 新機器要重建什麼（空筆電開場檢查清單）

按優先順序，做到能splice+push+curl驗證為止就夠用，不用整套重建：

1. **`git clone https://github.com/scatjay/zhewei-digital-notes.git`**——這一步拿到全部已
   deploy的內容+本檔+`_work_in_progress/`裡搶救出來的中繼產物。
2. 確認能`git push`（GitHub認證，個人帳號scatjay）。
3. 確認能跑`curl`打GitHub Pages URL驗證部署（一般網路即可，不需要特殊工具）。
4. **如果要繼續處理逐字稿（splice科普草稿、修正must_fix清單）**：只需要文字編輯能力，
   `_work_in_progress/`裡的材料已經夠用，**不需要GPU/faster-whisper**。
5. **如果要繼續轉錄新場次的錄音**（第五~七場、圓桌二）：才需要faster-whisper+CUDA環境，
   這是真正的重建工程（裝Python/PyTorch/CUDA/faster-whisper套件），**若新機器沒有GPU或
   懶得裝，可以先跳過轉錄，只處理已完成場次的splice/科普轉譯/部署驗證這些不需要GPU的工作，
   等楊老師回到原本那台機器再繼續轉錄**。
6. **CDP瀏覽器視覺驗證**（`歷史上的今天`repo裡的`cdp_browser/cli.py`）：這是另一個repo，
   新機器上不存在。**沒有它不影響splice/commit/push，只是無法做視覺截圖驗證這一步**——
   退而求其次可以請楊老師自己開瀏覽器看網站確認，或跳過這步驟直接信任curl+tag balance檢查。
7. **NBLM enqueue**（`E:/Downloads/nblm-orchestrator`）：另一個repo+daemon，新機器上不存在，
   **這步驟直接跳過**，等楊老師確認要生成時再處理，不影響逐字稿本身上站。
8. Drive下載新錄音需要rclone設定（`D:/Downloads/rclone/rclone.exe`）——新機器沒有的話，
   改用Google Drive MCP的`download_file_content`直接下載base64（skill第1節已經寫了退路）。

## 10. 這條session的skill/memory參考

- `~/.claude/skills/zhewei-session-processing/SKILL.md`——完整9步驟管線，包含所有CSS/JS陷阱清單
- memory: `project_zhewei_workshop_unit_20260918.md`、`reference_zhewei_recording_upload_location.md`、
  `feedback_cannot_vs_not_found.md`
