https://www.peacelove.ai

這個元件在做什麼
一段 <script> 嵌進任何網站,右下角會坐一個 Live2D 虛擬人。訪客開口講話,她聽、回答、開口回,嘴型跟著音量動。

引擎、長相、知識庫分開。皮(data-model)、肉(data-knowledge)、腦(可選的 WebLLM),三層自己換。預設純前端,免後端,語音走瀏覽器內建,神經語音才需要一支 serverless function。

它跟 PEACELOVE 雙核心的關係
PEACELOVE 把品牌切成兩條線:
P.L. CORE 處理 PEACE(安全、信任、本地化、隱私)
P.L. HUES 處理 LOVE(熱情、時尚、AI 個人化、共創)

這個 Widget 是兩條線第一次在同一支程式裡碰頭。

怎麼體現 P.L. CORE
零金鑰。訪客不登入任何雲端,LLM 用 WebLLM 在本機跑,STT 用瀏覽器內建,神經語音只在部署者自己接 /api/tts 時才走網路
資料不外傳。對話內容留在訪客的瀏覽器 tab,部署者看不到
同網域防濫用。/api/tts 預設只接受自家來源的呼叫,避免被當免費 TTS proxy
降級設計。神經語音掛了,退回瀏覽器內建語音;WebLLM 沒開,退回純檢索式 FAQ

「真正的創新來自熱情與安全的平衡」這句 PEACELOVE 官網的標語,在這支元件裡是行為守則,不是裝飾。

怎麼體現 P.L. HUES
角色可換。要美式潮妹要日系蘿莉,把 data-model 換掉就行。window.AvatarWidget.say(text) 還能讓宿主網站主動叫她開口,拿來做商品介紹或開箱小劇場都行
內容可換。data-knowledge 換成自家 FAQ JSON,她就從客服變穿搭顧問,變美妝老師,變品牌大使
本地 AI 個人化。把 WebLLM 啟用後,她能針對訪客的提問即時組織語句,不是死背 FAQ,這是 Chroma AI「命定推薦」邏輯在對話介面的延伸

怎麼用
方式一,自架純前端(推薦)

把 widget.html、embed.js、knowledge.js 三個檔放進你的網站,貼這行:

<script src="/path/embed.js"
        data-model="你的-live2d.model3.json"
        data-knowledge="你的-faq.json"></script>


跑在訪客的瀏覽器裡,語音用瀏覽器內建。零後端、零金鑰、零雲端帳單。

方式二,託管一行

<script src="https://你的部署.vercel.app/embed.js"></script>


試玩最快。但運算跟流量算在部署者頭上,別用在正式上線的客戶專案。

方式三,加神經語音

npm install
vercel --prod


不設 data-api,Widget 會自己試同站的 /api/tts,抓不到就退回瀏覽器語音。整個降級路徑是設計好的,不會卡死。

設定一覽
data-model:Live2D .model3.json 網址(皮)
data-knowledge:FAQ JSON 網址,陣列 [{q, kw, a}](內容)
data-api:神經語音端點。不設走同站預設
data-voice:聲線。預設 zh-TW-HsiaoChenNeural
data-widget:widget.html 的網址。預設跟 embed.js 同目錄
data-open:一進站是否展開。true 直接打開,false 先收成右下角小氣泡

對外 JS API 三個:
window.AvatarWidget.open() 展開對話框
window.AvatarWidget.close() 收回氣泡
window.AvatarWidget.say(text) 叫她主動講一句

風險要先看清楚
 (1/2)
TTS 走非官方端點。/api/tts 透過 msedge-tts 連到微軟 Edge 朗讀,非官方,可能隨時被封。失效會自動退回瀏覽器語音,但正式環境建議改接 Azure Speech 官方
/api/tts 是公開端點。預設只擋跨網域,沒做完整限流。Vercel 部署務必在專案層開 Spend Management / Firewall 限流,免得帳單被灌爆
語音辨識會送雲端。webkitSpeechRecognition 在 Chrome 下會把麥克風音訊丟給 Google。這不是本機辨識,要在隱私政策裡跟使用者講
首次載入 WebLLM 約 1GB。要走 PWA / 提示使用者稍等,別讓人以為網頁當機
Live2D 與 Haru 範例模型有授權。本 repo 的程式碼是 MIT,但 Live2D Cubism Core 走 Live2D Proprietary License,Haru 模型走 Live2D Free Material License,正式上線換成自有合法授權的模型
模型權重授權。WebLLM 的模型權重各走各的授權,Qwen2.5 那些條款要先查

誰適合用
想要「網站 + 24h 客服」但不想每月燒 LLM API 帳單的中小型電商
想在品牌官網放一個會說話的品牌大使,又不想被 SaaS 綁死的時尚、美妝、創作者平台
對資安敏感的企業內網站,需要「對話介面」但對話資料不能上雲的場景
想驗證「本地 LLM + 瀏覽器 STT/TTS」這個技術組合到底穩不穩的工程團隊

不適合的:金流個資、長期對話紀錄、多人協作 CRM — 那些請接 CRM,別拿這個硬撐。

PEACELOVE 怎麼看這件事
PEACELOVE 把創新切得很乾淨:科技面(CORE)解決信任,時尚面(HUES)解決表達。過去兩條線的產品各自獨立 — ShieldOS 在伺服器那頭,Chroma AI 在手機這頭。這個 Widget 是第一次讓兩條線在瀏覽器同一個 tab 碰在一起。

本地 LLM 是 CORE 的延伸(資料不外傳),可換的角色是 HUES 的延伸(每個人都有自己的樣子)。一行 <script> 把兩件事壓進來,部署者自己決定要偏哪邊。

這篇文章沒打算告訴你「這會改變世界」。它就只是一個開源元件,做得到、做不到、該注意什麼,程式碼跟授權檔都攤在 repo 裡。PEACELOVE 一貫的態度:可驗證的東西直接講,需要自己評估的講清楚,別把行銷稿當成規格書。
