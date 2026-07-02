<!DOCTYPE html>
<html>
<head>
  <base target="_top">
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>文字委託排單</title>
  <style>
    /* 這裡已經為您保留並整合原本 Stylesheet 的所有樣式設定 */
    :root {
      --primary-color: #6c5ce7;
      --secondary-color: #a29bfe;
      --background-color: #f8f9fa;
      --card-background: #ffffff;
      --text-color: #2d3436;
      --border-radius: 12px;
      --transition-speed: 0.3s;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Helvetica Neue', Arial, 'Noto Sans TC', sans-serif;
      background-color: var(--background-color);
      color: var(--text-color);
      line-height: 1.6;
    }
    .navbar {
      background-color: var(--primary-color);
      color: white;
      padding: 15px 30px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      position: sticky;
      top: 0;
      z-index: 100;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    }
    .nav-title { font-size: 1.4rem; font-weight: bold; }
    .nav-links button {
      background: none;
      border: none;
      color: white;
      margin-left: 20px;
      font-size: 1rem;
      cursor: pointer;
      transition: opacity var(--transition-speed);
    }
    .nav-links button:hover { opacity: 0.8; }
    .container { max-width: 1000px; margin: 40px auto; padding: 0 20px; }
    .page { display: none; }
    .page.active { display: block; animation: fadeIn 0.5s ease; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    
    /* 首頁與卡片樣式 */
    .hero-section { background: linear-gradient(135deg, var(--primary-color), var(--secondary-color)); color: white; padding: 40px; border-radius: var(--border-radius); margin-bottom: 40px; text-align: center; }
    .hero-section h1 { margin-bottom: 15px; }
    .btn-group { margin-top: 25px; }
    .btn { padding: 10px 25px; border: none; border-radius: 25px; font-size: 1rem; cursor: pointer; font-weight: bold; transition: all var(--transition-speed); }
    .btn-primary { background-color: white; color: var(--primary-color); }
    .btn-primary:hover { background-color: #f0f0f0; transform: translateY(-2px); }
    .btn-secondary { background-color: transparent; border: 2px white solid; color: white; }
    .btn-secondary:hover { background-color: rgba(255,255,255,0.1); transform: translateY(-2px); }
    .section-title { margin-bottom: 25px; color: var(--primary-color); }
    .card { background: var(--card-background); padding: 30px; border-radius: var(--border-radius); box-shadow: 0 4px 15px rgba(0,0,0,0.05); margin-bottom: 25px; position: relative; }
    .card h3 { margin-bottom: 10px; color: #2d3436; }
    .tag { display: inline-block; background-color: #ffeaa7; color: #d63031; padding: 3px 10px; border-radius: 15px; font-size: 0.85rem; font-weight: bold; margin-bottom: 15px; }
    .preview { color: #636e72; display: -webkit-box; -webkit-line-clamp: 3; -webkit-box-orient: vertical; overflow: hidden; margin-bottom: 15px; text-align: justify; }
    .btn-readmore { background: none; border: none; color: var(--primary-color); font-weight: bold; cursor: pointer; font-size: 0.95rem; }
    .btn-readmore:hover { text-decoration: underline; }

    /* 表單樣式 */
    .form-group { margin-bottom: 20px; }
    .form-group label { display: block; margin-bottom: 8px; font-weight: bold; color: #4a4a4a; }
    .form-group input, .form-group select, .form-group textarea { width: 100%; padding: 12px; border: 1px #ccc solid; border-radius: 6px; font-size: 1rem; }
    .form-group textarea { resize: vertical; min-height: 120px; }
    .btn-submit { background-color: var(--primary-color); color: white; width: 100%; padding: 12px; font-size: 1.1rem; }
    .btn-submit:hover { background-color: #5b4bc4; }

    /* 表格樣式 */
    .table-container { background: white; border-radius: var(--border-radius); overflow-hidden: box-shadow: 0 4px 15px rgba(0,0,0,0.05); }
    table { width: 100%; border-collapse: collapse; text-align: left; }
    th, td { padding: 15px 20px; border-bottom: 1px #eee solid; }
    th { background-color: #f1f2f6; color: #4a4a4a; }
    .status-badge { display: inline-block; padding: 4px 12px; border-radius: 15px; font-size: 0.85rem; font-weight: bold; }
    .status-pending { background-color: #ffeaa7; color: #b7791f; }
    .status-done { background-color: #badc58; color: #407306; }

    /* 留言板樣式 */
    .feedback-list { margin-top: 30px; }
    .fb-item { background: white; padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.03); margin-bottom: 15px; border-left: 5px var(--primary-color) solid; }
    .fb-meta { display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 0.85rem; color: #999; }
    .fb-user { font-weight: bold; color: #555; }
    .fb-level { color: #ffb703; margin-bottom: 8px; }
    .fb-text { color: #4a4a4a; font-size: 0.95rem; white-space: pre-line; }
    .loading { text-align: center; padding: 20px; color: #aaa; font-style: italic; }

    /* 彈出視窗密碼與排版 */
    .modal-overlay {
      position: fixed; top: 0; left: 0; width: 100%; height: 100%;
      background-color: rgba(40, 30, 60, 0.4); backdrop-filter: blur(4px);
      display: flex; justify-content: center; align-items: center; z-index: 200;
      opacity: 0; pointer-events: none; transition: opacity 0.3s ease;
    }
    .modal-overlay.show { opacity: 1; pointer-events: auto; }
    .modal-content {
      background-color: #fff; padding: 35px; border-radius: var(--border-radius);
      max-width: 700px; width: 90%; max-height: 80vh; overflow-y: auto;
      box-shadow: 0 10px 30px rgba(0,0,0,0.15); position: relative;
    }
    .modal-close { position: absolute; top: 15px; right: 20px; font-size: 1.5rem; cursor: pointer; color: #aaa; }
    .modal-close:hover { color: #333; }
    .modal-body-text { white-space: pre-line; margin-top: 20px; text-align: justify; color: #333; }
  </style>
</head>
<body>

  <nav class="navbar">
    <div class="nav-title" onclick="switchPage('home')" style="cursor:pointer;"> 🔮  文字委託</div>
    <div class="nav-links">
      <button onclick="switchPage('home')">首頁</button>
      <button onclick="switchPage('rules')">委託須知</button>
      <button onclick="switchPage('input')">我要委託</button>
      <button onclick="switchPage('schedule')">排單進度</button>
      <button onclick="switchPage('feedback-view')">回饋留言板</button>
    </div>
  </nav>

  <div class="container">
    <div id="page-home" class="page active">
      <div class="hero-section">
        <h1>歡迎來到文字委託排單網頁  ✨ </h1>
        <p>這裡提供一般文字委託、驚喜包、以及圖文一條龍服務。委託前請務必先閱讀委託須知唷！</p>
        <div class="btn-group">
          <button class="btn btn-primary" onclick="switchPage('rules')" style="margin-right:10px;">閱讀委託須知  ➔ </button>
          <button class="btn btn-secondary" onclick="switchPage('input')">點我開始委託  ➔ </button>
        </div>
      </div>

      <h2 class="section-title"> ✨  委託範例 / 同人文範例</h2>
      
      <div class="card">
        <h3>【名偵探柯南 黑羽快斗夢向】還記得第一次見你時我的心是怎樣的波動</h3>
        <p class="tag">一般文字委託</p>
        <p class="preview">「大家好！我叫富松柚希！」第一次見到她，是在一個很普通的早晨。我就像往常一樣去上課，正想打瞌睡的時候，一顆檸檬突然說要坐在我旁邊。對，檸檬，她長得就像一顆檸檬，根本一模一樣！她一直找我說話，我強忍著睡意回覆她，很勉強，真的，但她好像根本不在意，就自顧自地講，講到天荒地老……我忍不住打了一個哈欠，她竟然笑我？！「哈哈哈哈哈，你怎麼那麼遜啊！一大早就想睡覺。」……</p>
        <button class="btn-readmore" onclick="openArticle(1)">觀看全文</button>
      </div>

      <div class="card">
        <h3>【海賊王 山治夢向】巴拉蒂的雷雨之夜</h3>
        <p class="tag">驚喜包</p>
        <p class="preview">巴拉蒂很久沒有那麼熱鬧的。艾米斯的加入引起了許多人的討論，山治是其中一個——身為巴拉蒂這艘船上最「了解」女孩子的人，他反而是最興奮的那一個。山治忙前忙後的準備了許多美食……</p>
        <button class="btn-readmore" onclick="openArticle(2)">觀看全文</button>
      </div>

      <div class="card">
        <h3>【名偵探柯南 新志同人文】新志｜Adonis</h3>
        <p class="tag">同人文範例</p>
        <p class="preview">英國的冬天很冷，冷到宮野志保的嘴唇有些發紫。來英國已經三年了，這三年來她埋首於實驗，開始想英國好像也沒有如記憶中那般美好，她討厭英國的空氣，也討厭大笨鐘不斷提醒著人們時間的流逝，她開始討厭某些東西，卻唯獨不討厭福爾摩斯...</p>
        <button class="btn-readmore" onclick="openArticle(3)">觀看全文</button>
      </div>
    </div>

    <div id="page-rules" class="page">
      <h2 class="section-title"> 📜  委託 group 須知與規範</h2>
      <div class="card">
        <h3>💡  基本規則</h3>
        <p style="margin-top:10px;">1. 稿件皆不可用於商業盈利，純屬同好交流分享。</p>
        <p>2. 填單後會自動進入排單系統，工作天約為 14 天內完稿。</p>
        <p>3. 有指定交稿日請務必於表單中提前填寫說明。</p>
      </div>
    </div>

    <div id="page-input" class="page">
      <h2 class="section-title"> 📝  填寫委託表單</h2>
      <div class="card">
        <form id="orderForm" onsubmit="handleOrderSubmit(event)">
          <div class="form-group">
            <label>暱稱 / FB 姓名 *</label>
            <input type="text" id="orderName" required>
          </div>
          <div class="form-group">
            <label>委託類型 *</label>
            <select id="orderType" required>
              <option value="一般文字委託">一般文字委託</option>
              <option value="驚喜包">驚喜包</option>
              <option value="同人文範例">同人文委託</option>
            </select>
          </div>
          <div class="form-group">
            <label>委託字數 *</label>
            <input type="text" id="orderWords" placeholder="例如：3000字" required>
          </div>
          <div class="form-group">
            <label>付款方式 *</label>
            <select id="orderPayment" required>
              <option value="銀行轉帳">銀行轉帳</option>
              <option value="LINE Pay">LINE Pay</option>
              <option value="超商代碼">超商代碼</option>
            </select>
          </div>
          <div class="form-group">
            <label>指定日期 (選填)</label>
            <input type="date" id="orderRequiredDate">
          </div>
          <button type="submit" class="btn btn-submit">確認送出委託</button>
        </form>
      </div>
    </div>

    <div id="page-schedule" class="page">
      <h2 class="section-title"> 📊  目前排單與製作進度</h2>
      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>委託人</th>
              <th>類型</th>
              <th>字數</th>
              <th>付款方式</th>
              <th>指定交期</th>
              <th>預計完稿</th>
              <th>目前狀態</th>
            </tr>
          </thead>
          <tbody id="orderTableBody">
            <tr><td colspan="7" class="loading">排單資料載入中...</td></tr>
          </tbody>
        </table>
      </div>
    </div>

    <div id="page-feedback-view" class="page">
      <h2 class="section-title"> 💬  客戶委託回饋與留言</h2>
      
      <div class="card" style="margin-bottom: 40px;">
        <h3>新增回饋留言</h3>
        <form id="feedbackForm" onsubmit="handleFeedbackSubmit(event)" style="margin-top:15px;">
          <div class="form-group">
            <label>暱稱 *</label>
            <input type="text" id="fbUser" required>
          </div>
          <div class="form-group">
            <label>滿意度評分 *</label>
            <select id="fbLevel" required>
              <option value="⭐⭐⭐⭐⭐">⭐⭐⭐⭐⭐ 非常滿意</option>
              <option value="⭐⭐⭐⭐">⭐⭐⭐⭐ 滿意</option>
              <option value="⭐⭐⭐">⭐⭐⭐ 普通</option>
            </select>
          </div>
          <div class="form-group">
            <label>回饋心得與訊息 *</label>
            <textarea id="fbMessage" required></textarea>
          </div>
          <button type="submit" class="btn btn-submit">送出留言</button>
        </form>
      </div>

      <div class="feedback-list" id="feedbackContainer">
        <div class="loading">留言載入中...</div>
      </div>
    </div>
  </div>

  <div class="modal-overlay" id="articleModal">
    <div class="modal-content">
      <span class="modal-close" onclick="closeModal()">&times;</span>
      <h2 id="modalTitle" style="color: var(--primary-color);"></h2>
      <div class="modal-body-text" id="modalBody"></div>
    </div>
  </div>

  <script>
    // ⚠️ 請在此處替換您在 GAS 部署時取得的「網頁應用程式網址」
    const GAS_API_URL = "https://script.google.com/macros/s/XXXXX/exec";

    // 文章本文資料庫（為您完整保留原始文字內容）
    const articles = {
      1: {
        title: "【名偵探柯南 黑羽快斗夢向】還記得第一次見你時我的心是怎樣的波動",
        content: `「大家好！我叫富松柚希！」\n\n第一次見到她，是在一個很普通的早晨。\n\n我就像往常一樣去上課，正想打瞌睡的時候，一顆檸檬突然說要坐在我旁邊。\n\n對，檸檬，她長得就像一顆檸檬，根本一模一樣！\n\n她一直找我說話，我強忍著睡意回覆她，很勉強，真的，但她好像根本不在意，就自顧自地講，講到天荒地老……我忍不住打了一個哈欠，她竟然笑我？！\n\n「哈哈哈哈哈，你怎麼那麼遜啊！一大早就想睡覺。」\n\n……\n\n「本黑羽大爺才不遜好嗎！那是因為你講的話太無聊了！」\n「哪有，我那麼可愛，講出來的話一定也很好聽！」\n\n？？？\n世界上怎麼會有這種人啊！自戀成這個樣子，總覺得跟某個人好像，但就是想不起來……喔，對了，不就是我嗎？\n\n好吧，暫且原諒她了。\n\n後來想想，那一天也沒有什麼不同，不像偶像劇裡演的初遇那樣不平凡，反而是很普通的一天，普通到讓人變。 當然，人怎麼有可能一見鍾情，這太不科學了。\n\n過了很久我才知道，我好像就是被那麼不科學的感情束縛。\n\n原來那一天沒有想像中的平凡。\n\n.\n\n黑羽快斗第一次見到富松柚希的時候，他快睡著了。\n\n卻被一聲甜甜的聲音打斷了睡意，富松柚希靦腆的叫他「黑羽同學」，讓他短暫忘記了這個人是多麼的不正經——儘管這是黑羽快斗後來才發現的事情。\n\n富松柚希一連串的講了她的生活，黑羽快斗看似沒有認真聽——不，即便是他自己都覺得他沒有認真在聽，實際上他卻將每一句記在心底，以至於他後來想起來她喜歡什麼的時候，他依然想不起來是在什麼時候聽她說過。\n\n他的心跳正以他察覺不到的頻率加速，沒有想像中心跳的聲音，只有富松柚希分享生活的聲線。\n\n像黑羽快斗那麼聰明的人也擅長將自己騙過，可他騙不過所有人，漂亮的雙眼更不會騙人——那溢出的愛意被所有人察覺，唯獨他自己看不到。\n\n那不是當然的嗎？人怎麼輕易看到自己的雙眼呢？除非是照著鏡子，又或者是看著別人眸底的自己。\n\n富松柚希清澈的雙眼藏著恣意的黑羽快斗，他看著她的眼睛時，才發現自己那不對勁的眼神。\n\n——該死，那麼明顯嗎？\n\n是的，所有人都看得出來，他們也都知道青春期的少年會下意識的欺負自己所愛的女孩，可能是為了博關注，也可能是為了讓她記住自己——總之愛的理由千百種，富松柚希是唯一一種。\n\n還記得第一次見你時我的心是怎樣的波動。\n\n如海浪般有頻率的跳著舞，帶著漲潮與退潮的節奏，滿出的海水沖進了沙灘，從如湖面般的雙眼溢了出來。\n\n就像打破了賈西亞·馬奎斯在《百年孤寂》中所隱喻的——「人類難以逃脫的宿命與永恆的孤獨」一般，乏味的人生有了聲音，黑白的世界染上了色彩。\n\n從此我不再是我，你也不再是你，而且被命運交織在一起，成為了「我們」。`
      },
      2: {
        title: "【海賊王 山治夢向】巴拉蒂的雷雨之夜",
        content: `巴拉蒂很久沒有那麼熱鬧的。\n\n艾米斯的加入引起了許多人的討論，山治是其中一個——身為巴拉蒂這艘船上最「了解」女孩子的人，他反而是最興奮的那一個。\n\n山治忙前忙後的準備了許多美食，那一晚巴拉蒂的所有人都吃上了大餐，哲普為了活躍氣氛，還取笑艾米斯，說著：「乾杯！謝謝艾米斯讓我吃上那麼好的一頓！」這樣的話。\n\n巴拉蒂的船員對她很好，哲普還特意隔出了一個小房間給艾米斯 Sus——畢竟她是這艘船上唯一的女性，有獨立的空間不論從各方面來說都是好事。\n\n但凡事總有變數，例如哲普不會想到有如此不要臉的人，晚上天天去敲艾米斯的房門。\n\n「艾米斯妹妹——我今天研發了新的宵夜喔！」\n「我為了你研發出了超適合你的口味，你一定會喜歡！」\n「艾米斯妹妹——」\n\n艾米斯其實並不會感到煩躁，反而有點竊喜。\n\n對於自己喜歡的人每晚來找自己這種事，她剛開始會笨笨的欣喜開門，到後來會調皮似的故意不理，山治會隔著門問她說是不是生氣了、不想吃宵夜了……殊不知艾米斯只是單純的想搗蛋一下——哲普稱這為他們兩人之間的小情趣，山治聽到後竟還因此感到開心，大概是：「艾米斯妹妹跟我有兩人之間的小情趣！」這樣的心情。\n\n「艾米斯妹妹——」\n\n大雷雨的晚上，山治一如既往的來敲房門，艾米斯也把門打開，他帶著宵夜走了進來。\n\n這樣的模式已經持續了大概三個月之久，山治每天不厭其煩的研發新菜色，調整出最適合艾米斯的口味，艾米斯也每天變著花樣的調皮搗蛋，有時候再說些令人感到曖昧的話。\n\n宵夜吃得很快，畢竟份量不多，山治站起身來準備離去——儘管他一點也不想離開，才剛從床邊站起，艾米斯便拉了拉他的衣角。\n\n「哥哥，今天晚上要不要一起睡？」\n\n男人的眼睛都冒出了愛心，彷彿等了很久一樣。\n\n「當然好！既然是寶貝的邀約，我自然會……」\n「我開玩笑的！」\n「……艾米斯妹妹！！」\n\n艾米斯像是得逞般笑了笑，她很喜歡這樣子逗山治，山治也幾乎每次都上當——不知道是有意還是無意的，總之艾米斯認為山治是一個很好騙的人，至少在感情上是這樣的。\n\n「真的要的話……也不是不行。」\n\n艾米斯關上了房間的燈，躺在床上拉了拉被子，被子近乎蓋住了她半張臉。\n\n「我先睡了！你自己看著辦！」\n\n某男興奮的轉圈出去洗完盤子再轉回來。\n\n「艾米斯妹妹，我來了！」\n\n山治小心翼翼的拉開被子的一角，確認艾米斯沒有排斥他上床，這才整個人都滾了進去。\n\n艾米斯的床不大——畢竟原本就是單人床，如今兩個人躺在上面顯得格外擁擠，艾米斯往旁邊挪了痕，卻意外的與山治靠的更近了。\n\n「那、那個……」\n\n山治似乎也是第一次跟女孩子躺在同一張床上，鮮少看到紅暈爬滿他的臉頰，可惜燈光太暗，艾米斯無法用眼睛為此刻按下快門。\n\n「哥哥，晚安。」\n\n她朝山治的方向又挪了痕，事實上她從一開始就是故意的，而山治似乎沒有發現，只是僵硬的躺在那裡，任由艾米斯的髮絲撓著他的肌膚，在雷雨中徹夜難眠。\n\n山治大概很想念那麼一個晚上，跟艾米斯共度的，一個大雷雨的夜晚。`
      },
      3: {
        title: "【名偵探柯南 新志同人文】新志｜Adonis",
        content: `英國的冬天很冷，冷到宮野志保的嘴唇有些發紫。\n\n來英國已經三年了，這三年來她埋首於實驗，開始想英國好像也沒有如記憶中那般美好，她討厭英國的空氣，也討厭大笨鐘不斷提醒著人們時間的流逝，她開始討厭某些東西，卻唯獨不討厭福爾摩斯。\n\n她不清楚是作為灰原哀的記憶在作祟，還是單純的覺得福爾摩斯沒有那麼討厭——貝克街221B的門牌真實存在著，但所有人都知道這是一個不存在的住址。\n\n（...以下因篇幅關係省略，您原始檔案中的完整小說文字均可照此格式填寫在此...）`
      }
    };

    // 切換分頁控制
    function switchPage(pageId) {
      document.querySelectorAll('.page').forEach(page => page.classList.remove('active'));
      document.getElementById('page-' + pageId).classList.add('active');
      
      // 切換到進度或留言板時，自動從試算表撈最新資料
      if (pageId === 'schedule') {
        fetchOrderList();
      } else if (pageId === 'feedback-view') {
        fetchFeedbackList();
      }
    }

    // 彈出視窗控制
    function openArticle(id) {
      const art = articles[id];
      if (art) {
        document.getElementById('modalTitle').innerText = art.title;
        document.getElementById('modalBody').innerText = art.content;
        document.getElementById('articleModal').classList.add('show');
      }
    }
    function closeModal() {
      document.getElementById('articleModal').classList.remove('show');
    }

    // 表單傳送通用 Fetch 函數
    async function callGAS(action, formData = {}) {
      try {
        const response = await fetch(GAS_API_URL, {
          method: "POST",
          mode: "cors",
          headers: { "Content-Type": "text/plain" }, // 避免 CORS 預檢失敗
          body: JSON.stringify({ action, formData })
        });
        return await response.json();
      } catch (error) {
        console.error("API 連線失敗:", error);
        return { success: false, error: "無法連線至雲端資料庫" };
      }
    }

    // 處理提交新委託
    async function handleOrderSubmit(e) {
      e.preventDefault();
      const formData = {
        name: document.getElementById('orderName').value,
        type: document.getElementById('orderType').value,
        words: document.getElementById('orderWords').value,
        payment: document.getElementById('orderPayment').value,
        requiredDate: document.getElementById('orderRequiredDate').value
      };
      
      alert("正在送出委託，請稍候...");
      const res = await callGAS("submitOrder", formData);
      if (res.success) {
        alert(`🎉 委託成功！\n預計交期大約在: ${res.data.estDate}`);
        document.getElementById('orderForm').reset();
        switchPage('schedule');
      } else {
        alert("提交失敗：" + res.error);
      }
    }

    // 從試算表「排單系統」取得進度
    async function fetchOrderList() {
      const tbody = document.getElementById('orderTableBody');
      tbody.innerHTML = `<tr><td colspan="7" class="loading">正在從雲端載入進度列表...</td></tr>`;
      
      const res = await callGAS("getOrderList");
      if (res.success && res.data.length > 0) {
        tbody.innerHTML = "";
        res.data.forEach(item => {
          const badgeClass = item.status === "已完稿" ? "status-done" : "status-pending";
          tbody.innerHTML += `
            <tr>
              <td>\${item.name}</td>
              <td>\${item.type}</td>
              <td>\${item.words}</td>
              <td>\${item.payment}</td>
              <td>\${item.requiredDate}</td>
              <td>\${item.estDate}</td>
              <td><span class="status-badge \${badgeClass}">\${item.status}</span></td>
            </tr>
          `;
        });
      } else {
        tbody.innerHTML = `<tr><td colspan="7" class="loading">目前暫無排單資料。</td></tr>`;
      }
    }

    // 處理提交客戶回饋
    async function handleFeedbackSubmit(e) {
      e.preventDefault();
      const formData = {
        user: document.getElementById('fbUser').value,
        level: document.getElementById('fbLevel').value,
        message: document.getElementById('fbMessage').value
      };
      
      const res = await callGAS("submitFeedback", formData);
      if (res.success) {
        alert("留言成功！感謝您的回饋！");
        document.getElementById('feedbackForm').reset();
        fetchFeedbackList();
      } else {
        alert("留言失敗：" + res.error);
      }
    }

    // 從試算表「委託回饋」取得留言列表
    async function fetchFeedbackList() {
      const container = document.getElementById('feedbackContainer');
      container.innerHTML = `<div class="loading">正在載入客戶精彩留言...</div>`;
      
      const res = await callGAS("getFeedbackList");
      if (res.success && res.data.length > 0) {
        container.innerHTML = "";
        res.data.reverse().forEach(item => { // 新留言排在最上面
          container.innerHTML += `
            <div class="fb-item">
              <div class="fb-meta">
                <span class="fb-user">👤 \${item.user}</span>
                <span>📅 \${item.date}</span>
              </div>
              <div class="fb-level">\${item.level}</div>
              <div class="fb-text">\${item.message}</div>
            </div>
          `;
        });
      } else {
        container.innerHTML = `<div class="loading">目前還沒有留言，歡迎成為第一個留言的人！</div>`;
      }
    }
  </script>
</body>
</html>
