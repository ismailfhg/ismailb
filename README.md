<!doctype html>
<html lang="tr">

<head>
    <meta charset="utf-8">
    <title>VideoApp • Güvenli Giriş, Doğrulama ve Admin (Tam)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        :root {
            --bg: #0b0f1f;
            --text: #e6e9ff;
            --muted: #9aa2c7;
            --card: #121428;
            --accent1: #5f7cff;
            --accent2: #00d4ff;
            --danger: #ff5f7a;
            --success: #3bd36b;
        }

        * {
            box-sizing: border-box
        }

        body {
            margin: 0;
            font-family: Inter, Segoe UI, system-ui, -apple-system, sans-serif;
            color: var(--text);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: var(--bg);
            overflow: hidden;
        }

        canvas#bg {
            position: fixed;
            inset: 0;
            z-index: -1
        }

        .glow {
            position: fixed;
            inset: -10%;
            z-index: -2;
            background:
                radial-gradient(800px 400px at 15% 20%, rgba(95, 124, 255, .15), transparent 60%),
                radial-gradient(700px 350px at 85% 80%, rgba(0, 212, 255, .18), transparent 60%);
            animation: hue 20s ease-in-out infinite;
        }

        @keyframes hue {
            0% {
                filter: hue-rotate(0)
            }

            50% {
                filter: hue-rotate(30deg) brightness(1.06)
            }

            100% {
                filter: hue-rotate(0)
            }
        }

        .card {
            width: 100%;
            max-width: 1080px;
            display: grid;
            grid-template-columns: 520px 1fr;
            gap: 18px;
            background: rgba(18, 20, 40, .7);
            border: 1px solid rgba(255, 255, 255, .08);
            border-radius: 18px;
            backdrop-filter: blur(10px) saturate(130%);
            box-shadow: 0 18px 50px rgba(0, 0, 0, .45);
            padding: 18px;
            animation: pop .6s ease;
        }

        @keyframes pop {
            from {
                opacity: 0;
                transform: translateY(16px)
            }

            to {
                opacity: 1;
                transform: translateY(0)
            }
        }

        .panel {
            background: rgba(12, 14, 30, .6);
            border: 1px solid rgba(255, 255, 255, .1);
            border-radius: 14px;
            padding: 16px
        }

        h1 {
            margin: 0 0 10px;
            font-size: 1.7rem;
            font-weight: 800;
            background: linear-gradient(90deg, #fff, #a8b5ff);
            -webkit-background-clip: text;
            color: transparent
        }

        .tabs {
            display: flex;
            gap: 8px;
            margin: 10px 0 12px
        }

        .tab {
            flex: 1;
            text-align: center;
            padding: 10px;
            border-radius: 10px;
            cursor: pointer;
            background: #1a1d32;
            color: #9aa2c7;
            border: 1px solid rgba(255, 255, 255, .08);
            transition: .2s
        }

        .tab.active {
            background: linear-gradient(90deg, var(--accent1), var(--accent2));
            color: #0b0e1e;
            font-weight: 700
        }

        .section {
            display: none;
            animation: fade .25s ease
        }

        .section.active {
            display: block
        }

        @keyframes fade {
            from {
                opacity: .0;
                transform: translateY(6px)
            }

            to {
                opacity: 1;
                transform: translateY(0)
            }
        }

        .field {
            margin-bottom: 10px;
            display: flex;
            flex-direction: column;
            gap: 6px
        }

        .field label {
            font-size: .9rem;
            color: #9aa2c7
        }

        .input {
            padding: 12px 14px;
            border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, .12);
            background: #0f1126;
            color: var(--text);
            outline: none;
            transition: .18s border, .18s box-shadow;
        }

        .input:focus {
            border-color: var(--accent2);
            box-shadow: 0 0 0 3px rgba(0, 212, 255, .18)
        }

        .row {
            display: flex;
            gap: 10px;
            align-items: center;
            flex-wrap: wrap
        }

        .btn {
            border: none;
            padding: 12px 14px;
            border-radius: 12px;
            font-weight: 700;
            cursor: pointer;
            background: linear-gradient(90deg, var(--accent1), var(--accent2));
            color: #0b0e1e;
            transition: .18s transform, .18s filter;
        }

        .btn:hover {
            transform: translateY(-1px)
        }

        .mutedBtn {
            background: #1a1d32;
            color: #9aa2c7
        }

        .error {
            color: var(--danger);
            font-size: .88rem;
            display: none
        }

        .note {
            color: #9aa2c7;
            font-size: .9rem
        }

        .meterWrap {
            height: 10px;
            background: #1a1d32;
            border: 1px solid rgba(255, 255, 255, .08);
            border-radius: 999px;
            overflow: hidden
        }

        .meter {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #ff5f7a, #ffd166, #3bd36b)
        }

        .toast {
            position: fixed;
            left: 50%;
            transform: translateX(-50%);
            bottom: 22px;
            background: #11132a;
            color: var(--text);
            border: 1px solid rgba(255, 255, 255, .12);
            padding: 10px 12px;
            border-radius: 12px;
            box-shadow: 0 12px 30px rgba(0, 0, 0, .35);
            display: none;
            min-width: 260px;
            text-align: center;
            z-index: 40;
        }

        .greet {
            position: fixed;
            inset: 0;
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 35;
            background: rgba(0, 0, 0, .6);
            backdrop-filter: blur(6px)
        }

        .greetBox {
            background: #121428;
            border: 1px solid rgba(255, 255, 255, .12);
            border-radius: 18px;
            padding: 24px 28px;
            box-shadow: 0 12px 30px rgba(0, 0, 0, .35);
            text-align: center
        }

        .greetTitle {
            font-size: 1.7rem;
            font-weight: 800;
            background: linear-gradient(90deg, #fff, #a8b5ff);
            -webkit-background-clip: text;
            color: transparent
        }

        #videos {
            display: none;
            width: 100%;
            max-width: 1080px;
            padding: 18px
        }

        .header {
            display: flex;
            align-items: center;
            justify-content: space-between
        }

        .badge {
            padding: 8px 12px;
            border: 1px solid rgba(255, 255, 255, .18);
            border-radius: 999px;
            color: #9aa2c7;
            background: #10122a
        }

        .smallBtn {
            padding: 8px 12px;
            border: none;
            border-radius: 10px;
            background: #222642;
            color: #fff;
            cursor: pointer
        }

        .adminPanel {
            background: #10122a;
            border: 1px solid rgba(255, 255, 255, .12);
            border-radius: 12px;
            padding: 12px;
            margin: 12px 0
        }

        .adminPanel .row .input {
            flex: 1
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 12px;
            margin-top: 12px
        }

        .cardV {
            background: #1a1d32;
            border: 1px solid rgba(255, 255, 255, .08);
            border-radius: 12px;
            overflow: hidden
        }

        .thumb {
            aspect-ratio: 16/9;
            background: #0c0f22;
            display: flex;
            align-items: center;
            justify-content: center
        }

        .meta {
            padding: 10px;
            display: flex;
            align-items: center;
            justify-content: space-between
        }

        iframe {
            width: 100%;
            height: 100%;
            border: none
        }

        video {
            width: 100%;
            height: 100%;
            background: #000
        }

        @media (max-width:900px) {
            .card {
                grid-template-columns: 1fr
            }
        }

        .overlay {
            position: fixed;
            inset: 0;
            display: none;
            align-items: center;
            justify-content: center;
            background: rgba(5, 7, 18, .7);
            backdrop-filter: blur(6px);
            z-index: 60
        }

        .modal {
            width: 92%;
            max-width: 520px;
            background: #121428;
            border: 1px solid rgba(255, 255, 255, .12);
            border-radius: 18px;
            box-shadow: 0 18px 50px rgba(0, 0, 0, .4);
            padding: 18px;
            animation: pop .3s ease;
        }

        .modal h2 {
            margin: 0 0 8px;
            font-size: 1.4rem;
            font-weight: 800;
            background: linear-gradient(90deg, #fff, #a8b5ff);
            -webkit-background-clip: text;
            color: transparent
        }
    </style>
</head>

<body>
    <div class="glow"></div>
    <canvas id="bg"></canvas>

    <div class="card" id="auth">
        <div class="panel">
            <h1>VideoApp • Giriş</h1>
            <div class="tabs">
                <div class="tab active" data-tab="login">Giriş</div>
                <div class="tab" data-tab="register">Kayıt</div>
                <div class="tab" data-tab="verify">E‑posta doğrula</div>
                <div class="tab" data-tab="reset">Şifre yenile</div>
                <div class="tab" data-tab="admin">Admin</div>
            </div>

            <div id="login" class="section active">
                <div class="field"><label>E‑posta veya telefon</label><input id="loginId" class="input"
                        placeholder="ornek@mail.com veya 0501234567"></div>
                <div class="field"><label>Şifre</label><input id="loginPass" type="password" class="input"
                        placeholder="••••••••"></div>
                <div class="row">
                    <button class="btn" id="loginBtn">Giriş yap</button>
                    <button class="mutedBtn btn" id="toRegister">Hesap oluştur</button>
                </div>
                <div id="loginError" class="error"></div>
                <div class="note">E‑posta hesabı varsa doğrulanmadan giriş olmaz. Telefonla giriş serbesttir.</div>
            </div>

            <div id="register" class="section">
                <div class="field"><label>E‑posta</label><input id="regEmail" class="input"
                        placeholder="ornek@mail.com"></div>
                <div class="field"><label>Telefon</label><input id="regPhone" class="input" placeholder="0501234567">
                </div>
                <div class="field">
                    <label>Şifre</label><input id="regPass" type="password" class="input"
                        placeholder="En az 8 karakter">
                    <div class="meterWrap">
                        <div id="meter" class="meter"></div>
                    </div>
                    <div class="note" id="meterNote">Şifre gücü: zayıf</div>
                </div>
                <div class="row"><input id="regAdmin" type="checkbox"><label>Bu hesabı admin yap (video ekleme
                        yetkisi)</label></div>
                <div class="row"><button class="btn" id="registerBtn">Kayıt ol</button><button class="mutedBtn btn"
                        id="toLogin1">Geri dön</button></div>
                <div id="regError" class="error"></div>
                <div class="note">Kayıttan sonra doğrulama kodu üretilecek. “E‑posta doğrula” sekmesinden gir.</div>
            </div>

            <div id="verify" class="section">
                <div class="field"><label>Kayıtlı e‑posta</label><input id="verEmail" class="input"
                        placeholder="ornek@mail.com"></div>
                <div class="field"><label>Doğrulama kodu</label><input id="verCode" class="input"
                        placeholder="6 haneli kod"></div>
                <div class="row"><button class="btn" id="verifyBtn">Doğrula</button><button class="mutedBtn btn"
                        id="resendBtn">Kodu gönder</button></div>
                <div id="verError" class="error"></div>
                <div class="note" id="verHint">Kod demo olarak ekranda gösterilir.</div>
            </div>

            <div id="reset" class="section">
                <div class="field"><label>Kayıtlı e‑posta</label><input id="resetEmail" class="input"
                        placeholder="ornek@mail.com"></div>
                <div class="field"><label>Yeni şifre</label><input id="resetPass" type="password" class="input"
                        placeholder="Yeni şifre"></div>
                <div class="row"><button class="btn" id="resetBtn">Şifreyi yenile</button><button class="mutedBtn btn"
                        id="toLogin2">Geri dön</button></div>
                <div id="resetError" class="error"></div>
            </div>

            <div id="admin" class="section">
                <div class="field"><label>Admin adı</label><input id="adminName" class="input" placeholder="Ad"></div>
                <div class="field"><label>Admin şifre</label><input id="adminPass" type="password" class="input"
                        placeholder="Şifre"></div>
                <div class="row"><button class="btn" id="adminBtn">Admin giriş</button></div>
                <div id="adminError" class="error"></div>
                <div class="note">Şifre kodda görünmez; hash ile doğrulanır.</div>
            </div>
        </div>

        <div class="panel">
            <h1>Özellikler</h1>
            <div class="note">• E‑posta doğrulama, telefonla giriş, şifre yenileme.</div>
            <div class="note">• Güçlü şifre ölçer, brute‑force sınırlama, XSS kaçışı.</div>
            <div class="note">• Admin videoları ekler/siler. Her https URL kabul edilir, otomatik embed’e çevrilir.
            </div>
        </div>
    </div>

    <div class="greet" id="greet">
        <div class="greetBox">
            <div class="greetTitle">Hoş geldiniz!</div>
            <div class="note">Video sayfasına geçiliyor…</div>
        </div>
    </div>

    <div id="videos">
        <div class="header">
            <div class="badge" id="userBadge">Misafir</div>
            <button class="smallBtn" id="logoutBtn">Çıkış</button>
        </div>

        <div id="adminPanel" class="adminPanel" style="display:none">
            <div class="note">URL https:// ile başlamalı. YouTube/Vimeo/Dailymotion otomatik embed; .mp4 dosyaları video
                oynatıcıda açılır.</div>
            <div class="row" style="margin-top:8px">
                <input id="videoTitle" class="input" placeholder="Video başlığı">
                <input id="videoUrl" class="input"
                    placeholder="Video URL (ör: https://youtu.be/ID, https://vimeo.com/ID, https://.../video.mp4)">
            </div>
            <div class="row" style="margin-top:8px">
                <button class="btn" id="addVideoBtn">Videoyu ekle</button>
                <button class="mutedBtn btn" id="seedBtn">Uzun demo yükle</button>
            </div>
        </div>

        <div class="grid" id="videoGrid"></div>
    </div>

    <div class="overlay" id="setupOverlay">
        <div class="modal">
            <h2>Admin kurulum</h2>
            <div class="note">İlk kez açtın. Admin adını ve şifreni belirle. Kodda düz şifre saklanmaz.</div>
            <div class="field"><label>Admin adı</label><input id="setupName" class="input" placeholder="Ad"></div>
            <div class="field"><label>Admin şifre</label><input id="setupPass" type="password" class="input"
                    placeholder="Güçlü şifre"></div>
            <div class="row"><button class="btn" id="setupSave">Kaydet</button></div>
            <div id="setupError" class="error"></div>
        </div>
    </div>

    <div class="toast" id="toast"></div>

    <script>
        function $(id) { return document.getElementById(id); }
        function showToast(msg, kind = 'info') {
            const t = $('toast');
            t.textContent = msg; t.style.display = 'block';
            t.style.borderColor = kind === 'error' ? 'rgba(255,95,122,.5)' : kind === 'success' ? 'rgba(59,211,107,.5)' : 'rgba(255,255,255,.12)';
            setTimeout(() => t.style.display = 'none', 2400);
        }
        function showErr(el, msg) { el.textContent = msg; el.style.display = 'block'; }
        function escapeHtml(s) { return String(s).replace(/[&<>"']/g, m => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[m])); }
        function sanitize(s) { return s.replace(/\s+/g, ' ').trim(); }

        // Partikül animasyonu
        const canvas = $('bg'), ctx = canvas.getContext('2d');
        function resize() { canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
        window.addEventListener('resize', resize); resize();
        const particles = Array.from({ length: 130 }, () => ({
            x: Math.random() * canvas.width, y: Math.random() * canvas.height,
            r: Math.random() * 2.5 + 1, dx: (Math.random() - 0.5) * 1.2, dy: (Math.random() - 0.5) * 1.2,
            hue: Math.floor(Math.random() * 360)
        }));
        (function tick() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            particles.forEach(p => {
                ctx.beginPath(); ctx.fillStyle = `hsla(${p.hue},70%,70%,0.85)`; ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2); ctx.fill();
                p.x += p.dx; p.y += p.dy; if (p.x < 0 || p.x > canvas.width) p.dx *= -1; if (p.y < 0 || p.y > canvas.height) p.dy *= -1;
            });
            requestAnimationFrame(tick);
        })();

        // Storage
        const LS = {
            get(k, def) { try { const v = JSON.parse(localStorage.getItem(k)); return v ?? def; } catch { return def } },
            set(k, v) { try { localStorage.setItem(k, JSON.stringify(v)); } catch { } },
            remove(k) { try { localStorage.removeItem(k); } catch { } }
        };

        // Demo videolar
        function seedLongVideos() {
            LS.set('videos', [
                { id: crypto.randomUUID(), title: 'Big Buck Bunny (10 dk)', url: 'https://www.youtube.com/embed/aqz-KE-bpKQ', createdAt: Date.now(), kind: 'iframe' },
                { id: crypto.randomUUID(), title: 'Sintel (15 dk)', url: 'https://www.youtube.com/embed/eRsGyueVLvQ', createdAt: Date.now(), kind: 'iframe' },
                { id: crypto.randomUUID(), title: 'Tears of Steel (12 dk)', url: 'https://www.youtube.com/embed/R6MlUcmOul8', createdAt: Date.now(), kind: 'iframe' }
            ]);
        }
        if (!LS.get('videos')) seedLongVideos();

        // Demo kullanıcı
        if (!LS.get('users')) {
            LS.set('users', [
                { id: crypto.randomUUID(), email: 'demo@mail.com', phone: '0500000000', pass: 'Demo123!', role: 'user', active: true, verifyCode: null }
            ]);
        }

        // UI refs
        const tabs = document.querySelectorAll('.tab');
        const sections = { login: $('login'), register: $('register'), verify: $('verify'), reset: $('reset'), admin: $('admin') };
        const toRegister = $('toRegister'), toLogin1 = $('toLogin1'), toLogin2 = $('toLogin2');

        const loginId = $('loginId'), loginPass = $('loginPass'), loginBtn = $('loginBtn'), loginError = $('loginError');

        const regEmail = $('regEmail'), regPhone = $('regPhone'), regPass = $('regPass'), regAdmin = $('regAdmin');
        const registerBtn = $('registerBtn'), regError = $('regError'), meter = $('meter'), meterNote = $('meterNote');

        const verEmail = $('verEmail'), verCode = $('verCode'), verifyBtn = $('verifyBtn'), verError = $('verError'), verHint = $('verHint'), resendBtn = $('resendBtn');

        const resetEmail = $('resetEmail'), resetPass = $('resetPass'), resetBtn = $('resetBtn'), resetError = $('resetError');

        const auth = $('auth'), greet = $('greet');

        const videosView = $('videos'), userBadge = $('userBadge'), logoutBtn = $('logoutBtn'), adminPanel = $('adminPanel');
        const addVideoBtn = $('addVideoBtn'), seedBtn = $('seedBtn'), videoTitle = $('videoTitle'), videoUrl = $('videoUrl'), videoGrid = $('videoGrid');

        const adminName = $('adminName'), adminPass = $('adminPass'), adminBtn = $('adminBtn'), adminError = $('adminError');

        // Admin kurulum refs
        const setupOverlay = $('setupOverlay'), setupName = $('setupName'), setupPass = $('setupPass'), setupSave = $('setupSave'), setupError = $('setupError');

        // Tabs
        tabs.forEach(t => {
            t.addEventListener('click', () => {
                tabs.forEach(x => x.classList.remove('active')); t.classList.add('active');
                Object.values(sections).forEach(s => s.classList.remove('active'));
                sections[t.dataset.tab].classList.add('active');
            });
        });
        toRegister?.addEventListener('click', () => document.querySelector('[data-tab="register"]').click());
        toLogin1?.addEventListener('click', () => document.querySelector('[data-tab="login"]').click());
        toLogin2?.addEventListener('click', () => document.querySelector('[data-tab="login"]').click());

        // Şifre gücü
        regPass.addEventListener('input', () => {
            const v = regPass.value;
            const score = strength(v);
            meter.style.width = Math.min(100, score * 25) + '%';
            meterNote.textContent = 'Şifre gücü: ' + (score < 2 ? 'zayıf' : score < 3 ? 'orta' : score < 4 ? 'iyi' : 'çok iyi');
        });
        function strength(p) {
            let s = 0;
            if (p.length >= 8) s++;
            if (/[A-Z]/.test(p) && /[a-z]/.test(p)) s++;
            if (/[0-9]/.test(p)) s++;
            if (/[^A-Za-z0-9]/.test(p)) s++;
            return s;
        }

        // Brute-force limiter
        function getAttempts() {
            const a = LS.get('attempts', { count: 0, until: 0 });
            const now = Date.now();
            if (a.until && now > a.until) { a.count = 0; a.until = 0; LS.set('attempts', a); }
            return a;
        }
        function recordFail() {
            const a = getAttempts();
            a.count++;
            if (a.count >= 3) { a.until = Date.now() + 15000; }
            LS.set('attempts', a);
        }
        function canLogin() {
            const a = getAttempts();
            if (a.until && Date.now() < a.until) {
                const left = Math.ceil((a.until - Date.now()) / 1000);
                loginError.textContent = `Çok fazla deneme. ${left} sn sonra tekrar deneyin.`;
                loginError.style.display = 'block'; return false;
            }
            return true;
        }

        // Kayıt
        registerBtn.addEventListener('click', () => {
            regError.style.display = 'none'; regError.textContent = '';
            const email = (regEmail.value || '').trim();
            const phone = (regPhone.value || '').trim();
            const pass = regPass.value;
            const makeAdmin = regAdmin.checked;

            if (!email && !phone) { return showErr(regError, 'E‑posta veya telefon gerekli'); }
            if (email && !/^[^@]+@[^@]+\.[^@]+$/.test(email)) { return showErr(regError, 'E‑posta geçersiz'); }
            if (phone && !/^[0-9]{7,15}$/.test(phone)) { return showErr(regError, 'Telefon 7‑15 hane, sadece rakam'); }
            if (strength(pass) < 3) { return showErr(regError, 'Şifre daha güçlü olmalı'); }

            const users = LS.get('users', []);
            if (users.some(u => email && u.email === email) || users.some(u => phone && u.phone === phone)) {
                return showErr(regError, 'Bu kullanıcı zaten var');
            }
            const code = genCode();
            const user = { id: crypto.randomUUID(), email, phone, pass, role: makeAdmin ? 'admin' : 'user', active: false, verifyCode: code };
            users.push(user); LS.set('users', users);

            regEmail.value = ''; regPhone.value = ''; regPass.value = ''; regAdmin.checked = false;
            showToast('Kayıt başarılı. Doğrulama kodu üretildi.', 'success');
            verHint.textContent = 'Kod: ' + code + ' (demo).';
            document.querySelector('[data-tab="verify"]').click();
        });
        function genCode() { return String(Math.floor(100000 + Math.random() * 900000)); }

        // Kod gönder (demo)
        resendBtn.addEventListener('click', () => {
            const email = (verEmail.value || '').trim();
            const users = LS.get('users', []);
            const u = users.find(x => x.email === email);
            if (!u) { verError.textContent = 'Bu e‑posta ile kullanıcı yok'; verError.style.display = 'block'; return; }
            u.verifyCode = genCode(); LS.set('users', users);
            verHint.textContent = 'Kod: ' + u.verifyCode + ' (demo).';
            showToast('Kod gönderildi (demo).', 'success');
        });

        // Doğrula
        verifyBtn.addEventListener('click', () => {
            verError.style.display = 'none'; verError.textContent = '';
            const email = (verEmail.value || '').trim();
            const code = (verCode.value || '').trim();
            const users = LS.get('users', []);
            const u = users.find(x => x.email === email);
            if (!u) { return showErr(verError, 'Kullanıcı bulunamadı'); }
            if (u.active) { return showErr(verError, 'Hesap zaten doğrulandı'); }
            if (u.verifyCode !== code) { return showErr(verError, 'Kod hatalı'); }
            u.active = true; u.verifyCode = null; LS.set('users', users);
            verEmail.value = ''; verCode.value = '';
            showToast('E‑posta doğrulandı. Giriş yapabilirsiniz.', 'success');
            document.querySelector('[data-tab="login"]').click();
        });

        // Giriş
        loginBtn.addEventListener('click', () => {
            loginError.style.display = 'none'; loginError.textContent = '';
            if (!canLogin()) return;
            const id = (loginId.value || '').trim();
            const pw = loginPass.value;
            if (!id || !pw) { return showErr(loginError, 'Alanları doldurun'); }
            const users = LS.get('users', []);
            const byEmail = users.find(x => x.email === id);
            const byPhone = users.find(x => x.phone === id);
            const u = byEmail || byPhone;
            if (!u) { recordFail(); return showErr(loginError, 'Kullanıcı bulunamadı'); }
            if (byEmail && !u.active) { recordFail(); return showErr(loginError, 'E‑posta doğrulanmadı'); }
            if (u.pass !== pw) { recordFail(); return showErr(loginError, 'Şifre yanlış'); }
            LS.set('attempts', { count: 0, until: 0 });
            greet.style.display = 'flex';
            setTimeout(() => { greet.style.display = 'none'; openVideos(u); }, 900);
        });

        // Admin: kurulum ve hash'li giriş
        if (!LS.get('adminCred')) {
            setupOverlay.style.display = 'flex';
        }
        setupSave.addEventListener('click', async () => {
            setupError.style.display = 'none'; setupError.textContent = '';
            const name = (setupName.value || '').trim();
            const pass = (setupPass.value || '').trim();
            if (!name || strength(pass) < 3) { return showErr(setupError, 'Ad ve güçlü şifre gerekli'); }
            const salt = crypto.getRandomValues(new Uint8Array(16));
            const userHash = await sha256Hex(salt, name);
            const passHash = await sha256Hex(salt, pass);
            LS.set('adminCred', { salt: Array.from(salt), userHash, passHash });
            setupName.value = ''; setupPass.value = ''; setupOverlay.style.display = 'none';
            showToast('Admin kurulum tamam', 'success');
        });

        adminBtn.addEventListener('click', async () => {
            adminError.style.display = 'none'; adminError.textContent = '';
            const name = (adminName.value || '').trim();
            const pass = (adminPass.value || '').trim();
            const cred = LS.get('adminCred', null);
            if (!cred) { return showErr(adminError, 'Önce admin kurulum yapın'); }
            const salt = new Uint8Array(cred.salt);
            const nameH = await sha256Hex(salt, name);
            const passH = await sha256Hex(salt, pass);
            if (nameH === cred.userHash && passH === cred.passHash) {
                openVideos({ id: 'admin-fixed', role: 'admin', email: 'admin@local', phone: '' });
            } else {
                showErr(adminError, 'Admin adı veya şifre yanlış!');
            }
        });

        async function sha256Hex(saltUint8, text) {
            const enc = new TextEncoder();
            const data = new Uint8Array(saltUint8.length + enc.encode(text).length);
            data.set(saltUint8, 0); data.set(enc.encode(text), saltUint8.length);
            const digest = await crypto.subtle.digest('SHA-256', data);
            const arr = Array.from(new Uint8Array(digest));
            return arr.map(b => b.toString(16).padStart(2, '0')).join('');
        }

        // Video ekleme
        addVideoBtn.addEventListener('click', () => {
            const title = sanitize(videoTitle.value || '');
            const rawUrl = (videoUrl.value || '').trim();
            if (!title || !rawUrl) { return showToast('Başlık ve URL gerekli', 'error'); }
            if (!/^https:\/\//.test(rawUrl)) { return showToast('URL https:// ile başlamalı', 'error'); }

            const parsed = toPlayable(rawUrl);
            const vids = LS.get('videos', []);
            vids.push({ id: crypto.randomUUID(), title, url: parsed.url, kind: parsed.kind, createdAt: Date.now(), note: parsed.note || '' });
            LS.set('videos', vids);
            videoTitle.value = ''; videoUrl.value = '';
            showToast('Video eklendi', 'success');
            renderVideos();
        });

        // URL'yi oynatılabilir formata çevir
        function toPlayable(u) {
            const YT_WATCH = /https:\/\/www\.youtube\.com\/watch\?v=([A-Za-z0-9_\-]{6,})/;
            const YT_SHORT = /https:\/\/youtu\.be\/([A-Za-z0-9_\-]{6,})/;
            const VIMEO = /https:\/\/vimeo\.com\/(\d+)/;
            const DM = /https:\/\/www\.dailymotion\.com\/video\/([A-Za-z0-9]+)/;

            if (YT_WATCH.test(u)) {
                const id = u.match(YT_WATCH)[1];
                return { kind: 'iframe', url: `https://www.youtube.com/embed/${id}` };
            }
            if (YT_SHORT.test(u)) {
                const id = u.match(YT_SHORT)[1];
                return { kind: 'iframe', url: `https://www.youtube.com/embed/${id}` };
            }
            if (VIMEO.test(u)) {
                const id = u.match(VIMEO)[1];
                return { kind: 'iframe', url: `https://player.vimeo.com/video/${id}` };
            }
            if (DM.test(u)) {
                const id = u.match(DM)[1];
                return { kind: 'iframe', url: `https://www.dailymotion.com/embed/video/${id}` };
            }
            if (/\.(mp4)(\?.*)?$/.test(u)) {
                return { kind: 'mp4', url: u };
            }
            // Diğer https URL'yi iframe dene, engel olabilir
            return { kind: 'iframe', url: u, note: 'Bazı siteler iframe’i engeller (X-Frame-Options). Çalışmazsa mp4 veya resmi embed URL kullanın.' };
        }

        // Videoları çiz
        function renderVideos() {
            const vids = LS.get('videos', []);
            videoGrid.innerHTML = '';
            vids.forEach(v => {
                const card = document.createElement('div'); card.className = 'cardV';
                const body = v.kind === 'mp4'
                    ? `<video controls src="${escapeHtml(v.url)}"></video>`
                    : `<iframe src="${escapeHtml(v.url)}" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>`;

                card.innerHTML = `
          <div class="thumb">${body}</div>
          <div class="meta">
            <div>
              <div style="font-weight:700">${escapeHtml(v.title)}</div>
              <div style="font-size:.85rem; color:var(--muted)">${new Date(v.createdAt).toLocaleString()}</div>
              ${v.note ? `<div style="font-size:.8rem; color:#ffd166">${escapeHtml(v.note)}</div>` : ''}
            </div>
            ${isAdminSession() ? `<button class="smallBtn del" data-id="${v.id}">Sil</button>` : ''}
          </div>
        `;
                videoGrid.appendChild(card);
            });

            if (isAdminSession()) {
                document.querySelectorAll('.del').forEach(b => {
                    b.addEventListener('click', () => {
                        const id = b.getAttribute('data-id');
                        const vids = LS.get('videos', []);
                        LS.set('videos', vids.filter(x => x.id !== id));
                        showToast('Video silindi', 'success');
                        renderVideos();
                    });
                });
            }
        }

        // Oturum ve kontroller
        function openVideos(user) {
            auth.style.display = 'none';
            videosView.style.display = 'block';
            userBadge.textContent = (user.role === 'admin' ? 'Admin • ' : 'Kullanıcı • ') + (user.email || user.phone || '—');
            adminPanel.style.display = user.role === 'admin' ? 'block' : 'none';
            LS.set('session', { id: user.id, role: user.role, email: user.email, phone: user.phone });
            renderVideos();
        }
        logoutBtn.addEventListener('click', () => {
            LS.remove('session');
            videosView.style.display = 'none';
            auth.style.display = 'grid';
            showToast('Çıkış yapıldı');
        });
        function isAdminSession() {
            const s = LS.get('session', null);
            return !!s && s.role === 'admin';
        }

        // Seed butonu
        seedBtn.addEventListener('click', () => {
            seedLongVideos();
            showToast('Uzun demo videolar yüklendi', 'success');
            renderVideos();
        });

        // Otomatik oturum
        const session = LS.get('session', null);
        if (session) { openVideos(session); }

        // Particle init already above
    </script>
</body>

</html>
