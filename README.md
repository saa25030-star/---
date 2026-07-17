<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>水の都・愛媛県西条市紹介ナビ</title>
    <style>
        /* -----------------------------------------
           共通・基本スタイル
        ----------------------------------------- */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: "Helvetica Neue", Arial, "Hiragino Kaku Gothic ProN", "Hiragino Sans", Meiryo, sans-serif;
            line-height: 1.8;
            color: #333;
            background-color: #f7fbfe; /* ほんのり青みがかった白 */
        }
        a {
            text-decoration: none;
            color: inherit;
        }
        li {
            list-style: none;
        }
        img {
            max-width: 100%;
            height: auto;
            display: block;
        }
        .container {
            width: 100%;
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 20px;
        }
        .section-title {
            text-align: center;
            font-size: 2rem;
            color: #0d5c75;
            margin-bottom: 40px;
            position: relative;
            padding-bottom: 10px;
        }
        .section-title::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 50%;
            transform: translateX(-50%);
            width: 60px;
            height: 3px;
            background-color: #3498db;
        }

        /* -----------------------------------------
           ヘッダー
        ----------------------------------------- */
        header {
            background-color: rgba(255, 255, 255, 0.95);
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .header-inner {
            display: flex;
            justify-content: space-between;
            align-items: center;
            height: 70px;
        }
        .logo {
            font-size: 1.5rem;
            font-weight: bold;
            color: #0d5c75;
        }
        .logo span {
            color: #3498db;
        }
        .nav-links {
            display: flex;
            gap: 25px;
        }
        .nav-links a {
            font-weight: 500;
            transition: color 0.3s;
        }
        .nav-links a:hover {
            color: #3498db;
        }

        /* -----------------------------------------
           メインビジュアル（ヒーローエリア）
        ----------------------------------------- */
        .hero {
            background: linear-gradient(rgba(13, 92, 117, 0.6), rgba(52, 152, 219, 0.4)), 
                        url('https://images.unsplash.com/photo-1501785888041-af3ef285b470?auto=format&fit=crop&w=1200&q=80') no-repeat center center/cover;
            /* ※写真はダミーの美しい川・山イメージです。お好きな西条市の画像に差し替え可能です */
            height: 500px;
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
            color: #fff;
        }
        .hero-content h1 {
            font-size: 2.8rem;
            margin-bottom: 15px;
            text-shadow: 2px 2px 10px rgba(0,0,0,0.5);
            letter-spacing: 0.1em;
        }
        .hero-content p {
            font-size: 1.2rem;
            margin-bottom: 30px;
            text-shadow: 1px 1px 5px rgba(0,0,0,0.5);
        }
        .btn {
            display: inline-block;
            background-color: #e67e22;
            color: #fff;
            padding: 12px 30px;
            border-radius: 25px;
            font-weight: bold;
            transition: background-color 0.3s, transform 0.2s;
            box-shadow: 0 4px 15px rgba(230, 126, 34, 0.4);
        }
        .btn:hover {
            background-color: #d35400;
            transform: translateY(-2px);
        }

        /* -----------------------------------------
           特徴セクション (3大ミリョク)
        ----------------------------------------- */
        .features {
            padding: 80px 0;
            background-color: #fff;
        }
        .features-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 30px;
        }
        .feature-card {
            background-color: #fcfdfe;
            border: 1px solid #e1f0fa;
            border-radius: 12px;
            padding: 30px;
            text-align: center;
            transition: transform 0.3s, box-shadow 0.3s;
        }
        .feature-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(13, 92, 117, 0.1);
        }
        .feature-icon {
            font-size: 3rem;
            margin-bottom: 15px;
        }
        .feature-card h3 {
            font-size: 1.4rem;
            color: #0d5c75;
            margin-bottom: 15px;
        }

        /* -----------------------------------------
           観光スポット（テーブル）
        ----------------------------------------- */
        .spots {
            padding: 80px 0;
        }
        .spots-table {
            width: 100%;
            border-collapse: collapse;
            background-color: #fff;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
        }
        .spots-table th, .spots-table td {
            padding: 18px 24px;
            text-align: left;
        }
        .spots-table th {
            background-color: #0d5c75;
            color: #fff;
            font-weight: bold;
        }
        .spots-table tr {
            border-bottom: 1px solid #eef2f5;
        }
        .spots-table tr:last-child {
            border-bottom: none;
        }
        .spots-table tr:nth-child(even) {
            background-color: #fcfdfe;
        }
        .spot-badge {
            display: inline-block;
            background-color: #e1f0fa;
            color: #0d5c75;
            padding: 4px 10px;
            border-radius: 15px;
            font-size: 0.85rem;
            font-weight: bold;
        }

        /* -----------------------------------------
           グルメセクション
        ----------------------------------------- */
        .gourmet {
            padding: 80px 0;
            background-color: #fff;
        }
        .gourmet-list {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
            gap: 30px;
        }
        .gourmet-item {
            border-radius: 10px;
            overflow: hidden;
            background: #f7fbfe;
            box-shadow: 0 5px 15px rgba(0,0,0,0.03);
        }
        .gourmet-img {
            height: 200px;
            background-size: cover;
            background-position: center;
        }
        .gourmet-info {
            padding: 25px;
        }
        .gourmet-info h3 {
            color: #0d5c75;
            margin-bottom: 10px;
            font-size: 1.3rem;
        }

        /* -----------------------------------------
           移住セクション (Call to Action)
        ----------------------------------------- */
        .migration {
            padding: 100px 0;
            background: linear-gradient(135deg, #0d5c75, #3498db);
            color: #fff;
            text-align: center;
        }
        .migration h2 {
            font-size: 2.3rem;
            margin-bottom: 20px;
        }
        .migration p {
            max-width: 700px;
            margin: 0 auto 40px auto;
            font-size: 1.1rem;
            line-height: 1.8;
        }
        .btn-white {
            background-color: #fff;
            color: #0d5c75;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }
        .btn-white:hover {
            background-color: #f0f4f8;
            color: #0d5c75;
        }

        /* -----------------------------------------
           フッター
        ----------------------------------------- */
        footer {
            background-color: #2c3e50;
            color: #bdc3c7;
            text-align: center;
            padding: 40px 0;
            font-size: 0.9rem;
        }
        footer p {
            margin-bottom: 10px;
        }

        /* -----------------------------------------
           レスポンシブ（スマホ表示調整）
        ----------------------------------------- */
        @media (max-width: 768px) {
            .header-inner {
                flex-direction: column;
                height: auto;
                padding: 15px 0;
                gap: 15px;
            }
            .hero-content h1 {
                font-size: 1.8rem;
            }
            .hero-content p {
                font-size: 1rem;
            }
            .spots-table {
                display: block;
                overflow-x: auto;
            }
            .section-title {
                font-size: 1.6rem;
            }
        }
    </style>
</head>
<body>

    <!-- ヘッダー -->
    <header>
        <div class="container header-inner">
            <div class="logo">水の都・西条<span>Navi</span></div>
            <nav>
                <ul class="nav-links">
                    <li><a href="#about">西条の魅力</a></li>
                    <li><a href="#spots">スポット</a></li>
                    <li><a href="#gourmet">グルメ</a></li>
                    <li><a href="#migration">移住情報</a></li>
                </ul>
            </nav>
        </div>
    </header>

    <!-- メインビジュアル -->
    <section class="hero">
        <div class="hero-content container">
            <h1>西日本最高峰の麓、名水が湧き出る「水の都」へ</h1>
            <p>愛媛県西条市は、石鎚山の大自然と名水「うちぬき」が恵みをもたらす美しい街です。</p>
            <a href="#about" class="btn">西条を旅する</a>
        </div>
    </section>

    <!-- 特徴（3大ミリョク） -->
    <section id="about" class="features">
        <div class="container">
            <h2 class="section-title">西条市の3大ミリョク</h2>
            <div class="features-grid">
                <div class="feature-card">
                    <div class="feature-icon">🏔️</div>
                    <h3>西日本最高峰・石鎚山</h3>
                    <p>標高1,982mを誇る霊峰。四季折々のアウトドアや、息をのむ絶景が登山客を惹きつけます。</p>
                </div>
                <div class="feature-card">
                    <div class="feature-icon">💧</div>
                    <h3>名水「うちぬき」</h3>
                    <p>石鎚山系の清らかな地下水が自噴する井戸。市内あちこちで、極上のおいしい水が無料で楽しめます。</p>
                </div>
                <div class="feature-card">
                    <div class="feature-icon">🏮</div>
                    <h3>熱気溢れる「西条まつり」</h3>
                    <p>秋に行われる伝統的な祭。150台以上もの豪華絢爛な「だんじり」「みこし」が街を埋め尽くします。</p>
                </div>
            </div>
        </div>
    </section>

    <!-- おすすめ観光スポット -->
    <section id="spots" class="spots">
        <div class="container">
            <h2 class="section-title">おすすめ観光スポット</h2>
            <table class="spots-table">
                <thead>
                    <tr>
                        <th style="width: 30%;">スポット名</th>
                        <th style="width: 20%;">ジャンル</th>
                        <th style="width: 50%;">ここがイチオシ！</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td><strong>石鎚登山ロープウェイ</strong></td>
                        <td><span class="spot-badge">アウトドア</span></td>
                        <td>初心者でも気軽に標高1,300m地点へ。そこから眺める瀬戸内海や山々の大パノラマは圧巻！</td>
                    </tr>
                    <tr>
                        <td><strong>アクアトピア水系</strong></td>
                        <td><span class="spot-badge">散策・癒やし</span></td>
                        <td>「水の都」西条を象徴する美しい親水水路。澄み切った水が流れる川辺をのんびりお散歩。</td>
                    </tr>
                    <tr>
                        <td><strong>鉄道歴史パーク in SAIJO</strong></td>
                        <td><span class="spot-badge">ファミリー</span></td>
                        <td>新幹線生みの親・十河信二（そごうしんじ）ゆかりの博物館。貴重な初代「0系新幹線」の実物展示も！</td>
                    </tr>
                    <tr>
                        <td><strong>湯之谷温泉</strong></td>
                        <td><span class="spot-badge">温泉</span></td>
                        <td>石鎚山のふもとにある歴史ある名湯。薪で沸かした極上の湯で旅の疲れをじんわり癒やせます。</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </section>

    <!-- ローカルグルメ -->
    <section id="gourmet" class="gourmet">
        <div class="container">
            <h2 class="section-title">西条ローカルグルメ</h2>
            <div class="gourmet-list">
                
                <div class="gourmet-item">
                    <!-- サンプル画像（ナポリタンのイメージ） -->
                    <div class="gourmet-img" style="background-image: url('https://images.unsplash.com/photo-1551183053-bf91a1d81141?auto=format&fit=crop&w=500&q=80');"></div>
                    <div class="gourmet-info">
                        <h3>西条てっぱんナポリタン</h3>
                        <p>熱々の鉄板の上にとき卵を流し込み、その上にナポリタンを盛り付けるスタイル。西条市民が愛してやまない王道B級グルメです！</p>
                    </div>
                </div>

                <div class="gourmet-item">
                    <!-- サンプル画像（ビールのイメージ） -->
                    <div class="gourmet-img" style="background-image: url('https://images.unsplash.com/photo-1608270586620-248524c67de9?auto=format&fit=crop&w=500&q=80');"></div>
                    <div class="gourmet-info">
                        <h3>名水育ちの地酒＆ビール</h3>
                        <p>全国に誇る「うちぬき」の水で仕込まれた極上の日本酒や、四国で唯一のアサヒビール四国工場で作られるフレッシュなビールは最高の味わい！</p>
                    </div>
                </div>

            </div>
        </div>
    </section>

    <!-- 移住セクション -->
    <section id="migration" class="migration">
        <div class="container">
            <h2>実は「住みたい田舎」全国No.1！</h2>
            <p>西条市は、宝島社の『田舎暮らしの本』で、全国住みたい田舎ベストランキング第1位（若者世代・子育て世代など）を何度も獲得しています。美味しい水、無料の生活水（一部地域）、充実した移住お試し制度など、最高のライフスタイルが待っています。</p>
            <a href="https://www.city.saijo.ehime.jp/" target="_blank" class="btn btn-white">西条市 公式サイトを見る</a>
        </div>
    </section>

    <!-- フッター -->
    <footer>
        <div class="container">
            <p>© 2026 水の都・西条市 観光情報ポータル. All Rights Reserved.</p>
            <p style="font-size: 0.8rem; color: #7f8c8d;">※本ページは観光PR用のデモサイトです。</p>
        </div>
    </footer>

</body>
</html>
