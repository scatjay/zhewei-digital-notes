# 交接文件 — 清大工作坊配套網站（張哲維老師）

> 寫於 2026-09-19 11:46（14:30更新，第五場完成後），工作坊Day2現場即時協作中。**楊老師明確交代：接手的機器
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

## 2. 目前完成度（截至14:30）

| 場次 | 逐字稿 | Splice進站 | NBLM三套件 | 備註 |
|---|---|---|---|---|
| 開幕式 | — | ✅ stub | — | 純資訊卡 |
| 第一場 | ✅ v2完整 | ✅ | ✅ 語音+圖表+簡報全齊 | PDF.js單頁瀏覽器 |
| 第二場 | ✅ 完整校對+去口語化 | ✅ | ✅ 全齊 | 同上框架 |
| 第三場 | ✅ 完整 | ✅ 全文article,單層7章節pill | ✅ 全齊 | 無投影片，scrollspy導覽 |
| 圓桌一 | ✅ 完整 | ✅ 全文article,單層8章節pill | ✅ 全齊 | 無投影片，同上 |
| **第四場** | ✅ 兩輪完整校對+去口語化（127頁,112頁有內容） | ✅ 已上站 | ⏳ 已enqueue待daemon處理 | 科普轉譯7節+14條must_fix+30處贅語去重，見第3節 |
| **第五場** | ✅ 兩輪完整校對+去口語化（95頁,75頁有內容） | ✅ 已上站 | ⏳ 已enqueue待daemon處理 | 科普轉譯8節+9條must_fix，見第3節 |
| **第六場** | ✅ 兩輪完整校對+去口語化（93頁,71頁有內容）+現場問答17分鐘5段 | ✅ 已上站(commit ce0b635) | ⏳ 待enqueue | 第二輪抓到第一輪P74編造內容錯誤(CSV截圖誤判成總督府檔案)，見第3節 |
| **第七場**（王道維） | ⏳ 第一輪完成(12節)，第二輪獨立校對進行中 | ✅ stub | — | ⚠**找不到投影片檔案**（Drive遍尋不到，可能沒上傳或本來就沒有投影片），純逐字稿整理不做頁碼對齊；之後若投影片出現要回頭補對照 |
| 圓桌二 | ⏳ **現場忘記錄音**，楊老師16:18正在跟工作坊主辦窗口確認能否補到他們自己的錄音（非「已上傳待處理」也非確定永久遺失，是待確認） | ✅ stub | — | stub頁面標「向主辦方確認錄音中」；補到之前不要主動去Drive查這場，等楊老師消息 |

**NBLM簡報顯示模糊問題**：已解決，09-19把4個場次的NBLM簡報從Office Online Viewer換成
LibreOffice headless轉出的原生PDF嵌入（瀏覽器原生渲染），4份pptx→pdf都已轉檔並上站
(`_nblm_artifacts/*/slides.pdf`)。

## 3. 第四、五場逐字稿＋科普轉譯（**都已完成並上站**）

兩場走的是同一套已經定型的pipeline：頁碼對齊(含視覺判讀空白頁) → **第一輪**去口語化 →
**獨立第二輪**完整校對(重新逐頁對照投影片，通常會再挖出幾十條第一輪沒抓到的錯誤) →
科普轉譯草稿(依該場實際內容自訂結構，不強套模板) → 獨立紅隊複核 → 逐條套用must_fix →
splice → 部署驗證。**第二輪校對跟紅隊複核都必須是獨立指派，不能沿用第一輪agent自己複查**
（同一個agent不容易抓到自己的盲點，09-19實測第四場第二輪抓到42條、第五場紅隊抓到人名
「黃祖銓/黃祖泉」兩輪各錯一次、只有第三次獨立視覺判讀8倍放大才讀對「黃祖權」）。

**第四場**（commit `24c2458`起共6次相關commit）：
- 127頁，112頁有內容，15頁誠實標記查無對應（教師口頭跳過）。第二輪校對新找到42條ASR修正
  （含「8億個引數」實為「8千億個引數」數字級錯誤）。另外掃出79處ASR重複贅語，其中30處
  「X，X。」句尾無延伸的明確贅語已去重，約30處「X，X接新內容」的教學節奏延伸判斷為正常
  用法保留（如「按下去，按下去之後…」）。
- 科普轉譯7節（起點/01提示詞工程/02 Function Calling/03 AI Agent/04 MCP/05 Agent Skill/結語）
  已套用14條must_fix：04節「昨天下午」→「昨天上午」、01節術語表拆成提示詞/上下文兩列、
  起點節p22引文補回第七條、04節重寫（不再誤把Obsidian掛資料夾講成MCP解決的事）、結語Harness
  層級圖修正、起點symlegend擴成7卡（加Token/Vibe Coding）、移除未溯源比喻、03節h2語氣還原、
  02節JSON範例加註「投影片原樣如此」。**起點節h2「議程五個名詞第四場一個都沒教」是判斷題**，
  紅隊建議二選一，時間緊迫先採軟化版本上站（git歷史裡兩版都在，楊老師覺得不對可換回）。
- 楊老師現場即時核對又抓到6處：張宏毅→張弘毅老師（掛耳咖啡包）、自工信/成績疊→資工系/
  成績跌、指輪→齒輪、元模型→語言模型(2處)、Token Nice→Tokenize、幹預→干預(3處)、
  Vibe Coding頁碼p20→p18。全部已修正上站。

**第五場**（commit `ddabe8e`起共3次相關commit）：
- 95頁，75頁有內容，20頁誠實標記查無對應（12頁教師53:22口頭明確宣布跳過RAG理論細節、
  過場頁、安靜段落ASR幻覺）。第二輪校對**結果是原文已達標準、不需改動文字**（第一輪一次到位，
  不是每場都會需要第二輪修正——差別在於第一輪一開始就套用了跟第四場相同的高標準規格）。
- **重大發現**：投影片後半（p55-95，佔43%頁數、後段31分鐘）完全脫離官方議程「資料對話
  機器人」主題，變成獨立的Codex（AI coding agent）操作教學，最後13分鐘才用Agent Skills
  處理德國外交部檔案拉回史料情境——這不是教學疏漏，是老師自己當場口頭宣布的取捨。
- 科普轉譯8節**依實際內容自訂結構**（起點/01為什麼要用RAG/02 RAG怎麼做出來/03自己動手做/
  04 RAG的邊界/05轉場Codex是什麼/06 Codex長出手腳之後/結語），沒有硬套第四場五名詞模板。
  紅隊複核9條must_fix：**人名訂正黃祖銓→黃祖權**（投影片放大8倍核實，兩輪草稿各錯一次同一字，
  第三次獨立視覺判讀才讀對）、修正MCP表單誤讀（畫面上是介面灰色提示文字，不是老師示範操作）、
  修正誤引的「Codex有很多很多的外掛」實為「Codex也有很多外掛功能，因為時間有限先簡單帶過」、
  p52/p53標籤改「改寫為條列」並補回漏引的「精確的可回溯性」、起點節h2軟化措辭、
  德文件→德文文件、YAML/Bi-Encoder/Cross-Encoder/API Key補白話註解、補週額度77%說明。

**兩場的部署驗證**：JS語法(`node --check`)過、標籤配平、GitHub Pages curl輪詢確認上線。
**視覺走查未完整完成**——卡在網站自己的Gmail登入閘門（Firebase OAuth，會開一個MCP工具
看不到的瀏覽器彈窗），沒有輸入任何密碼就中止了，之後有機會請楊老師或哲維老師本人肉眼過
一次tab-s4/tab-s5。

**第六場（進行中，錄音尚未上傳）現場筆記已記錄3則**（tab-s6的user-note區塊，全部標明
「現場筆記轉述，非逐字引言，待錄音處理後可對照校正」，且**不具名**——楊老師09-19在同一
session講了兩次「不要寫我的名字」，已記進memory `feedback_zhewei_no_username_in_notes.md`）：
①「AI是一場不可逆的典範轉型」②「強化研究成果的推廣」③「人文才是人類知識的目的」。
楊老師自己說過一句「台灣的時代精神是實力主義，人文的邊緣化是全球化的現象」**明確交代
不要放上網站**，只留在這份交接紀錄裡供接手者知道曾經講過，不要誤放上去。

## 4.5 科普轉譯是Day2往後每一場的標準做法（不只第四場）

楊老師明確澄清（09-19 11:49）：**從第四場開始，Day2的內容都偏技術/偏難**（第五場RAG/向量檢索、
第六場工作流程優化含OpenClaw、第七場AI輔助教學），科普轉譯不是第四場的單次任務，是**後面每一場
splice進站時都要比照辦理的標準流程**。做法沿用第4節的模式：讀該場投影片全部內容+萃取前面場次
老師自己的教學語氣與比喻手法(可累積參考第一~四場)+紅隊逐條查證grounding，不憑空發明比喻。

## 5. NBLM三套件（第四、五場都已enqueue，待daemon處理）

管線在`E:/Downloads/nblm-orchestrator`，該線自己的規則檔`E:\Downloads\nblm-orchestrator\CLAUDE.md`。
`session4-transcript`跟`session5-transcript`兩個job都已經enqueue（用的是最終校對版文字，
不是原始ASR），daemon狀態截至14:30是`assisted_health`延後（偵測到user在動作，避免打擾），
兩個job都還是`queued`尚未`fired`。用法：
```bash
python E:/Downloads/nblm-orchestrator/nblm_enqueue.py --project zhewei-workshop-0918 \
  --paper-key session6-transcript --pdf "<第六場逐字稿txt路徑>" \
  --artifacts audio,infographic,slides --focus "第六場說明" --priority 2
```
**⚠ dedup檢查踩過的坑**：`--paper-key`的相似度比對是比對**key字串本身**，不是比對內容——
「session6-transcript」跟「session5-transcript」只差一個字元會被判定94%相似擋下來
（`⚠ 疑似重複→不入佇列待確認`），確認內容真的不同（例如檢查有沒有這場獨有的關鍵詞）後
直接加`--force`重新跑同一條指令即可，不是bug。

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

0. **🔴 楊老師14:29交代的下一階段任務，第六場處理完之後才做**（原話：「到時候單元6處理好的
   時候，全盤對哲維的課程，特別是扣合對準和王道維老師的討論，回過頭去重新討論技術工具的
   教學，然後全面盤點他所有他的單元。」）——這不是逐場splice的例行工作，是**跨場次的綜合
   整理**，等第一~六場（張哲維老師主講的全部場次）都splice完成後才啟動，具體要做：
   - 全盤盤點張哲維老師講過的所有單元（第一/二/四/五/六場，他是這幾場的主講人）——內容、
     教學脈絡、彼此的銜接關係整理成一份總覽。
   - **特別要扣合對準王道維老師的討論**——⚠ 楊老師14:31現場更正：**王道維老師已經在第六場
     尾聲現身、當場提出一些討論**（不是只有官方議程排定的第七場主講／圓桌二與談才會出現），
     這代表**第六場錄音本身可能就直接錄到這段跨場次討論**，splice第六場逐字稿時要特別留意
     結尾附近有沒有王道維老師的發言段落，不用等第七場/圓桌二錄音才有材料可以對照。王道維
     老師另外也是官方議程第七場「AI如何輔助人社領域學生學習和探索」主講人、圓桌論壇二
     （引言人王道維/張哲維/陳登武/廖咸惠）與談人，那兩場材料到齊後可以再補充對照，但**第一手
     材料很可能已經在第六場逐字稿裡**，處理第六場時要特別檢查結尾部分。
   - **回過頭去重新討論技術工具的教學**——這句話的確切範圍要等實際做的時候跟楊老師確認，
     初步理解是對哲維老師教的技術工具(Obsidian/Token/RAG/Codex/Agent Skill等)做一次
     跨場次的回顧與反思，不是重複已經做過的單場科普轉譯。
   - 第七場跟圓桌二的錄音目前都還沒上傳，這個任務要等這兩場也處理完才有完整材料可以對照。
   - 這件事優先權在第六場逐字稿+科普轉譯完成之後，不要提前開始。

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
楊智傑（不是楊士傑，本人被誤聽）、卓穎院長（不是卓雲/卓穎所長混用）、張弘毅老師（提供掛耳
咖啡包的台灣史學會老師，不是張宏毅，楊老師09-19現場核對）、黃祖權（第五場人權故事地圖案主，
不是黃祖銓/黃祖泉，投影片放大8倍核實）、**王道維老師任教於清大物理系**（楊老師09-19 16:23現場
核對確認；第七場逐字稿第二輪校對曾抓到round1自行腦補「成大」且附了個逐字稿裡查無依據的理由，
已改回誠實標記，這條確認可以拿來補上這個bio空缺，但「新大哲學院」那個ASR謎團詞——出現在他講
「這學期會在[新大哲學院]和同學之間試用」的上下文——文法上看不出來能直接替換成「清大物理系」，
仍維持round2的「無法確認」標記，兩件事不要混為一談）。完整查證報告：
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
- memory: `project_zhewei_workshop_unit_20260918.md`（含09-19更新：Day2 pipeline定型、
  科普轉譯做法已獲楊老師確認）、`reference_zhewei_recording_upload_location.md`、
  `feedback_cannot_vs_not_found.md`、`feedback_zhewei_no_username_in_notes.md`
  （現場筆記絕不具名寫楊老師名字，09-19同session踩過兩次）
