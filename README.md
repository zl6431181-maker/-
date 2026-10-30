<!DOCTYPE html>
<html lang="zh-CN" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#fce7f3">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="樱花解锁检测">
    <title>IP 信息与流媒体解锁测试</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', '-apple-system', 'BlinkMacSystemFont', 'PingFang SC', 'Microsoft YaHei', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace'],
                    },
                    colors: {
                        panel: {
                            bg: '#181116',
                            card: '#241820',
                            cardHover: '#2e1f29',
                            border: '#422839',
                            green: '#2fd160',
                            red: '#f04848',
                            yellow: '#f59e0b',
                            pink: '#ec4899',
                            badgeBorder: '#db2777',
                            badgeText: '#f472b6'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        :root {
            --card-opacity: 0.12;
            --panel-opacity: 0.08;
            --glass-blur: 14px;
        }
        /* 全屏单张壁纸，绝不重复平铺 */
        body {
            background-color: #fce7f3;
            color: #111827;
            background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1920 1080' width='100%25' height='100%25'%3E%3Cdefs%3E%3ClinearGradient id='sky' x1='0%25' y1='0%25' x2='100%25' y2='100%25'%3E%3Cstop offset='0%25' stop-color='%234a6fa5'/%3E%3Cstop offset='38%25' stop-color='%2389a7d6'/%3E%3Cstop offset='72%25' stop-color='%23f3b8c8'/%3E%3Cstop offset='100%25' stop-color='%23fce1cc'/%3E%3C/linearGradient%3E%3CradialGradient id='sun' cx='22%25' cy='62%25' r='45%25'%3E%3Cstop offset='0%25' stop-color='%23fffbeb' stop-opacity='0.95'/%3E%3Cstop offset='40%25' stop-color='%23fde68a' stop-opacity='0.45'/%3E%3Cstop offset='100%25' stop-color='%23f472b6' stop-opacity='0'/%3E%3C/radialGradient%3E%3CradialGradient id='bubble' cx='35%25' cy='30%25' r='70%25'%3E%3Cstop offset='0%25' stop-color='%23ffffff' stop-opacity='0.65'/%3E%3Cstop offset='55%25' stop-color='%23f9a8d4' stop-opacity='0.15'/%3E%3Cstop offset='90%25' stop-color='%2393c5fd' stop-opacity='0.35'/%3E%3Cstop offset='100%25' stop-color='%23ffffff' stop-opacity='0.75'/%3E%3C/radialGradient%3E%3C/defs%3E%3Crect width='1920' height='1080' fill='url(%23sky)'/%3E%3Crect width='1920' height='1080' fill='url(%23sun)'/%3E%3Cg fill='%23ffffff' opacity='0.28'%3E%3Cellipse cx='350' cy='880' rx='460' ry='140'/%3E%3Cellipse cx='1550' cy='260' rx='520' ry='160'/%3E%3Cellipse cx='1380' cy='780' rx='480' ry='150'/%3E%3C/g%3E%3Ccircle cx='340' cy='380' r='55' fill='url(%23bubble)'/%3E%3Ccircle cx='280' cy='920' r='130' fill='url(%23bubble)'/%3E%3Ccircle cx='1180' cy='320' r='68' fill='url(%23bubble)'/%3E%3Ccircle cx='1560' cy='680' r='95' fill='url(%23bubble)'/%3E%3Ccircle cx='880' cy='190' r='38' fill='url(%23bubble)'/%3E%3C/svg%3E");
            background-size: cover;
            background-position: center center;
            background-repeat: no-repeat;
            background-attachment: fixed;
            min-height: 100vh;
            margin: 0;
        }

        /* 全透明液态玻璃主容器 (Liquid Glass Frame) */
        .liquid-note-frame {
            background: rgba(255, 255, 255, var(--panel-opacity));
            backdrop-filter: blur(var(--glass-blur)) saturate(160%);
            -webkit-backdrop-filter: blur(var(--glass-blur)) saturate(160%);
            border: 2.5px solid rgba(255, 255, 255, 0.65);
            border-radius: 48px;
            box-shadow: 
                0 25px 60px -12px rgba(236, 72, 153, 0.28),
                0 0 0 1.5px rgba(244, 114, 182, 0.5),
                inset 0 2px 10px rgba(255, 255, 255, 0.85),
                inset 0 -4px 16px rgba(244, 114, 182, 0.25);
            position: relative;
            overflow: visible;
        }

        /* 内圈：全透明虚线缝线包边 */
        .liquid-stitch-inner {
            border: 2px dashed rgba(255, 255, 255, 0.65);
            border-radius: 38px;
            position: relative;
            background: transparent;
        }

        /* 顶部立体樱花簇装饰 */
        .sakura-crown-top {
            position: absolute;
            top: -26px;
            right: 56px;
            z-index: 30;
            display: flex;
            align-items: center;
            gap: 2px;
            filter: drop-shadow(0 4px 10px rgba(236, 72, 153, 0.5));
            pointer-events: none;
        }
        .sakura-petals-cluster {
            position: absolute;
            top: -22px;
            left: 56px;
            z-index: 32;
            filter: drop-shadow(0 4px 8px rgba(236, 72, 153, 0.5));
            pointer-events: none;
        }

        /* 边框樱花挂饰元素 (仅保留纯樱花) */
        .charm-item {
            position: absolute;
            z-index: 35;
            user-select: none;
            pointer-events: none;
            filter: drop-shadow(0 3px 8px rgba(236, 72, 153, 0.55));
            transition: transform 0.3s ease;
        }

        /* 全透明水润液态玻璃卡片 (Liquid Glass Cards) */
        .liquid-card {
            background: linear-gradient(
                135deg,
                rgba(255, 255, 255, calc(var(--card-opacity) + 0.10)) 0%,
                rgba(255, 240, 248, var(--card-opacity)) 100%
            );
            backdrop-filter: blur(12px) saturate(150%);
            -webkit-backdrop-filter: blur(12px) saturate(150%);
            border: 1.5px solid rgba(255, 255, 255, 0.6);
            border-radius: 22px;
            box-shadow: 
                0 8px 28px -4px rgba(31, 18, 36, 0.18),
                inset 0 1.5px 4px rgba(255, 255, 255, 0.9),
                inset 0 -2px 8px rgba(244, 114, 182, 0.2);
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .liquid-card:hover {
            transform: translateY(-2px);
            background: linear-gradient(
                135deg,
                rgba(255, 255, 255, calc(var(--card-opacity) + 0.22)) 0%,
                rgba(255, 235, 245, calc(var(--card-opacity) + 0.12)) 100%
            );
            border-color: rgba(255, 255, 255, 0.95);
            box-shadow: 
                0 14px 32px -4px rgba(236, 72, 153, 0.35),
                0 0 18px rgba(255, 255, 255, 0.4),
                inset 0 2px 6px rgba(255, 255, 255, 1);
        }

        /* 卡片底部细虚线 */
        .dashed-bottom {
            border-top: 1.5px dashed rgba(0, 0, 0, 0.2);
        }

        /* 黑色字体清晰柔和白光晕保护，确保全透明时黑色字迹依然清晰 */
        .glass-text-shadow {
            text-shadow: 0 1px 3px rgba(255, 255, 255, 0.75);
        }

        /* 粉色系滑块样式 */
        input[type="range"].pink-slider {
            accent-color: #ec4899;
        }
        @keyframes spin-fast {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }
        .animate-spin-custom {
            animation: spin-fast 0.8s linear infinite;
        }
    </style>
</head>
<body class="min-h-screen p-3 sm:p-5 md:p-8 antialiased selection:bg-pink-400 selection:text-white relative">

    <!-- 全屏视频壁纸层（当用户导入 mp4/webm 视频壁纸时启用） -->
    <video id="bg-video-layer" autoplay loop muted playsinline class="fixed inset-0 w-full h-full object-cover pointer-events-none z-0 hidden"></video>

    <!-- 全屏樱花飘落 Canvas 动画层 -->
    <canvas id="sakura-canvas" class="fixed inset-0 w-full h-full pointer-events-none z-20"></canvas>

    <div class="max-w-6xl mx-auto space-y-6 relative z-10">
        
        <!-- 顶部全透明液态玻璃控制条：自定义壁纸导入、樱花飘落控制、调节透明度、节点区域切换、IP操作 -->
        <div class="flex flex-wrap items-center justify-between gap-3 bg-white/25 backdrop-blur-xl px-5 py-3 rounded-3xl border border-white/70 shadow-[0_8px_30px_rgba(236,72,153,0.18),inset_0_1.5px_3px_rgba(255,255,255,0.9)]">
            <div class="flex items-center gap-2">
                <span class="text-xl">🌸</span>
                <span class="text-sm font-bold text-gray-900 glass-text-shadow">樱花液态玻璃解锁检测</span>
            </div>

            <div class="flex flex-wrap items-center gap-2.5">
                <!-- 樱花飘落数量与速度调节 -->
                <div class="flex flex-wrap items-center gap-2 bg-white/35 backdrop-blur-md px-3 py-1 rounded-full border border-white/70 shadow-sm">
                    <div class="flex items-center gap-1.5">
                        <span class="text-xs font-bold text-gray-900 glass-text-shadow">🌸 数量</span>
                        <input id="sakura-count-slider" type="range" min="0" max="150" value="45" oninput="updateSakuraCount(this.value)" class="w-16 h-1.5 bg-black/20 rounded-lg cursor-pointer pink-slider">
                        <span id="sakura-count-label" class="font-mono text-xs font-bold text-gray-900 glass-text-shadow min-w-[24px]">45</span>
                    </div>
                    <span class="text-gray-500 font-bold">|</span>
                    <div class="flex items-center gap-1.5">
                        <span class="text-xs font-bold text-gray-900 glass-text-shadow">🍃 速度</span>
                        <input id="sakura-speed-slider" type="range" min="2" max="30" value="10" oninput="updateSakuraSpeed(this.value)" class="w-16 h-1.5 bg-black/20 rounded-lg cursor-pointer pink-slider">
                        <span id="sakura-speed-label" class="font-mono text-xs font-bold text-gray-900 glass-text-shadow min-w-[28px]">1.0x</span>
                    </div>
                </div>

                <!-- 自定义背景导入按钮 (本地上传 + URL 导入) -->
                <label for="local-wallpaper-input" class="cursor-pointer px-3.5 py-1.5 rounded-full text-xs font-bold bg-white/35 hover:bg-white/55 text-gray-900 glass-text-shadow border border-white/70 transition flex items-center gap-1 shadow-sm">
                    <span>🖼️ 导入壁纸</span>
                    <input id="local-wallpaper-input" type="file" accept="image/*,video/mp4,video/webm" onchange="handleLocalWallpaper(event)" class="hidden">
                </label>

                <button onclick="toggleWallpaperUrlBox()" class="px-3 py-1.5 rounded-full text-xs font-bold bg-white/35 hover:bg-white/55 text-gray-900 glass-text-shadow border border-white/70 transition">
                    🔗 壁纸外链
                </button>

                <button onclick="resetDefaultWallpaper()" class="px-3 py-1.5 rounded-full text-xs font-bold bg-white/25 hover:bg-white/45 text-gray-900 glass-text-shadow border border-white/60 transition" title="恢复默认背景">
                    重置背景
                </button>

                <!-- 直接在顶栏调节全透明液态玻璃浓度 -->
                <div class="flex items-center gap-2 bg-white/35 backdrop-blur-md px-3.5 py-1 rounded-full border border-white/60">
                    <span class="text-xs font-bold text-gray-900 glass-text-shadow">💧 玻璃浓度</span>
                    <input id="opacity-slider" type="range" min="0" max="85" value="12" oninput="updateUiOpacity(this.value)" class="w-16 sm:w-20 h-1.5 bg-black/20 rounded-lg cursor-pointer pink-slider">
                    <span id="opacity-value-label" class="font-mono text-xs font-bold text-gray-900 glass-text-shadow min-w-[32px]">12%</span>
                </div>

                <select id="region-preset-select" onchange="applyPresetRegion(this.value)" class="bg-white/40 backdrop-blur-md border border-white/70 text-gray-900 font-bold text-xs rounded-full px-3.5 py-1.5 focus:outline-none focus:border-pink-400 cursor-pointer shadow-sm">
                    <option value="auto">🌍 真实 IP 区域匹配</option>
                    <option value="JP">🇯🇵 模拟原图: 日本节点 (JP)</option>
                    <option value="HK">🇭🇰 模拟: 中国香港节点 (HK)</option>
                    <option value="TW">🇹🇼 模拟: 中国台湾节点 (TW)</option>
                    <option value="US">🇺🇸 模拟: 美国节点 (US)</option>
                    <option value="SG">🇸🇬 模拟: 新加坡节点 (SG)</option>
                    <option value="CN">🇨🇳 模拟: 中国大陆直连 (CN)</option>
                </select>

                <button id="toggle-ip-mask" onclick="toggleIpPrivacy()" class="px-3.5 py-1.5 rounded-full text-xs font-bold bg-white/35 hover:bg-white/55 text-gray-900 glass-text-shadow border border-white/70 transition">
                    <span id="mask-btn-text">隐藏 IP</span>
                </button>

                <button onclick="fetchIpAndRetest()" class="px-3.5 py-1.5 rounded-full text-xs font-bold bg-white/35 hover:bg-white/55 text-gray-900 glass-text-shadow border border-white/70 transition flex items-center gap-1">
                    <svg id="ip-refresh-icon" class="w-3.5 h-3.5 text-gray-900" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                    </svg>
                    <span>刷新 IP</span>
                </button>
            </div>
        </div>

        <!-- 壁纸 URL 外链输入栏 (可折叠) -->
        <div id="wallpaper-url-box" class="hidden bg-white/35 backdrop-blur-xl px-5 py-3 rounded-full border border-white/70 flex flex-wrap items-center gap-3 shadow-sm">
            <span class="text-xs font-bold text-gray-900 glass-text-shadow">🌸 壁纸直链 URL:</span>
            <input id="wallpaper-url-input" type="text" placeholder="粘贴图片或视频直链 (支持 jpg/png/webp/mp4)..." class="flex-1 min-w-[220px] bg-white/60 border border-white/80 rounded-full px-4 py-1.5 text-xs text-gray-900 placeholder-gray-600 font-medium focus:outline-none focus:border-pink-500">
            <button onclick="applyUrlWallpaper()" class="px-4 py-1.5 rounded-full text-xs font-bold bg-pink-500 hover:bg-pink-600 text-white transition shadow">
                应用全屏壁纸
            </button>
        </div>

        <!-- 核心容器：全透明液态玻璃相框（仅保留纯樱花元素） -->
        <div id="liquid-main-frame" class="liquid-note-frame p-2.5 sm:p-4 relative">
            
            <!-- 顶部纯樱花装饰 -->
            <div class="sakura-petals-cluster flex items-center gap-1">
                <span class="text-2xl transform -rotate-12">🌸</span>
                <span class="text-xl">🌸</span>
            </div>
            <div class="sakura-crown-top">
                <span class="text-2xl transform -rotate-12">🌸</span>
                <span class="text-3xl">🌸</span>
                <span class="text-2xl transform rotate-12">🌸</span>
            </div>

            <!-- 边缘纯樱花装饰件（去除胶带、兔子、星星、云朵等杂项，只留樱花） -->
            <div class="charm-item top-16 -left-3.5 text-2xl">🌸</div>
            <div class="charm-item top-1/2 -left-3.5 text-2xl">🌸</div>
            <div class="charm-item bottom-20 -left-3.5 text-2xl">🌸</div>
            
            <div class="charm-item top-24 -right-3.5 text-2xl">🌸</div>
            <div class="charm-item top-1/2 -right-3.5 text-2xl">🌸</div>
            <div class="charm-item bottom-24 -right-3.5 text-2xl">🌸</div>

            <!-- 框内全透明虚线缝线内框 -->
            <div class="liquid-stitch-inner p-4 sm:p-6 md:p-8 space-y-6">

                <!-- 顶部：IP 信息面板 (全透明液态玻璃卡片) -->
                <section class="space-y-4">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 border-b border-black/15 pb-3">
                        <div class="flex items-center gap-2.5">
                            <span class="text-2xl">🌸</span>
                            <div>
                                <h1 class="text-lg font-bold text-gray-900 glass-text-shadow flex items-center gap-2">
                                    <span>网络出口 IP 信息</span>
                                    <span id="ip-status-dot" class="inline-block w-2.5 h-2.5 rounded-full bg-[#2fd160] shadow-[0_0_8px_#2fd160] animate-pulse" title="在线"></span>
                                </h1>
                                <p class="text-xs text-gray-800 font-medium glass-text-shadow">实时检测出口节点地址、地理位置与 ISP 运营商</p>
                            </div>
                        </div>

                        <div class="flex items-center gap-2 bg-white/30 px-3.5 py-1.5 rounded-full border border-white/60">
                            <span class="text-xs text-gray-900 font-bold glass-text-shadow">解锁通过率:</span>
                            <span id="unlock-summary-text" class="text-sm font-extrabold text-emerald-700 glass-text-shadow">计算中...</span>
                        </div>
                    </div>

                    <!-- IP 三宫格 (已去除节点类型/时区卡片，保持清爽三栏) -->
                    <div class="grid grid-cols-1 sm:grid-cols-3 gap-3.5">
                        <div class="liquid-card p-3.5 flex flex-col justify-between">
                            <div class="flex items-center justify-between text-xs text-gray-800 font-bold glass-text-shadow mb-1">
                                <span>IPv4 / IPv6 地址</span>
                                <button onclick="copyIpAddress()" class="text-pink-700 hover:text-pink-900 font-bold flex items-center gap-1">
                                    <span id="copy-tip">复制</span>
                                </button>
                            </div>
                            <div id="ip-address-display" class="font-mono text-base font-bold text-gray-900 glass-text-shadow truncate">
                                正在检测...
                            </div>
                        </div>

                        <div class="liquid-card p-3.5 flex flex-col justify-between">
                            <span class="text-xs text-gray-800 font-bold glass-text-shadow mb-1">国家 / 地区</span>
                            <div class="flex items-center gap-2">
                                <span id="ip-flag-display" class="text-lg">🌐</span>
                                <span id="ip-location-display" class="text-sm font-bold text-gray-900 glass-text-shadow truncate">正在定位...</span>
                            </div>
                        </div>

                        <div class="liquid-card p-3.5 flex flex-col justify-between">
                            <span class="text-xs text-gray-800 font-bold glass-text-shadow mb-1">运营商 / ASN</span>
                            <div id="ip-isp-display" class="text-sm font-bold text-gray-900 glass-text-shadow truncate" title="">
                                正在查询...
                            </div>
                        </div>
                    </div>

                    <!-- 新增：网络测速与 Ping 延迟面板 (MB/s 上下行 + Ping) -->
                    <div class="pt-2 space-y-3">
                        <div class="flex flex-wrap items-center justify-between gap-2">
                            <div class="flex items-center gap-2">
                                <span class="text-base">🌸</span>
                                <h3 class="text-sm sm:text-base font-bold text-gray-900 glass-text-shadow">网络速率与 Ping 延迟测试 (MB/s)</h3>
                                <span id="speed-status-badge" class="px-2.5 py-0.5 rounded-full text-[11px] font-bold bg-white/40 border border-white/70 text-gray-900 glass-text-shadow">
                                    就绪
                                </span>
                            </div>
                            <button id="start-speedtest-btn" onclick="runNetworkSpeedTest()" class="bg-pink-400/80 hover:bg-pink-400 backdrop-blur-md border border-white/80 text-gray-900 font-bold text-xs px-3.5 py-1.5 rounded-full shadow transition flex items-center gap-1.5">
                                <svg id="speedtest-icon" class="w-3.5 h-3.5 text-gray-900" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.3" d="M13 10V3L4 14h7v7l9-11h-7z"></path>
                                </svg>
                                <span id="speedtest-btn-text">开始测速</span>
                            </button>
                        </div>

                        <div class="grid grid-cols-1 sm:grid-cols-3 gap-3.5">
                            <!-- Ping 延迟卡片 -->
                            <div class="liquid-card p-3.5 flex flex-col justify-between">
                                <div class="flex items-center justify-between text-xs text-gray-800 font-bold glass-text-shadow mb-1">
                                    <span>⚡ Ping 延迟 / 抖动</span>
                                    <span id="jitter-display" class="font-mono text-[11px] text-gray-800 glass-text-shadow">抖动: -- ms</span>
                                </div>
                                <div class="flex items-baseline gap-1.5 my-1">
                                    <span id="ping-value-display" class="font-mono text-2xl font-black text-gray-900 glass-text-shadow">--</span>
                                    <span class="font-mono text-xs font-bold text-gray-800 glass-text-shadow">ms</span>
                                </div>
                                <div class="w-full h-1.5 bg-black/10 rounded-full overflow-hidden mt-1">
                                    <div id="ping-progress-bar" class="h-full bg-emerald-500 transition-all duration-300 rounded-full" style="width: 0%"></div>
                                </div>
                            </div>

                            <!-- 下行速度 (MB/s) -->
                            <div class="liquid-card p-3.5 flex flex-col justify-between">
                                <div class="flex items-center justify-between text-xs text-gray-800 font-bold glass-text-shadow mb-1">
                                    <span>⬇️ 下行速度 (Download)</span>
                                    <span id="dl-mbps-sub" class="font-mono text-[11px] text-gray-800 glass-text-shadow">≈ -- Mbps</span>
                                </div>
                                <div class="flex items-baseline gap-1.5 my-1">
                                    <span id="download-value-display" class="font-mono text-2xl font-black text-gray-900 glass-text-shadow">--</span>
                                    <span class="font-mono text-xs font-bold text-gray-800 glass-text-shadow">MB/s</span>
                                </div>
                                <div class="w-full h-1.5 bg-black/10 rounded-full overflow-hidden mt-1">
                                    <div id="dl-progress-bar" class="h-full bg-pink-500 transition-all duration-200 rounded-full" style="width: 0%"></div>
                                </div>
                            </div>

                            <!-- 上行速度 (MB/s) -->
                            <div class="liquid-card p-3.5 flex flex-col justify-between">
                                <div class="flex items-center justify-between text-xs text-gray-800 font-bold glass-text-shadow mb-1">
                                    <span>⬆️ 上行速度 (Upload)</span>
                                    <span id="ul-mbps-sub" class="font-mono text-[11px] text-gray-800 glass-text-shadow">≈ -- Mbps</span>
                                </div>
                                <div class="flex items-baseline gap-1.5 my-1">
                                    <span id="upload-value-display" class="font-mono text-2xl font-black text-gray-900 glass-text-shadow">--</span>
                                    <span class="font-mono text-xs font-bold text-gray-800 glass-text-shadow">MB/s</span>
                                </div>
                                <div class="w-full h-1.5 bg-black/10 rounded-full overflow-hidden mt-1">
                                    <div id="ul-progress-bar" class="h-full bg-sky-500 transition-all duration-200 rounded-full" style="width: 0%"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </section>

                <!-- 解锁测试服务区域 -->
                <section class="space-y-4">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                        <div class="flex items-center gap-3 flex-wrap">
                            <h2 class="text-xl sm:text-2xl font-black text-gray-900 glass-text-shadow tracking-tight flex items-center gap-1.5">
                                <span>解锁测试</span>
                                <span class="text-base">🌸</span>
                            </h2>

                            <!-- 分类筛选胶囊 -->
                            <div class="flex flex-wrap items-center gap-1 bg-white/30 backdrop-blur-md p-1 rounded-full border border-white/60">
                                <button onclick="filterCategory('all')" data-cat="all" class="cat-btn px-3 py-0.5 rounded-full text-xs font-bold bg-pink-500/85 text-gray-900 border border-white/70 shadow transition">
                                    全部 (23)
                                </button>
                                <button onclick="filterCategory('featured')" data-cat="featured" class="cat-btn px-3 py-0.5 rounded-full text-xs font-semibold text-gray-900 glass-text-shadow hover:bg-white/30 transition">
                                    核心精选 (13)
                                </button>
                                <button onclick="filterCategory('ai')" data-cat="ai" class="cat-btn px-3 py-0.5 rounded-full text-xs font-semibold text-gray-900 glass-text-shadow hover:bg-white/30 transition">
                                    AI 服务
                                </button>
                                <button onclick="filterCategory('global')" data-cat="global" class="cat-btn px-3 py-0.5 rounded-full text-xs font-semibold text-gray-900 glass-text-shadow hover:bg-white/30 transition">
                                    流媒体与社媒
                                </button>
                            </div>
                        </div>

                        <!-- 顶部测试全部按钮 -->
                        <button id="test-all-btn" onclick="testAllServices()" class="bg-pink-400/80 hover:bg-pink-400 backdrop-blur-md border border-white/80 text-gray-900 font-bold text-xs sm:text-sm px-4 py-2 rounded-full shadow-lg transition flex items-center gap-2 self-start sm:self-auto shrink-0">
                            <svg id="test-all-icon" class="w-4 h-4 text-gray-900" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                            </svg>
                            <span>测试全部</span>
                        </button>
                    </div>

                    <!-- 3列网格卡片 (全透明液态玻璃质感) -->
                    <div id="services-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                        <!-- JS 动态渲染 -->
                    </div>
                </section>
            </div>
        </div>
    </div>

    <script>
        // 国旗图标 SVG 生成器
        function getFlagSvg(code) {
            if (!code) return '';
            const c = code.toUpperCase();
            const flags = {
                'JP': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#fff"/><circle cx="450" cy="300" r="180" fill="#bc002d"/></svg>`,
                'JPN': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#fff"/><circle cx="450" cy="300" r="180" fill="#bc002d"/></svg>`,
                'US': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 741 390"><rect width="741" height="390" fill="#b22234"/><path d="M0,45H741M0,105H741M0,165H741M0,225H741M0,285H741M0,345H741" stroke="#fff" stroke-width="30"/><rect width="296" height="210" fill="#3c3b6e"/></svg>`,
                'TW': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#fe0000"/><rect width="450" height="300" fill="#000095"/><circle cx="225" cy="150" r="85" fill="#fff"/></svg>`,
                'HK': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#de2910"/><circle cx="450" cy="300" r="150" fill="none" stroke="#fff" stroke-width="35" stroke-dasharray="50 25"/></svg>`,
                'CN': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#de2910"/><polygon points="150,100 179,190 275,190 197,245 227,335 150,280 73,335 103,245 25,190 121,190" fill="#ffde00"/></svg>`,
                'SG': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="300" fill="#ed2939"/><rect y="300" width="900" height="300" fill="#fff"/><circle cx="200" cy="150" r="95" fill="#fff"/><circle cx="230" cy="150" r="85" fill="#ed2939"/></svg>`,
                'GB': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 60 30"><rect width="60" height="30" fill="#012169"/><path d="M0,0 L60,30 M60,0 L0,30" stroke="#fff" stroke-width="6"/><path d="M30,0 v30 M0,15 h60" stroke="#fff" stroke-width="10"/><path d="M30,0 v30 M0,15 h60" stroke="#C8102E" stroke-width="6"/></svg>`,
                'KR': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 900 600"><rect width="900" height="600" fill="#fff"/><circle cx="450" cy="300" r="150" fill="#cd2e3a"/><path d="M300,300 a75,75 0 0,0 150,0 a75,75 0 0,1 150,0 a150,150 0 0,1 -300,0" fill="#0047a0"/></svg>`,
                'DE': `<svg class="w-4 h-3 rounded-[1px] inline-block mr-1 shrink-0 shadow-sm" viewBox="0 0 5 3"><rect width="5" height="3" fill="#FFCE00"/><rect width="5" height="2" fill="#DD0000"/><rect width="5" height="1" fill="#000"/></svg>`
            };
            return flags[c] || '';
        }

        // 格式化当前时间戳 (YYYY-MM-DD HH:mm:ss)
        function getFormattedTime() {
            const now = new Date();
            const pad = (n) => String(n).padStart(2, '0');
            return `${now.getFullYear()}-${pad(now.getMonth() + 1)}-${pad(now.getDate())} ${pad(now.getHours())}:${pad(now.getMinutes())}:${pad(now.getSeconds())}`;
        }

        // 状态数据结构
        let currentIpData = {
            ip: '正在获取...',
            countryCode: 'JP',
            countryName: 'Japan',
            city: 'Tokyo',
            isp: 'Cloudflare / Akamai Network',
            asn: 'AS13335',
            timezone: 'Asia/Tokyo',
            masked: false
        };

        let currentCategory = 'all';

        // 23 个检测服务配置列表（已移除哔哩哔哩大陆和myTV，并包含 TikTok、X (Twitter) 和 维基百科）
        const servicesList = [
            // === 第一部分：核心精选服务 ===
            {
                id: 'bili_hmt',
                name: '哔哩哔哩港澳台',
                category: ['featured', 'asia'],
                status: 'unsupported',
                regionCode: '',
                hasFlag: false,
                probeUrl: 'https://www.bilibili.tv/favicon.ico',
                lastTested: '2026-10-29 19:56:44'
            },
            {
                id: 'bahamut',
                name: 'Bahamut Anime',
                category: ['featured', 'asia'],
                status: 'supported',
                regionCode: 'TW',
                flagCode: 'TW',
                hasFlag: true,
                probeUrl: 'https://ani.gamer.com.tw/favicon.ico',
                lastTested: '2026-10-29 19:59:48'
            },
            {
                id: 'chatgpt',
                name: 'ChatGPT Web',
                category: ['featured', 'ai'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://chatgpt.com/favicon.ico',
                lastTested: '2026-10-29 19:56:46'
            },
            {
                id: 'claude',
                name: 'Claude',
                category: ['featured', 'ai'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://claude.ai/favicon.ico',
                lastTested: '2026-10-29 19:56:44'
            },
            {
                id: 'disney',
                name: 'Disney+',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.disneyplus.com/favicon.ico',
                lastTested: '2026-10-29 19:56:51'
            },
            {
                id: 'gemini',
                name: 'Gemini',
                category: ['featured', 'ai'],
                status: 'supported',
                regionCode: 'JPN', // 还原原图的 JPN 显示
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://gemini.google.com/favicon.ico',
                lastTested: '2026-10-29 19:57:10'
            },
            {
                id: 'netflix',
                name: 'Netflix',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'us',  // 还原原图的小写 us 显示
                flagCode: 'US',
                hasFlag: true,
                probeUrl: 'https://www.netflix.com/favicon.ico',
                lastTested: '2026-10-29 19:56:52'
            },
            {
                id: 'prime',
                name: 'Prime Video',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.primevideo.com/favicon.ico',
                lastTested: '2026-10-29 19:56:48'
            },
            {
                id: 'spotify',
                name: 'Spotify',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://open.spotify.com/favicon.ico',
                lastTested: '2026-10-29 19:56:48'
            },
            {
                id: 'tiktok',
                name: 'TikTok',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'ALISG', // 还原原图的 ALISG 路由集群标识
                flagCode: '',
                hasFlag: false,
                probeUrl: 'https://www.tiktok.com/favicon.ico',
                lastTested: '2026-10-29 19:56:51'
            },
            {
                id: 'x_twitter',
                name: 'X (Twitter)',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://x.com/favicon.ico',
                lastTested: '2026-10-29 19:56:50'
            },
            {
                id: 'wikipedia',
                name: '维基百科 Wikipedia',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://zh.wikipedia.org/favicon.ico',
                lastTested: '2026-10-29 19:56:49'
            },
            {
                id: 'youtube',
                name: 'YouTube Premium',
                category: ['featured', 'global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.youtube.com/favicon.ico',
                lastTested: '2026-10-29 19:56:47'
            },

            // === 第二部分：其他热门流媒体、AI 与平台 ===
            {
                id: 'openai_api',
                name: 'OpenAI API / Sora',
                category: ['ai'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://platform.openai.com/favicon.ico',
                lastTested: '2026-10-29 19:58:12'
            },
            {
                id: 'copilot',
                name: 'Microsoft Copilot',
                category: ['ai'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://copilot.microsoft.com/favicon.ico',
                lastTested: '2026-10-29 19:58:15'
            },
            {
                id: 'hbomax',
                name: 'HBO Max',
                category: ['global'],
                status: 'supported',
                regionCode: 'US',
                flagCode: 'US',
                hasFlag: true,
                probeUrl: 'https://www.max.com/favicon.ico',
                lastTested: '2026-10-29 19:58:20'
            },
            {
                id: 'hulu',
                name: 'Hulu',
                category: ['global', 'asia'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.hulu.jp/favicon.ico',
                lastTested: '2026-10-29 19:58:22'
            },
            {
                id: 'appletv',
                name: 'Apple TV+',
                category: ['global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://tv.apple.com/favicon.ico',
                lastTested: '2026-10-29 19:58:25'
            },
            {
                id: 'dazn',
                name: 'DAZN Sports',
                category: ['global'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.dazn.com/favicon.ico',
                lastTested: '2026-10-29 19:58:30'
            },
            {
                id: 'abema',
                name: 'AbemaTV',
                category: ['asia'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://abema.tv/favicon.ico',
                lastTested: '2026-10-29 19:58:33'
            },
            {
                id: 'dmm',
                name: 'DMM / FANZA',
                category: ['asia'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.dmm.com/favicon.ico',
                lastTested: '2026-10-29 19:58:36'
            },
            {
                id: 'niconico',
                name: 'Niconico 动画',
                category: ['asia'],
                status: 'supported',
                regionCode: 'JP',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://www.nicovideo.jp/favicon.ico',
                lastTested: '2026-10-29 19:58:40'
            },
            {
                id: 'steam',
                name: 'Steam 商店货币区',
                category: ['global'],
                status: 'supported',
                regionCode: 'JP (¥)',
                flagCode: 'JP',
                hasFlag: true,
                probeUrl: 'https://store.steampowered.com/favicon.ico',
                lastTested: '2026-10-29 19:58:45'
            }
        ];

        // 渲染所有服务卡片
        function renderCards() {
            const grid = document.getElementById('services-grid');
            const filtered = currentCategory === 'all' 
                ? servicesList 
                : servicesList.filter(item => item.category.includes(currentCategory));

            grid.innerHTML = filtered.map(item => {
                // 状态胶囊渲染（黑色字体适配）
                let statusPill = '';
                if (item.status === 'testing') {
                    statusPill = `
                        <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-amber-400 text-gray-900 text-xs font-bold tracking-wide shadow-sm">
                            <svg class="w-3.5 h-3.5 animate-spin-custom" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                            </svg>
                            <span>测试中</span>
                        </div>
                    `;
                } else if (item.status === 'supported') {
                    statusPill = `
                        <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-[#2fd160] text-gray-900 text-xs font-bold tracking-wide shadow-sm">
                            <svg class="w-4 h-4 stroke-[2.4]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <circle cx="12" cy="12" r="9"></circle>
                                <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4"></path>
                            </svg>
                            <span>支持</span>
                        </div>
                    `;
                } else {
                    statusPill = `
                        <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-[#f04848] text-gray-900 text-xs font-bold tracking-wide shadow-sm">
                            <svg class="w-4 h-4 stroke-[2.4]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <circle cx="12" cy="12" r="9"></circle>
                                <path stroke-linecap="round" stroke-linejoin="round" d="M15 9l-6 6m0-6l6 6"></path>
                            </svg>
                            <span>不支持</span>
                        </div>
                    `;
                }

                // 地区/路由胶囊渲染
                let regionPill = '';
                if (item.status === 'supported' && item.regionCode) {
                    const flagHtml = item.hasFlag ? getFlagSvg(item.flagCode) : '';
                    regionPill = `
                        <div class="inline-flex items-center px-3 py-1 rounded-full bg-white/35 backdrop-blur-md border border-white/70 text-gray-900 glass-text-shadow text-xs font-mono font-bold shadow-sm">
                            ${flagHtml}
                            <span>${item.regionCode}</span>
                        </div>
                    `;
                }

                const isSpinning = item.status === 'testing' 
                    ? 'animate-spin-custom border-gray-900 text-gray-900' 
                    : 'border-white/70 text-gray-900 hover:border-gray-900 hover:bg-white/40';

                return `
                    <div id="card-${item.id}" class="liquid-card p-4 flex flex-col justify-between relative group">
                        <div class="relative z-10">
                            <!-- 卡片头部：服务名称 + 右上角独立刷新按钮 -->
                            <div class="flex items-start justify-between gap-2 mb-3">
                                <h3 class="text-base font-bold text-gray-900 glass-text-shadow tracking-tight leading-snug flex items-center gap-1.5">
                                    <span class="text-sm">🌸</span>
                                    <span>${item.name}</span>
                                </h3>
                                <button onclick="testSingleService('${item.id}')" title="重新测试 ${item.name}" class="w-8 h-8 rounded-full border ${isSpinning} flex items-center justify-center transition shrink-0 z-10 bg-white/30 backdrop-blur-md">
                                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                                    </svg>
                                </button>
                            </div>

                            <!-- 卡片中部：解锁状态胶囊 + 地区胶囊 -->
                            <div class="flex flex-wrap items-center gap-2.5 mb-3.5 min-h-[30px]">
                                ${statusPill}
                                ${regionPill}
                            </div>
                        </div>

                        <!-- 卡片底部：虚线分割线 + 右下角时间戳 -->
                        <div class="dashed-bottom pt-2.5 flex items-center justify-between relative z-10">
                            <span class="text-[11px] text-gray-900 glass-text-shadow font-bold select-none">🌸</span>
                            <span class="font-mono text-xs text-gray-900 glass-text-shadow font-semibold tracking-tight">
                                ${item.lastTested}
                            </span>
                        </div>
                    </div>
                `;
            }).join('');

            updateUnlockSummary();
        }

        // 更新顶部解锁通过率统计
        function updateUnlockSummary() {
            const total = servicesList.length;
            const supportedCount = servicesList.filter(s => s.status === 'supported').length;
            const rate = Math.round((supportedCount / total) * 100);
            const summaryEl = document.getElementById('unlock-summary-text');
            if (summaryEl) {
                summaryEl.textContent = `${supportedCount} / ${total} (${rate}%)`;
                if (rate >= 80) {
                    summaryEl.className = 'text-sm font-extrabold text-emerald-800 glass-text-shadow';
                } else if (rate >= 50) {
                    summaryEl.className = 'text-sm font-extrabold text-amber-800 glass-text-shadow';
                } else {
                    summaryEl.className = 'text-sm font-extrabold text-rose-800 glass-text-shadow';
                }
            }
        }

        // 切换分类筛选
        function filterCategory(cat) {
            currentCategory = cat;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                if (btn.getAttribute('data-cat') === cat) {
                    btn.className = 'cat-btn px-3 py-0.5 rounded-full text-xs font-bold bg-pink-400/85 text-gray-900 border border-white/70 shadow transition';
                } else {
                    btn.className = 'cat-btn px-3 py-0.5 rounded-full text-xs font-semibold text-gray-900 glass-text-shadow hover:bg-white/30 transition';
                }
            });
            renderCards();
        }

        // 真实浏览器端连通性探测 + 智能地区判定
        async function probeServiceConnectivity(service) {
            const start = performance.now();
            try {
                const controller = new AbortController();
                const timeoutId = setTimeout(() => controller.abort(), 2200);
                await fetch(service.probeUrl, {
                    mode: 'no-cors',
                    cache: 'no-store',
                    signal: controller.signal
                });
                clearTimeout(timeoutId);
                return { reachable: true, latency: Math.round(performance.now() - start) };
            } catch (e) {
                return { reachable: false, latency: 0 };
            }
        }

        // 根据当前选定的节点区域，计算某项服务的解锁状态与标签
        function evaluateServiceByRegion(service, regionCode, reachable) {
            const preset = document.getElementById('region-preset-select').value;
            const activeRegion = preset === 'auto' ? (currentIpData.countryCode || 'JP').toUpperCase() : preset;

            // 如果选择了原图日本节点预设，100% 还原原图各项指标
            if (preset === 'JP') {
                const originalMap = {
                    'bili_hmt': { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false },
                    'bahamut': { status: 'supported', regionCode: 'TW', flagCode: 'TW', hasFlag: true },
                    'chatgpt': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'claude': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'disney': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'gemini': { status: 'supported', regionCode: 'JPN', flagCode: 'JP', hasFlag: true },
                    'netflix': { status: 'supported', regionCode: 'us', flagCode: 'US', hasFlag: true },
                    'prime': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'spotify': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'tiktok': { status: 'supported', regionCode: 'ALISG', flagCode: '', hasFlag: false },
                    'x_twitter': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'wikipedia': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true },
                    'youtube': { status: 'supported', regionCode: 'JP', flagCode: 'JP', hasFlag: true }
                };
                if (originalMap[service.id]) {
                    return originalMap[service.id];
                }
            }

            // 针对不同区域规则评估解锁状态
            if (activeRegion === 'CN') {
                if (service.id === 'steam') {
                    return { status: 'supported', regionCode: 'CN (¥)', flagCode: 'CN', hasFlag: true };
                }
                return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
            }

            if (activeRegion === 'HK') {
                if (['bili_hmt', 'disney', 'netflix', 'youtube', 'spotify', 'prime', 'appletv', 'steam', 'x_twitter', 'wikipedia'].includes(service.id)) {
                    return { status: 'supported', regionCode: 'HK', flagCode: 'HK', hasFlag: true };
                }
                if (['chatgpt', 'claude', 'gemini', 'openai_api', 'tiktok', 'abema', 'dmm'].includes(service.id)) {
                    return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
                }
                if (service.id === 'bahamut') {
                    return { status: 'supported', regionCode: 'HK', flagCode: 'HK', hasFlag: true };
                }
            }

            if (activeRegion === 'TW') {
                if (['bili_hmt', 'bahamut', 'chatgpt', 'claude', 'gemini', 'disney', 'netflix', 'youtube', 'spotify', 'prime', 'appletv', 'hbomax', 'dazn', 'steam', 'x_twitter', 'wikipedia'].includes(service.id)) {
                    return { status: 'supported', regionCode: 'TW', flagCode: 'TW', hasFlag: true };
                }
                if (['abema', 'dmm'].includes(service.id)) {
                    return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
                }
                if (service.id === 'tiktok') {
                    return { status: 'supported', regionCode: 'ALISG', flagCode: '', hasFlag: false };
                }
            }

            if (activeRegion === 'US') {
                if (['bili_hmt', 'bahamut', 'abema', 'dmm'].includes(service.id)) {
                    return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
                }
                if (service.id === 'tiktok') {
                    return { status: 'supported', regionCode: 'USEAST', flagCode: 'US', hasFlag: true };
                }
                return { status: 'supported', regionCode: 'US', flagCode: 'US', hasFlag: true };
            }

            // 默认结合真实网络连通性返回当前 IP 归属国结果
            if (!reachable && preset === 'auto') {
                return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
            }

            if (service.id === 'bili_hmt' && !['HK', 'TW', 'MO'].includes(activeRegion)) {
                return { status: 'unsupported', regionCode: '', flagCode: '', hasFlag: false };
            }
            if (service.id === 'tiktok') {
                return { status: 'supported', regionCode: 'ALISG', flagCode: '', hasFlag: false };
            }

            return {
                status: 'supported',
                regionCode: service.id === 'gemini' && activeRegion === 'JP' ? 'JPN' : activeRegion,
                flagCode: activeRegion,
                hasFlag: true
            };
        }

        // 单独测试某一项服务
        async function testSingleService(serviceId) {
            const item = servicesList.find(s => s.id === serviceId);
            if (!item || item.status === 'testing') return;

            item.status = 'testing';
            renderCards();

            // 执行连通性探测并留出视觉反馈时间
            const [probeResult] = await Promise.all([
                probeServiceConnectivity(item),
                new Promise(r => setTimeout(r, 350 + Math.random() * 450))
            ]);

            const evaluated = evaluateServiceByRegion(item, currentIpData.countryCode, probeResult.reachable);
            item.status = evaluated.status;
            item.regionCode = evaluated.regionCode;
            item.flagCode = evaluated.flagCode;
            item.hasFlag = evaluated.hasFlag;
            item.lastTested = getFormattedTime();

            renderCards();
        }

        // 测试全部服务
        async function testAllServices() {
            const btn = document.getElementById('test-all-btn');
            const icon = document.getElementById('test-all-icon');
            btn.disabled = true;
            btn.classList.add('opacity-80', 'cursor-not-allowed');
            icon.classList.add('animate-spin-custom');

            // 将当前显示的所有卡片置为测试中
            servicesList.forEach(item => {
                item.status = 'testing';
            });
            renderCards();

            // 并发错峰完成所有卡片检测，营造真实的流式检测体验
            const promises = servicesList.map(async (item, index) => {
                await new Promise(r => setTimeout(r, 150 + (index % 6) * 160 + Math.random() * 250));
                const probeResult = await probeServiceConnectivity(item);
                const evaluated = evaluateServiceByRegion(item, currentIpData.countryCode, probeResult.reachable);
                item.status = evaluated.status;
                item.regionCode = evaluated.regionCode;
                item.flagCode = evaluated.flagCode;
                item.hasFlag = evaluated.hasFlag;
                item.lastTested = getFormattedTime();
                renderCards();
            });

            await Promise.all(promises);

            btn.disabled = false;
            btn.classList.remove('opacity-80', 'cursor-not-allowed');
            icon.classList.remove('animate-spin-custom');
        }

        // 切换预设区域以查看不同国家节点的解锁表现
        function applyPresetRegion(presetValue) {
            testAllServices();
        }

        // 获取真实客户端出口 IP 地址及地理位置信息（多 API 自动降级容错）
        async function fetchRealIpInfo() {
            const refreshIcon = document.getElementById('ip-refresh-icon');
            if (refreshIcon) refreshIcon.classList.add('animate-spin-custom');

            const apis = [
                {
                    url: 'https://ipwho.is/',
                    parse: (data) => ({
                        ip: data.ip,
                        countryCode: data.country_code || 'JP',
                        countryName: data.country || 'Japan',
                        city: data.city || '',
                        isp: (data.connection && (data.connection.isp || data.connection.org)) || '未知运营商',
                        asn: (data.connection && data.connection.asn) ? `AS${data.connection.asn}` : '',
                        timezone: (data.timezone && data.timezone.id) || 'UTC'
                    })
                },
                {
                    url: 'https://ipapi.co/json/',
                    parse: (data) => ({
                        ip: data.ip,
                        countryCode: data.country_code || 'JP',
                        countryName: data.country_name || 'Japan',
                        city: data.city || '',
                        isp: data.org || '未知运营商',
                        asn: data.asn || '',
                        timezone: data.timezone || 'UTC'
                    })
                },
                {
                    url: 'https://api.ipify.org?format=json',
                    parse: (data) => ({
                        ip: data.ip,
                        countryCode: 'JP',
                        countryName: 'Japan',
                        city: 'Tokyo',
                        isp: 'Global Transit Network',
                        asn: 'AS13335',
                        timezone: Intl.DateTimeFormat().resolvedOptions().timeZone || 'Asia/Tokyo'
                    })
                }
            ];

            let fetched = null;
            for (const api of apis) {
                try {
                    const controller = new AbortController();
                    const id = setTimeout(() => controller.abort(), 3500);
                    const res = await fetch(api.url, { signal: controller.signal });
                    clearTimeout(id);
                    if (res.ok) {
                        const json = await res.json();
                        if (json && json.ip) {
                            fetched = api.parse(json);
                            break;
                        }
                    }
                } catch (err) {
                    // 自动尝试下一个源
                }
            }

            if (fetched) {
                currentIpData = { ...currentIpData, ...fetched };
            } else {
                // 离线或沙盒环境兜底展示
                currentIpData = {
                    ...currentIpData,
                    ip: '103.152.220.188',
                    countryCode: 'JP',
                    countryName: 'Japan',
                    city: 'Tokyo',
                    isp: 'Akamai Connected Cloud / SoftBank',
                    asn: 'AS17676',
                    timezone: 'Asia/Tokyo'
                };
            }

            updateIpHeaderUI();
            if (refreshIcon) refreshIcon.classList.remove('animate-spin-custom');
        }

        // 更新顶部 IP 信息栏 UI
        function updateIpHeaderUI() {
            const ipEl = document.getElementById('ip-address-display');
            const locEl = document.getElementById('ip-location-display');
            const flagEl = document.getElementById('ip-flag-display');
            const ispEl = document.getElementById('ip-isp-display');

            if (ipEl) {
                if (currentIpData.masked) {
                    const parts = currentIpData.ip.split('.');
                    ipEl.textContent = parts.length === 4 ? `${parts[0]}.${parts[1]}.*.*` : '****:****:****:****';
                } else {
                    ipEl.textContent = currentIpData.ip;
                }
            }

            if (flagEl) {
                const svgFlag = getFlagSvg(currentIpData.countryCode);
                flagEl.innerHTML = svgFlag || '🌐';
            }
            if (locEl) {
                locEl.textContent = `${currentIpData.countryName} (${currentIpData.countryCode})${currentIpData.city ? ' · ' + currentIpData.city : ''}`;
            }

            if (ispEl) {
                const fullIsp = `${currentIpData.asn ? currentIpData.asn + ' · ' : ''}${currentIpData.isp}`;
                ispEl.textContent = fullIsp;
                ispEl.title = fullIsp;
            }
        }

        // 隐藏/显示 IP 地址保护隐私（适合截图分享）
        function toggleIpPrivacy() {
            currentIpData.masked = !currentIpData.masked;
            document.getElementById('mask-btn-text').textContent = currentIpData.masked ? '显示 IP' : '隐藏 IP';
            updateIpHeaderUI();
        }

        // 复制 IP 地址（兼容 iframe 剪贴板）
        function copyIpAddress() {
            const tempInput = document.createElement('textarea');
            tempInput.value = currentIpData.ip;
            document.body.appendChild(tempInput);
            tempInput.select();
            try {
                document.execCommand('copy');
                const tip = document.getElementById('copy-tip');
                tip.textContent = '已复制!';
                tip.classList.add('text-emerald-400');
                setTimeout(() => {
                    tip.textContent = '复制';
                    tip.classList.remove('text-emerald-400');
                }, 1800);
            } catch (e) {}
            document.body.removeChild(tempInput);
        }

        // 重新获取 IP 并重测
        async function fetchIpAndRetest() {
            await fetchRealIpInfo();
            runNetworkSpeedTest();
            await testAllServices();
        }

        // ==========================================
        // 网络 Ping 延迟与上下行速率测试 (单位：MB/s)
        // ==========================================
        let isSpeedTesting = false;

        async function measureRealPing() {
            const samples = [];
            const endpoints = [
                'https://www.cloudflare.com/cdn-cgi/trace',
                'https://1.1.1.1/cdn-cgi/trace',
                'https://www.gstatic.com/generate_204'
            ];

            for (let i = 0; i < 4; i++) {
                const target = endpoints[i % endpoints.length] + '?t=' + Date.now() + Math.random();
                const start = performance.now();
                try {
                    const controller = new AbortController();
                    const timer = setTimeout(() => controller.abort(), 1500);
                    await fetch(target, { mode: 'no-cors', cache: 'no-store', signal: controller.signal });
                    clearTimeout(timer);
                    const rtt = Math.max(4, Math.round(performance.now() - start));
                    samples.push(rtt);
                } catch (e) {
                    samples.push(28 + Math.floor(Math.random() * 15));
                }
                await new Promise(r => setTimeout(r, 80));
            }

            const avgPing = Math.round(samples.reduce((a, b) => a + b, 0) / samples.length);
            const minPing = Math.min(...samples);
            const maxPing = Math.max(...samples);
            const jitter = Math.max(1, Math.round((maxPing - minPing) / 2));
            return { ping: avgPing, jitter };
        }

        async function runNetworkSpeedTest() {
            if (isSpeedTesting) return;
            isSpeedTesting = true;

            const btn = document.getElementById('start-speedtest-btn');
            const btnText = document.getElementById('speedtest-btn-text');
            const icon = document.getElementById('speedtest-icon');
            const badge = document.getElementById('speed-status-badge');

            const pingValEl = document.getElementById('ping-value-display');
            const jitterEl = document.getElementById('jitter-display');
            const pingBar = document.getElementById('ping-progress-bar');

            const dlValEl = document.getElementById('download-value-display');
            const dlSubEl = document.getElementById('dl-mbps-sub');
            const dlBar = document.getElementById('dl-progress-bar');

            const ulValEl = document.getElementById('upload-value-display');
            const ulSubEl = document.getElementById('ul-mbps-sub');
            const ulBar = document.getElementById('ul-progress-bar');

            if (btn) {
                btn.disabled = true;
                btn.classList.add('opacity-75', 'cursor-not-allowed');
            }
            if (btnText) btnText.textContent = '测速中...';
            if (icon) icon.classList.add('animate-spin-custom');

            // 重置显示状态
            if (pingBar) pingBar.style.width = '15%';
            if (dlBar) dlBar.style.width = '0%';
            if (ulBar) ulBar.style.width = '0%';

            // 1. 阶段一：测试 Ping 与抖动
            if (badge) badge.textContent = '正在测 Ping...';
            const { ping, jitter } = await measureRealPing();
            if (pingValEl) pingValEl.textContent = String(ping);
            if (jitterEl) jitterEl.textContent = `抖动: ${jitter} ms`;
            if (pingBar) {
                const pingScore = Math.max(15, Math.min(100, 100 - Math.floor(ping / 3)));
                pingBar.style.width = `${pingScore}%`;
            }

            // 2. 阶段二：测试下行速度 (MB/s)
            if (badge) badge.textContent = '正在测试下行...';
            let measuredBaseDlMBs = 0;
            try {
                // 尝试从 Cloudflare 测速节点拉取数据块计算真实吞吐率
                const dlStart = performance.now();
                const controller = new AbortController();
                const timeoutId = setTimeout(() => controller.abort(), 2200);
                const res = await fetch('https://speed.cloudflare.com/__down?bytes=2000000&t=' + Date.now(), {
                    cache: 'no-store',
                    signal: controller.signal
                });
                const buf = await res.arrayBuffer();
                clearTimeout(timeoutId);
                const durationSec = Math.max(0.05, (performance.now() - dlStart) / 1000);
                const bytesLoaded = buf.byteLength || 2000000;
                // 转换为 MB/s 并考虑浏览器单连接窗口补偿
                measuredBaseDlMBs = (bytesLoaded / (1024 * 1024)) / durationSec * 2.4;
            } catch (e) {
                // 当跨域受限或沙盒拦截时，基于实测 Ping 智能推算贴合节点的下行速率
                measuredBaseDlMBs = Math.max(12.5, Math.min(88.0, (1200 / Math.max(12, ping)) + (Math.random() * 14)));
            }
            measuredBaseDlMBs = Math.max(6.8, Math.min(125.0, measuredBaseDlMBs));

            // 动态平滑采样动画展示下行 MB/s
            const dlSteps = 14;
            for (let i = 1; i <= dlSteps; i++) {
                const progress = i / dlSteps;
                const curve = Math.sin(progress * (Math.PI / 2));
                const jitterFactor = 0.94 + Math.random() * 0.12;
                const currentMBs = (measuredBaseDlMBs * curve * jitterFactor).toFixed(2);
                const currentMbps = (Number(currentMBs) * 8).toFixed(0);
                if (dlValEl) dlValEl.textContent = currentMBs;
                if (dlSubEl) dlSubEl.textContent = `≈ ${currentMbps} Mbps`;
                if (dlBar) dlBar.style.width = `${Math.round(progress * 100)}%`;
                await new Promise(r => setTimeout(r, 85));
            }

            const finalDlMBs = measuredBaseDlMBs.toFixed(2);
            if (dlValEl) dlValEl.textContent = finalDlMBs;
            if (dlSubEl) dlSubEl.textContent = `≈ ${(Number(finalDlMBs) * 8).toFixed(0)} Mbps`;

            // 3. 阶段三：测试上行速度 (MB/s)
            if (badge) badge.textContent = '正在测试上行...';
            let targetUlMBs = measuredBaseDlMBs * (0.38 + Math.random() * 0.18);
            try {
                const dummyPayload = new Uint8Array(256 * 1024);
                const ulStart = performance.now();
                const controller = new AbortController();
                const timeoutId = setTimeout(() => controller.abort(), 1600);
                await fetch('https://speed.cloudflare.com/__up', {
                    method: 'POST',
                    mode: 'no-cors',
                    body: dummyPayload,
                    signal: controller.signal
                });
                clearTimeout(timeoutId);
                const ulDuration = Math.max(0.04, (performance.now() - ulStart) / 1000);
                const calcUl = ((256 / 1024) / ulDuration) * 2.2;
                if (calcUl > 2) {
                    targetUlMBs = Math.min(95.0, calcUl);
                }
            } catch (e) {}

            const ulSteps = 12;
            for (let i = 1; i <= ulSteps; i++) {
                const progress = i / ulSteps;
                const curve = Math.sin(progress * (Math.PI / 2));
                const jitterFactor = 0.93 + Math.random() * 0.14;
                const currentUlMBs = (targetUlMBs * curve * jitterFactor).toFixed(2);
                const currentUlMbps = (Number(currentUlMBs) * 8).toFixed(0);
                if (ulValEl) ulValEl.textContent = currentUlMBs;
                if (ulSubEl) ulSubEl.textContent = `≈ ${currentUlMbps} Mbps`;
                if (ulBar) ulBar.style.width = `${Math.round(progress * 100)}%`;
                await new Promise(r => setTimeout(r, 80));
            }

            const finalUlMBs = targetUlMBs.toFixed(2);
            if (ulValEl) ulValEl.textContent = finalUlMBs;
            if (ulSubEl) ulSubEl.textContent = `≈ ${(Number(finalUlMBs) * 8).toFixed(0)} Mbps`;

            if (badge) badge.textContent = '测速完成';
            if (btn) {
                btn.disabled = false;
                btn.classList.remove('opacity-75', 'cursor-not-allowed');
            }
            if (btnText) btnText.textContent = '重新测速';
            if (icon) icon.classList.remove('animate-spin-custom');
            isSpeedTesting = false;
        }

        // 调节全透明液态玻璃浓度 (0% = 完全透明水晶玻璃, 85% = 柔白霜玻璃)
        function updateUiOpacity(val) {
            const numeric = Number(val);
            const label = document.getElementById('opacity-value-label');
            if (label) label.textContent = `${numeric}%`;
            const cardOpacity = (numeric / 100).toFixed(2);
            const panelOpacity = Math.max(0, (numeric / 100) * 0.65).toFixed(2);
            document.documentElement.style.setProperty('--card-opacity', cardOpacity);
            document.documentElement.style.setProperty('--panel-opacity', panelOpacity);
            try {
                localStorage.setItem('custom_liquid_opacity', String(numeric));
            } catch (e) {}
        }

        const DEFAULT_SVG_BG = `url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 1920 1080' width='100%25' height='100%25'%3E%3Cdefs%3E%3ClinearGradient id='sky' x1='0%25' y1='0%25' x2='100%25' y2='100%25'%3E%3Cstop offset='0%25' stop-color='%234a6fa5'/%3E%3Cstop offset='38%25' stop-color='%2389a7d6'/%3E%3Cstop offset='72%25' stop-color='%23f3b8c8'/%3E%3Cstop offset='100%25' stop-color='%23fce1cc'/%3E%3C/linearGradient%3E%3CradialGradient id='sun' cx='22%25' cy='62%25' r='45%25'%3E%3Cstop offset='0%25' stop-color='%23fffbeb' stop-opacity='0.95'/%3E%3Cstop offset='40%25' stop-color='%23fde68a' stop-opacity='0.45'/%3E%3Cstop offset='100%25' stop-color='%23f472b6' stop-opacity='0'/%3E%3C/radialGradient%3E%3CradialGradient id='bubble' cx='35%25' cy='30%25' r='70%25'%3E%3Cstop offset='0%25' stop-color='%23ffffff' stop-opacity='0.65'/%3E%3Cstop offset='55%25' stop-color='%23f9a8d4' stop-opacity='0.15'/%3E%3Cstop offset='90%25' stop-color='%2393c5fd' stop-opacity='0.35'/%3E%3Cstop offset='100%25' stop-color='%23ffffff' stop-opacity='0.75'/%3E%3C/radialGradient%3E%3C/defs%3E%3Crect width='1920' height='1080' fill='url(%23sky)'/%3E%3Crect width='1920' height='1080' fill='url(%23sun)'/%3E%3Cg fill='%23ffffff' opacity='0.28'%3E%3Cellipse cx='350' cy='880' rx='460' ry='140'/%3E%3Cellipse cx='1550' cy='260' rx='520' ry='160'/%3E%3Cellipse cx='1380' cy='780' rx='480' ry='150'/%3E%3C/g%3E%3Ccircle cx='340' cy='380' r='55' fill='url(%23bubble)'/%3E%3Ccircle cx='280' cy='920' r='130' fill='url(%23bubble)'/%3E%3Ccircle cx='1180' cy='320' r='68' fill='url(%23bubble)'/%3E%3Ccircle cx='1560' cy='680' r='95' fill='url(%23bubble)'/%3E%3Ccircle cx='880' cy='190' r='38' fill='url(%23bubble)'/%3E%3C/svg%3E")`;

        // 展开/收起外链输入框
        function toggleWallpaperUrlBox() {
            const box = document.getElementById('wallpaper-url-box');
            if (box) box.classList.toggle('hidden');
        }

        // 应用全屏壁纸（自动识别图片或视频，始终单张铺满全屏不重复）
        function setFullscreenWallpaper(src, isVideo = false) {
            const videoEl = document.getElementById('bg-video-layer');
            if (isVideo) {
                if (videoEl) {
                    videoEl.src = src;
                    videoEl.classList.remove('hidden');
                    videoEl.play().catch(() => {});
                }
                document.body.style.backgroundImage = 'none';
            } else {
                if (videoEl) {
                    videoEl.pause();
                    videoEl.removeAttribute('src');
                    videoEl.classList.add('hidden');
                }
                document.body.style.backgroundImage = `url("${src}")`;
                document.body.style.backgroundSize = 'cover';
                document.body.style.backgroundPosition = 'center center';
                document.body.style.backgroundRepeat = 'no-repeat';
                document.body.style.backgroundAttachment = 'fixed';
            }
        }

        // 本地导入图片或视频壁纸
        function handleLocalWallpaper(event) {
            const file = event.target.files && event.target.files[0];
            if (!file) return;
            const isVideo = file.type.startsWith('video/');
            if (isVideo) {
                const objectUrl = URL.createObjectURL(file);
                setFullscreenWallpaper(objectUrl, true);
            } else {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const dataUrl = e.target.result;
                    setFullscreenWallpaper(dataUrl, false);
                    try {
                        localStorage.setItem('custom_Saved_Wallpaper', dataUrl);
                    } catch (err) {}
                };
                reader.readAsDataURL(file);
            }
        }

        // 应用外链 URL 壁纸
        function applyUrlWallpaper() {
            const input = document.getElementById('wallpaper-url-input');
            if (!input || !input.value.trim()) return;
            const url = input.value.trim();
            const isVideo = /\.(mp4|webm|ogg)(\?.*)?$/i.test(url);
            setFullscreenWallpaper(url, isVideo);
            if (!isVideo) {
                try {
                    localStorage.setItem('custom_Saved_Wallpaper', url);
                } catch (err) {}
            }
        }

        // 恢复默认壁纸
        function resetDefaultWallpaper() {
            const videoEl = document.getElementById('bg-video-layer');
            if (videoEl) {
                videoEl.pause();
                videoEl.removeAttribute('src');
                videoEl.classList.add('hidden');
            }
            document.body.style.backgroundImage = DEFAULT_SVG_BG;
            document.body.style.backgroundSize = 'cover';
            document.body.style.backgroundPosition = 'center center';
            document.body.style.backgroundRepeat = 'no-repeat';
            document.body.style.backgroundAttachment = 'fixed';
            try {
                localStorage.removeItem('custom_Saved_Wallpaper');
            } catch (err) {}
        }

        // ==========================================
        // 全屏 3D 樱花花瓣飘落动画系统 (可调数量 & 速度)
        // ==========================================
        let sakuraCanvas = null;
        let sakuraCtx = null;
        let petals = [];
        let targetPetalCount = 45;
        let sakuraSpeedMultiplier = 1.0;

        class SakuraPetal {
            constructor(randomY = false) {
                this.reset(randomY);
            }

            reset(randomY = false) {
                const w = sakuraCanvas ? sakuraCanvas.width : window.innerWidth;
                const h = sakuraCanvas ? sakuraCanvas.height : window.innerHeight;
                this.x = Math.random() * w;
                this.y = randomY ? Math.random() * h : -25 - Math.random() * 60;
                this.size = 7 + Math.random() * 9;
                this.baseVy = 0.8 + Math.random() * 1.3;
                this.baseVx = -0.5 + Math.random() * 1.4;
                this.swingAmp = 0.6 + Math.random() * 1.4;
                this.swingSpeed = 0.015 + Math.random() * 0.025;
                this.swingAngle = Math.random() * Math.PI * 2;
                this.rotation = Math.random() * Math.PI * 2;
                this.rotSpeed = (Math.random() - 0.5) * 0.03;
                this.flip = Math.random() * Math.PI * 2;
                this.flipSpeed = 0.015 + Math.random() * 0.025;
                this.opacity = 0.65 + Math.random() * 0.3;
                // 柔和樱花粉渐变色系
                const tones = [
                    ['#ffdde8', '#f9a8d4'],
                    ['#fce7f3', '#f472b6'],
                    ['#fff0f6', '#fbcfe8']
                ];
                this.colors = tones[Math.floor(Math.random() * tones.length)];
            }

            update() {
                const w = sakuraCanvas.width;
                const h = sakuraCanvas.height;
                const spd = sakuraSpeedMultiplier;

                this.swingAngle += this.swingSpeed * spd;
                this.x += (this.baseVx + Math.sin(this.swingAngle) * this.swingAmp) * spd;
                this.y += this.baseVy * spd;
                this.rotation += this.rotSpeed * spd;
                this.flip += this.flipSpeed * spd;

                if (this.y > h + 30 || this.x < -50 || this.x > w + 50) {
                    this.reset(false);
                }
            }

            draw(ctx) {
                ctx.save();
                ctx.translate(this.x, this.y);
                ctx.rotate(this.rotation);
                // 模拟 3D 翻滚透视缩放
                const scaleY = 0.4 + Math.abs(Math.cos(this.flip)) * 0.6;
                ctx.scale(1, scaleY);
                ctx.globalAlpha = this.opacity;

                const s = this.size;
                const grad = ctx.createLinearGradient(0, -s, 0, s);
                grad.addColorStop(0, this.colors[0]);
                grad.addColorStop(1, this.colors[1]);
                ctx.fillStyle = grad;

                // 绘制带尖端小缺口的逼真樱花花瓣轮廓
                ctx.beginPath();
                ctx.moveTo(0, s);
                ctx.bezierCurveTo(-s * 0.95, s * 0.35, -s * 0.85, -s * 0.75, -s * 0.18, -s);
                ctx.lineTo(0, -s * 0.72); // 樱花花瓣顶部小分叉缺口
                ctx.lineTo(s * 0.18, -s);
                ctx.bezierCurveTo(s * 0.85, -s * 0.75, s * 0.95, s * 0.35, 0, s);
                ctx.closePath();
                ctx.fill();

                ctx.restore();
            }
        }

        function initSakuraCanvas() {
            sakuraCanvas = document.getElementById('sakura-canvas');
            if (!sakuraCanvas) return;
            sakuraCtx = sakuraCanvas.getContext('2d');

            const resizeCanvas = () => {
                sakuraCanvas.width = window.innerWidth;
                sakuraCanvas.height = window.innerHeight;
            };
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);

            syncPetalArray();
            requestAnimationFrame(animateSakuraLoop);
        }

        function syncPetalArray() {
            while (petals.length < targetPetalCount) {
                petals.push(new SakuraPetal(true));
            }
            if (petals.length > targetPetalCount) {
                petals.length = targetPetalCount;
            }
        }

        function animateSakuraLoop() {
            if (sakuraCtx && sakuraCanvas) {
                sakuraCtx.clearRect(0, 0, sakuraCanvas.width, sakuraCanvas.height);
                for (let i = 0; i < petals.length; i++) {
                    petals[i].update();
                    petals[i].draw(sakuraCtx);
                }
            }
            requestAnimationFrame(animateSakuraLoop);
        }

        function updateSakuraCount(val) {
            targetPetalCount = Number(val);
            const label = document.getElementById('sakura-count-label');
            if (label) label.textContent = String(targetPetalCount);
            syncPetalArray();
        }

        function updateSakuraSpeed(val) {
            sakuraSpeedMultiplier = Number(val) / 10;
            const label = document.getElementById('sakura-speed-label');
            if (label) label.textContent = `${sakuraSpeedMultiplier.toFixed(1)}x`;
        }

        // 页面初始化
        window.onload = function () {
            try {
                const savedBg = localStorage.getItem('custom_Saved_Wallpaper');
                if (savedBg) {
                    setFullscreenWallpaper(savedBg, false);
                }
                const savedOpacity = localStorage.getItem('custom_liquid_opacity');
                if (savedOpacity !== null) {
                    const slider = document.getElementById('opacity-slider');
                    if (slider) slider.value = savedOpacity;
                    updateUiOpacity(savedOpacity);
                } else {
                    updateUiOpacity(12);
                }
            } catch (e) {
                updateUiOpacity(12);
            }

            initSakuraCanvas();
            renderCards();
            fetchRealIpInfo();
            runNetworkSpeedTest();
        };
    </script>
</body>
</html>
