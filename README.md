# photo-editor-app
Photo Studio Pro — A browser-based web application for professional photo editing and color grading. Features 20+ cinematic presets, an advanced adjustments suite, Krita-style blur effects, an RGB luminance histogram, crop tools, and split-screen comparison, built with HTML5 Canvas and Tailwind CSS.
[README.md](https://github.com/user-attachments/files/34525248/README.md)
# Photo Studio Pro 📸✨

A lightweight, powerful, and modern client-side photo editor and post-processing web utility built with **HTML5 Canvas**, **JavaScript**, and **Tailwind CSS**. No installation or server required — just open and edit!

## 🚀 Key Features

* **20+ Professional Presets:** Quick-apply cinematic, cyberpunk, vintage, golden hour, and black & white styles.
* **Advanced Color Correction:** Fine-tune exposure, contrast, saturation, highlights, shadows, color temperature, vignette, and sharpness using smooth interactive sliders.
* **Krita-Style Blur Effects:** Gaussian blur, lens bokeh simulation, radial zoom, and motion blur effects.
* **Photographer Utilities:** 
  * Real-time **RGB & Luminance Histogram**.
  * Flexible **Aspect Ratio Cropping** (1:1, 4:5, 16:9, 9:16, 3:2).
  * Custom **Watermark Overlay** with adjustable opacity.
  * EXIF & technical specs inspector.
* **Side-by-Side Comparison:** Interactive "Before / After" split-screen slider.
* **History & Shortcuts:** Full Undo (`Ctrl+Z`) / Redo (`Ctrl+Y`), copy (`Ctrl+C`), paste (`Ctrl+V`) from clipboard, and quick export in JPEG, PNG, or WebP.

## 🛠️ Tech Stack

* **HTML5 Canvas API** (for high-performance pixel manipulation)
* **Tailwind CSS** (for modern dark-mode UI styling)
* **FontAwesome** (for UI icons)
* **Vanilla JavaScript (ES6+)** (class-based modular architecture)

## 📦 How to Run

1. Download or clone the `photo-studio-pro.html` file.
2. Rename it to `index.html` (optional).
3. Open it in any modern web browser (Chrome, Firefox, Safari, Edge).
[photo-studio-pro.html](https://github.com/user-attachments/files/34525265/photo-studio-pro.html)
All code: 
<!DOCTYPE html>
<html lang="uk" class="h-full bg-slate-950 text-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Photo Studio Pro - Професійний Редактор Фотографій</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        ::-webkit-scrollbar {
            width: 5px;
            height: 5px;
        }
        ::-webkit-scrollbar-track {
            background: #090d16;
        }
        ::-webkit-scrollbar-thumb {
            background: #1e293b;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #334155;
        }
        
        .checkerboard {
            background-color: #0f172a;
            background-image: 
                linear-gradient(45deg, #020617 25%, transparent 25%), 
                linear-gradient(-45deg, #020617 25%, transparent 25%), 
                linear-gradient(45deg, transparent 75%, #020617 75%), 
                linear-gradient(-45deg, transparent 75%, #020617 75%);
            background-size: 20px 20px;
            background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
        }

        .tab-btn.active {
            border-bottom-color: #6366f1;
            color: #818cf8;
            background-color: rgba(99, 102, 241, 0.05);
        }

        /* Before/After Split Container */
        .split-slider-handle {
            touch-action: none;
        }
    </style>
</head>
<body class="h-full flex flex-col font-sans overflow-hidden select-none">

    <!-- Top Navigation & Shortcut Action Bar -->
    <header class="bg-slate-900 border-b border-slate-800 px-4 py-2.5 flex items-center justify-between shrink-0 z-20">
        <div class="flex items-center space-x-3">
            <div class="bg-gradient-to-tr from-indigo-600 to-violet-600 text-white p-2 rounded-xl flex items-center justify-center shadow-lg shadow-indigo-500/20">
                <i class="fa-solid fa-camera-retro text-xl"></i>
            </div>
            <div>
                <h1 class="font-bold text-base leading-none bg-gradient-to-r from-indigo-400 via-purple-300 to-pink-400 bg-clip-text text-transparent">Photo Studio Pro</h1>
                <p class="text-[11px] text-slate-400 mt-0.5">Утиліта для фотографів & обробка фотографій</p>
            </div>
        </div>

        <!-- Quick Shortcut Actions UI Buttons (Corresponding to Keyboard Shortcuts) -->
        <div class="hidden lg:flex items-center space-x-1.5 bg-slate-950/70 p-1 rounded-xl border border-slate-800/80">
            <button id="btnUndo" class="px-2.5 py-1.5 rounded-lg text-xs bg-slate-900 hover:bg-slate-800 text-slate-300 hover:text-white transition flex items-center space-x-1.5" title="Ctrl+Z">
                <i class="fa-solid fa-rotate-left text-indigo-400"></i>
                <span>Undo</span>
                <span class="text-[10px] text-slate-500 bg-slate-950 px-1 rounded font-mono">Ctrl+Z</span>
            </button>
            <button id="btnRedo" class="px-2.5 py-1.5 rounded-lg text-xs bg-slate-900 hover:bg-slate-800 text-slate-300 hover:text-white transition flex items-center space-x-1.5" title="Ctrl+Y / Ctrl+Shift+Z">
                <i class="fa-solid fa-rotate-right text-indigo-400"></i>
                <span>Redo</span>
                <span class="text-[10px] text-slate-500 bg-slate-950 px-1 rounded font-mono">Ctrl+Y</span>
            </button>
            <div class="h-4 w-px bg-slate-800 mx-1"></div>
            <button id="btnCopy" class="px-2.5 py-1.5 rounded-lg text-xs bg-slate-900 hover:bg-slate-800 text-slate-300 hover:text-white transition flex items-center space-x-1.5" title="Ctrl+C">
                <i class="fa-solid fa-copy text-amber-400"></i>
                <span>Копіювати</span>
                <span class="text-[10px] text-slate-500 bg-slate-950 px-1 rounded font-mono">Ctrl+C</span>
            </button>
            <button id="btnPaste" class="px-2.5 py-1.5 rounded-lg text-xs bg-slate-900 hover:bg-slate-800 text-slate-300 hover:text-white transition flex items-center space-x-1.5" title="Ctrl+V">
                <i class="fa-solid fa-paste text-amber-400"></i>
                <span>Вставити</span>
                <span class="text-[10px] text-slate-500 bg-slate-950 px-1 rounded font-mono">Ctrl+V</span>
            </button>
            <div class="h-4 w-px bg-slate-800 mx-1"></div>
            <button id="btnReset" class="px-2.5 py-1.5 rounded-lg text-xs bg-slate-900 hover:bg-slate-800 text-slate-300 hover:text-white transition flex items-center space-x-1.5" title="Ctrl+A">
                <i class="fa-solid fa-arrows-rotate text-emerald-400"></i>
                <span>Скинути</span>
                <span class="text-[10px] text-slate-500 bg-slate-950 px-1 rounded font-mono">Ctrl+A</span>
            </button>
        </div>

        <!-- Main Header Actions -->
        <div class="flex items-center space-x-3">
            <input type="file" id="fileInput" accept="image/*" class="hidden">
            <button id="openFileBtn" class="bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-200 text-xs font-medium px-3.5 py-2 rounded-xl transition flex items-center space-x-2">
                <i class="fa-solid fa-folder-open text-indigo-400"></i>
                <span>Відкрити фото</span>
            </button>
            
            <button id="exportBtn" class="bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-500 hover:to-violet-500 text-white font-medium text-xs px-4 py-2 rounded-xl flex items-center space-x-2 shadow-lg shadow-indigo-600/25 transition transform active:scale-95" title="Ctrl+S">
                <i class="fa-solid fa-download"></i>
                <span>Зберегти</span>
                <span class="text-[10px] opacity-75 font-mono hidden sm:inline">Ctrl+S</span>
            </button>
        </div>
    </header>

    <!-- Main Studio Workspace -->
    <div class="flex-1 flex overflow-hidden">
        
        <!-- Main Viewport (Canvas / Before-After Comparison) -->
        <main class="flex-1 flex flex-col bg-slate-950 relative overflow-hidden">
            
            <!-- View Mode Switcher Header -->
            <div class="bg-slate-900/60 border-b border-slate-800/80 px-4 py-2 flex items-center justify-between text-xs backdrop-blur z-10">
                <div class="flex items-center space-x-3">
                    <button id="modeSingleBtn" class="px-3 py-1 rounded-lg bg-indigo-600/20 text-indigo-300 border border-indigo-500/30 font-medium">Звичайний перегляд</button>
                    <button id="modeCompareBtn" class="px-3 py-1 rounded-lg bg-slate-800 text-slate-400 hover:text-slate-200 border border-slate-700 transition">До / Після (Спліт)</button>
                </div>

                <!-- Info Specs Badge -->
                <div id="imageSpecs" class="hidden sm:flex items-center space-x-3 text-slate-400 font-mono text-[11px]">
                    <span id="specRes"><i class="fa-solid fa-expand text-slate-500 mr-1"></i>0x0 px</span>
                    <span id="specMp"><i class="fa-solid fa-microchip text-slate-500 mr-1"></i>0 MP</span>
                    <span id="specRatio"><i class="fa-solid fa-crop text-slate-500 mr-1"></i>-</span>
                </div>
            </div>

            <!-- Canvas Container Viewport -->
            <div id="viewportContainer" class="flex-1 flex items-center justify-center p-6 overflow-auto checkerboard relative">
                
                <!-- Placeholder Screen when no image is loaded -->
                <div id="uploadPlaceholder" class="flex flex-col items-center justify-center text-center p-8 border-2 border-dashed border-slate-800 rounded-3xl max-w-md bg-slate-900/40 backdrop-blur">
                    <div class="w-16 h-16 bg-slate-800/80 rounded-2xl flex items-center justify-center text-slate-400 text-2xl mb-4 shadow-inner">
                        <i class="fa-solid fa-cloud-arrow-up"></i>
                    </div>
                    <h3 class="text-base font-semibold text-slate-200">Перетягніть сюди фотографію</h3>
                    <p class="text-xs text-slate-400 mt-1 mb-4">підтримуються файли JPG, PNG, WebP (RAW preview)</p>
                    <button onclick="document.getElementById('fileInput').click()" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-medium px-5 py-2.5 rounded-xl transition shadow-lg shadow-indigo-600/30">
                        Обрати файл з диска
                    </button>
                    <p class="text-[11px] text-slate-500 mt-4">або натисніть <span class="bg-slate-800 px-1.5 py-0.5 rounded text-slate-300 font-mono">Ctrl+V</span> щоб вставити з буфера</p>
                </div>

                <!-- Active Main Working Canvas -->
                <div id="canvasWrapper" class="relative max-w-full max-h-full shadow-2xl rounded-lg overflow-hidden hidden border border-slate-800">
                    <canvas id="mainCanvas" class="max-w-full max-h-full block"></canvas>

                    <!-- Split Screen Compare Overlay -->
                    <div id="compareOverlay" class="absolute top-0 left-0 bottom-0 overflow-hidden hidden border-r-2 border-amber-400 shadow-2xl pointer-events-none">
                        <canvas id="originalCanvas" class="block max-w-none max-h-none"></canvas>
                        <div class="absolute top-2 left-2 bg-slate-900/80 backdrop-blur text-amber-400 text-[10px] font-bold px-2 py-0.5 rounded border border-amber-500/30">ДО</div>
                    </div>
                    <div id="compareTagAfter" class="absolute top-2 right-2 bg-slate-900/80 backdrop-blur text-indigo-400 text-[10px] font-bold px-2 py-0.5 rounded border border-indigo-500/30 hidden pointer-events-none">ПІСЛЯ</div>
                    
                    <!-- Split Handle Slider UI -->
                    <div id="splitHandle" class="absolute top-0 bottom-0 w-1 bg-amber-400 cursor-ew-resize hidden z-20 flex items-center justify-center split-slider-handle">
                        <div class="w-6 h-6 bg-amber-400 text-slate-950 rounded-full flex items-center justify-center shadow-lg text-xs">
                            <i class="fa-solid fa-arrows-left-right"></i>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Bottom Floating Quick Status & Shortcuts Footer -->
            <footer class="bg-slate-900 border-t border-slate-800 px-4 py-2 flex items-center justify-between shrink-0 text-xs text-slate-400">
                <div class="flex items-center space-x-4">
                    <span class="flex items-center"><i class="fa-solid fa-circle text-[8px] text-emerald-400 mr-1.5"></i> Canvas 2D Engine</span>
                    <span id="activePresetBadge" class="bg-slate-800 text-slate-300 px-2 py-0.5 rounded text-[11px]">Оригінал</span>
                </div>
                <div class="hidden md:flex items-center space-x-3 text-[11px]">
                    <span>Швидкі клавіші:</span>
                    <span class="bg-slate-950 px-1.5 py-0.5 rounded border border-slate-800 text-slate-300 font-mono">Ctrl+Z Undo</span>
                    <span class="bg-slate-950 px-1.5 py-0.5 rounded border border-slate-800 text-slate-300 font-mono">Ctrl+Y Redo</span>
                    <span class="bg-slate-950 px-1.5 py-0.5 rounded border border-slate-800 text-slate-300 font-mono">Ctrl+C Copy</span>
                    <span class="bg-slate-950 px-1.5 py-0.5 rounded border border-slate-800 text-slate-300 font-mono">Ctrl+V Paste</span>
                </div>
            </footer>
        </main>

        <!-- Right Control Sidebar Panel -->
        <aside class="w-96 bg-slate-900 border-l border-slate-800 flex flex-col shrink-0">
            
            <!-- Sidebar Navigation Tabs -->
            <div class="grid grid-cols-4 border-b border-slate-800 text-xs font-semibold bg-slate-950/60">
                <button class="tab-btn active py-3 text-center border-b-2 border-transparent hover:text-slate-200 transition" data-tab="tab-presets">
                    <i class="fa-solid fa-wand-magic-sparkles block text-sm mb-1"></i>Шаблони
                </button>
                <button class="tab-btn py-3 text-center border-b-2 border-transparent hover:text-slate-200 transition" data-tab="tab-adjust">
                    <i class="fa-solid fa-sliders block text-sm mb-1"></i>Корекція
                </button>
                <button class="tab-btn py-3 text-center border-b-2 border-transparent hover:text-slate-200 transition" data-tab="tab-blur">
                    <i class="fa-solid fa-droplet block text-sm mb-1"></i>Блюр FX
                </button>
                <button class="tab-btn py-3 text-center border-b-2 border-transparent hover:text-slate-200 transition" data-tab="tab-tools">
                    <i class="fa-solid fa-toolbox block text-sm mb-1"></i>Утиліти
                </button>
            </div>

            <!-- Tab Contents Wrapper -->
            <div class="flex-1 overflow-y-auto p-4 space-y-6">

                <!-- 1. PRESETS TAB (20+ Presets Grid) -->
                <div id="tab-presets" class="tab-content">
                    <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-3">20+ Готових пресетів & стилів</h3>
                    <div id="presetsGrid" class="grid grid-cols-2 gap-2.5">
                        <!-- Dynamically populated via JS with presets -->
                    </div>
                </div>

                <!-- 2. ADJUSTMENTS TAB -->
                <div id="tab-adjust" class="tab-content hidden space-y-4">
                    <div class="flex items-center justify-between">
                        <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">Основна світлокорекція</h3>
                        <button id="resetAdjustmentsBtn" class="text-[11px] text-indigo-400 hover:underline">Скинути слайдери</button>
                    </div>

                    <!-- Exposure / Brightness / Contrast -->
                    <div class="space-y-3 bg-slate-950/40 p-3 rounded-xl border border-slate-800">
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Експозиція</span>
                                <span id="valExposure" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjExposure" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Контраст</span>
                                <span id="valContrast" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjContrast" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Насиченість</span>
                                <span id="valSaturation" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjSaturation" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                    </div>

                    <!-- Highlights & Shadows -->
                    <h4 class="text-xs font-bold text-slate-400 uppercase tracking-wider pt-2">Тіні та Світлі ділянки</h4>
                    <div class="space-y-3 bg-slate-950/40 p-3 rounded-xl border border-slate-800">
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Світлі місця (Highlights)</span>
                                <span id="valHighlights" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjHighlights" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Глибина тіней (Shadows)</span>
                                <span id="valShadows" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjShadows" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                    </div>

                    <!-- Color Temp & Vignette -->
                    <h4 class="text-xs font-bold text-slate-400 uppercase tracking-wider pt-2">Колірний баланс & Віньєтка</h4>
                    <div class="space-y-3 bg-slate-950/40 p-3 rounded-xl border border-slate-800">
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Температура (Warmth)</span>
                                <span id="valTemp" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjTemp" min="-100" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Віньєтка (Vignette)</span>
                                <span id="valVignette" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjVignette" min="0" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Різкість (Sharpen)</span>
                                <span id="valSharpen" class="text-slate-400 font-mono">0</span>
                            </div>
                            <input type="range" id="adjSharpen" min="0" max="100" value="0" class="w-full accent-indigo-500">
                        </div>
                    </div>
                </div>

                <!-- 3. KRITA-STYLE BLUR FX TAB -->
                <div id="tab-blur" class="tab-content hidden space-y-4">
                    <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">Професійні Блюр-Ефекти</h3>
                    <p class="text-xs text-slate-400">Симуляція об'єктива та розмиття як у Krita/Photoshop.</p>

                    <div class="space-y-3 bg-slate-950/40 p-3.5 rounded-xl border border-slate-800">
                        <div>
                            <label class="text-xs font-semibold text-slate-300 block mb-1.5">Тип розмиття</label>
                            <select id="blurTypeSelect" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-2 text-xs text-slate-200 focus:outline-none focus:border-indigo-500">
                                <option value="none">Без розмиття</option>
                                <option value="gaussian">Гаусове Розмиття (Gaussian Blur)</option>
                                <option value="bokeh">Симуляція Боке / Лінзи (Bokeh FX)</option>
                                <option value="radial">Радіальне Розмиття (Radial Zoom)</option>
                                <option value="motion">Динамічне Розмиття (Motion Blur)</option>
                            </select>
                        </div>

                        <div>
                            <div class="flex justify-between text-xs mb-1">
                                <span class="text-slate-300">Інтенсивність (Радіус)</span>
                                <span id="valBlurRadius" class="text-slate-400 font-mono">0 px</span>
                            </div>
                            <input type="range" id="blurRadiusInput" min="0" max="40" value="0" class="w-full accent-indigo-500">
                        </div>
                    </div>
                </div>

                <!-- 4. PHOTOGRAPHER UTILITIES TAB -->
                <div id="tab-tools" class="tab-content hidden space-y-5">
                    
                    <!-- RGB & Luminance Histogram -->
                    <div>
                        <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-2 flex items-center justify-between">
                            <span>Гістограма (RGB & Luminance)</span>
                            <i class="fa-solid fa-chart-column text-indigo-400"></i>
                        </h3>
                        <div class="bg-slate-950 p-2 rounded-xl border border-slate-800 h-32 relative flex items-end">
                            <canvas id="histogramCanvas" class="w-full h-full block"></canvas>
                        </div>
                    </div>

                    <!-- Crop & Aspect Ratios -->
                    <div class="space-y-2">
                        <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">Кадрування & Пропорції</h3>
                        <div class="grid grid-cols-3 gap-2">
                            <button class="crop-preset-btn bg-slate-950 hover:bg-slate-800 border border-slate-800 rounded-lg py-2 text-center text-xs" data-ratio="1:1">1:1 Square</button>
                            <button class="crop-preset-btn bg-slate-950 hover:bg-slate-800 border border-slate-800 rounded-lg py-2 text-center text-xs" data-ratio="4:5">4:5 Insta</button>
                            <button class="crop-preset-btn bg-slate-950 hover:bg-slate-800 border border-slate-800 rounded-lg py-2 text-center text-xs" data-ratio="16:9">16:9 Video</button>
                            <button class="crop-preset-btn bg-slate-950 hover:bg-slate-800 border border-slate-800 rounded-lg py-2 text-center text-xs" data-ratio="9:16">9:16 Story</button>
                            <button class="crop-preset-btn bg-slate-950 hover:bg-slate-800 border border-slate-800 rounded-lg py-2 text-center text-xs" data-ratio="3:2">3:2 DSLR</button>
                            <button id="cropCancelBtn" class="bg-red-500/10 hover:bg-red-500/20 text-red-400 border border-red-500/20 rounded-lg py-2 text-center text-xs">Скинути</button>
                        </div>
                    </div>

                    <!-- Watermark Overlay -->
                    <div class="space-y-2 pt-2 border-t border-slate-800">
                        <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">Водяний знак / Вотермарка</h3>
                        <div class="bg-slate-950/40 p-3 rounded-xl border border-slate-800 space-y-2">
                            <input type="text" id="watermarkText" placeholder="© Авторське право / Текст" class="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 text-xs text-slate-200 focus:outline-none focus:border-indigo-500">
                            <div class="flex items-center justify-between text-xs">
                                <span class="text-slate-400">Прозорість</span>
                                <input type="range" id="watermarkOpacity" min="10" max="100" value="50" class="w-28 accent-indigo-500">
                            </div>
                            <button id="applyWatermarkBtn" class="w-full bg-indigo-600/20 hover:bg-indigo-600/30 text-indigo-300 border border-indigo-500/30 rounded-lg py-1.5 text-xs transition font-medium">Додати знак на фото</button>
                        </div>
                    </div>

                    <!-- EXIF / Technical Specs Box -->
                    <div class="space-y-2 pt-2 border-t border-slate-800">
                        <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">Інспектор метаданих</h3>
                        <div id="exifBox" class="bg-slate-950 p-3 rounded-xl border border-slate-800 font-mono text-[11px] text-slate-400 space-y-1">
                            <div>Файл: <span id="exifName" class="text-slate-200">-</span></div>
                            <div>Розмір: <span id="exifSize" class="text-slate-200">-</span></div>
                            <div>Колірна модель: <span class="text-slate-200">sRGB Standard Canvas</span></div>
                        </div>
                    </div>

                </div>

            </div>

        </aside>

    </div>

    <!-- Export Modal -->
    <div id="exportModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm z-50 flex items-center justify-center hidden">
        <div class="bg-slate-900 border border-slate-800 p-6 rounded-2xl max-w-sm w-full shadow-2xl space-y-5">
            <div class="flex items-center justify-between border-b border-slate-800 pb-3">
                <h3 class="text-base font-bold text-slate-100 flex items-center">
                    <i class="fa-solid fa-file-export text-indigo-400 mr-2"></i> Експорт Фотографії
                </h3>
                <button id="closeExportModal" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
            </div>

            <div class="space-y-4">
                <div>
                    <label class="text-xs font-semibold text-slate-300 block mb-1.5">Формат збереження</label>
                    <select id="exportFormat" class="w-full bg-slate-950 border border-slate-800 rounded-xl px-3 py-2 text-xs text-slate-200 focus:outline-none focus:border-indigo-500">
                        <option value="image/jpeg">JPEG (.jpg) - Найкраще для фото</option>
                        <option value="image/png">PNG (.png) - Без втрат якості</option>
                        <option value="image/webp">WebP (.webp) - Сучасний веб-формат</option>
                    </select>
                </div>

                <div>
                    <div class="flex justify-between text-xs mb-1">
                        <span class="text-slate-300">Якість стиснення</span>
                        <span id="exportQualityVal" class="text-slate-400 font-mono">92%</span>
                    </div>
                    <input type="range" id="exportQuality" min="10" max="100" value="92" class="w-full accent-indigo-500">
                </div>
            </div>

            <div class="pt-2 flex justify-end space-x-2">
                <button id="cancelExportBtn" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-300 text-xs font-medium rounded-xl transition">Скасувати</button>
                <button id="downloadFinalBtn" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-medium rounded-xl transition shadow-lg shadow-indigo-600/30">Завантажити</button>
            </div>
        </div>
    </div>

    <!-- Notification Toast Container -->
    <div id="toastContainer" class="fixed bottom-4 left-4 z-50 flex flex-col space-y-2 pointer-events-none"></div>

    <script>
        /**
         * Photo Studio Pro Engine
         */
        class PhotoStudio {
            constructor() {
                this.originalImage = null;
                this.originalCanvas = document.getElementById('originalCanvas');
                this.mainCanvas = document.getElementById('mainCanvas');
                this.ctx = this.mainCanvas.getContext('2d', { willReadFrequently: true });
                this.histogramCanvas = document.getElementById('histogramCanvas');

                // Undo / Redo history state
                this.history = [];
                this.historyIndex = -1;
                this.maxHistory = 15;

                // Adjustment states
                this.adjustments = {
                    exposure: 0,
                    contrast: 0,
                    saturation: 0,
                    highlights: 0,
                    shadows: 0,
                    temp: 0,
                    vignette: 0,
                    sharpen: 0,
                    blurType: 'none',
                    blurRadius: 0,
                    preset: 'Оригінал'
                };

                // Presets Definition (20 Preset Templates)
                this.presets = [
                    { id: 'orig', name: 'Оригінал', icon: 'fa-image', adj: {} },
                    { id: 'cinematic', name: 'Cinematic Teal', icon: 'fa-film', adj: { temp: -15, contrast: 20, saturation: 15, vignette: 30 } },
                    { id: 'cyberpunk', name: 'Cyberpunk Neon', icon: 'fa-bolt', adj: { temp: -30, saturation: 45, contrast: 30, highlights: -20 } },
                    { id: 'vintage80s', name: 'Vintage 1980s', icon: 'fa-compact-disc', adj: { temp: 25, contrast: -15, saturation: -20, vignette: 40 } },
                    { id: 'filmnoir', name: 'Film Noir B&W', icon: 'fa-moon', adj: { saturation: -100, contrast: 45, shadows: -30 } },
                    { id: 'golden', name: 'Golden Hour', icon: 'fa-sun', adj: { temp: 40, exposure: 10, saturation: 20, highlights: 15 } },
                    { id: 'hdr', name: 'HDR Dramatic', icon: 'fa-eye', adj: { contrast: 35, saturation: 25, sharpen: 30, shadows: 20 } },
                    { id: 'pastel', name: 'Pastel Dream', icon: 'fa-cloud', adj: { exposure: 15, contrast: -25, saturation: -15, temp: 10 } },
                    { id: 'portrait', name: 'Warm Portrait', icon: 'fa-user', adj: { temp: 15, contrast: -10, highlights: -10, shadows: 10 } },
                    { id: 'winter', name: 'Cold Winter', icon: 'fa-snowflake', adj: { temp: -40, saturation: -10, exposure: 5 } },
                    { id: 'cross', name: 'Cross Process', icon: 'fa-shuffle', adj: { contrast: 25, temp: -20, saturation: 30 } },
                    { id: 'sepia', name: 'Sepia Retro', icon: 'fa-box-archive', adj: { temp: 50, saturation: -60, contrast: 10 } },
                    { id: 'moody', name: 'Moody Dark', icon: 'fa-cloud-moon', adj: { exposure: -15, contrast: 25, shadows: -40, vignette: 50 } },
                    { id: 'highbw', name: 'High-Contrast B&W', icon: 'fa-circle-half-stroke', adj: { saturation: -100, contrast: 60, exposure: 10 } },
                    { id: 'summer', name: 'Vibrant Summer', icon: 'fa-umbrella-beach', adj: { saturation: 40, temp: 15, exposure: 10 } },
                    { id: 'matte', name: 'Matte Film', icon: 'fa-camera', adj: { contrast: -20, shadows: 30, vignette: 20 } },
                    { id: 'tealorange', name: 'Teal & Orange', icon: 'fa-palette', adj: { temp: -10, saturation: 35, contrast: 15 } },
                    { id: 'infrared', name: 'Infrared Glow', icon: 'fa-circle-radiation', adj: { temp: -50, contrast: 40, saturation: -30 } },
                    { id: 'softglow', name: 'Soft Glow', icon: 'fa-wand-magic', adj: { exposure: 10, contrast: -15, blurType: 'gaussian', blurRadius: 3 } },
                    { id: 'emerald', name: 'Emerald Forest', icon: 'fa-tree', adj: { temp: -25, saturation: 20, contrast: 15 } }
                ];

                this.initDOM();
                this.bindEvents();
                this.renderPresetsList();
            }

            initDOM() {
                // UI Elements
                this.uploadPlaceholder = document.getElementById('uploadPlaceholder');
                this.canvasWrapper = document.getElementById('canvasWrapper');
                this.compareOverlay = document.getElementById('compareOverlay');
                this.compareTagAfter = document.getElementById('compareTagAfter');
                this.splitHandle = document.getElementById('splitHandle');
                this.fileInput = document.getElementById('fileInput');
                this.activePresetBadge = document.getElementById('activePresetBadge');

                // Specs
                this.specRes = document.getElementById('specRes');
                this.specMp = document.getElementById('specMp');
                this.specRatio = document.getElementById('specRatio');
                this.exifName = document.getElementById('exifName');
                this.exifSize = document.getElementById('exifSize');
            }

            bindEvents() {
                // File Upload
                document.getElementById('openFileBtn').addEventListener('click', () => this.fileInput.click());
                this.fileInput.addEventListener('change', (e) => this.handleFileSelect(e));

                // Drag and Drop
                window.addEventListener('dragover', (e) => e.preventDefault());
                window.addEventListener('drop', (e) => {
                    e.preventDefault();
                    if (e.dataTransfer.files.length) {
                        this.loadImageFile(e.dataTransfer.files[0]);
                    }
                });

                // Tab Navigation
                document.querySelectorAll('.tab-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
                        document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
                        
                        const target = e.currentTarget;
                        target.classList.add('active');
                        document.getElementById(target.dataset.tab).classList.remove('hidden');
                    });
                });

                // Keyboard Shortcuts Engine (Ctrl+Z, Ctrl+Y, Ctrl+C, Ctrl+V, Ctrl+A, Ctrl+S)
                window.addEventListener('keydown', (e) => {
                    if (e.ctrlKey || e.metaKey) {
                        const key = e.key.toLowerCase();
                        if (key === 'z') {
                            e.preventDefault();
                            if (e.shiftKey) this.redo();
                            else this.undo();
                        } else if (key === 'y') {
                            e.preventDefault();
                            this.redo();
                        } else if (key === 'c') {
                            e.preventDefault();
                            this.copyToClipboard();
                        } else if (key === 'v') {
                            e.preventDefault();
                            this.pasteFromClipboard();
                        } else if (key === 'a') {
                            e.preventDefault();
                            this.resetAdjustments();
                            this.showToast('Параметри фото скинуто');
                        } else if (key === 's') {
                            e.preventDefault();
                            this.openExportModal();
                        }
                    }
                });

                // Quick Action Buttons in Top Navbar
                document.getElementById('btnUndo').addEventListener('click', () => this.undo());
                document.getElementById('btnRedo').addEventListener('click', () => this.redo());
                document.getElementById('btnCopy').addEventListener('click', () => this.copyToClipboard());
                document.getElementById('btnPaste').addEventListener('click', () => this.pasteFromClipboard());
                document.getElementById('btnReset').addEventListener('click', () => {
                    this.resetAdjustments();
                    this.showToast('Параметри фото скинуто');
                });

                // Adjustment Sliders
                const sliders = ['Exposure', 'Contrast', 'Saturation', 'Highlights', 'Shadows', 'Temp', 'Vignette', 'Sharpen'];
                sliders.forEach(adj => {
                    const el = document.getElementById(`adj${adj}`);
                    const valEl = document.getElementById(`val${adj}`);
                    el.addEventListener('input', (e) => {
                        valEl.textContent = e.target.value;
                        this.adjustments[adj.toLowerCase()] = parseInt(e.target.value);
                        this.applyProcessing();
                    });
                    el.addEventListener('change', () => this.saveHistoryState());
                });

                document.getElementById('resetAdjustmentsBtn').addEventListener('click', () => this.resetAdjustments());

                // Blur Controls
                document.getElementById('blurTypeSelect').addEventListener('change', (e) => {
                    this.adjustments.blurType = e.target.value;
                    this.applyProcessing();
                    this.saveHistoryState();
                });
                document.getElementById('blurRadiusInput').addEventListener('input', (e) => {
                    document.getElementById('valBlurRadius').textContent = `${e.target.value} px`;
                    this.adjustments.blurRadius = parseInt(e.target.value);
                    this.applyProcessing();
                });
                document.getElementById('blurRadiusInput').addEventListener('change', () => this.saveHistoryState());

                // View Modes (Single / Compare Split)
                const modeSingleBtn = document.getElementById('modeSingleBtn');
                const modeCompareBtn = document.getElementById('modeCompareBtn');

                modeSingleBtn.addEventListener('click', () => {
                    modeSingleBtn.className = "px-3 py-1 rounded-lg bg-indigo-600/20 text-indigo-300 border border-indigo-500/30 font-medium";
                    modeCompareBtn.className = "px-3 py-1 rounded-lg bg-slate-800 text-slate-400 hover:text-slate-200 border border-slate-700 transition";
                    this.compareOverlay.classList.add('hidden');
                    this.compareTagAfter.classList.add('hidden');
                    this.splitHandle.classList.add('hidden');
                });

                modeCompareBtn.addEventListener('click', () => {
                    modeCompareBtn.className = "px-3 py-1 rounded-lg bg-indigo-600/20 text-indigo-300 border border-indigo-500/30 font-medium";
                    modeSingleBtn.className = "px-3 py-1 rounded-lg bg-slate-800 text-slate-400 hover:text-slate-200 border border-slate-700 transition";
                    this.compareOverlay.classList.remove('hidden');
                    this.compareTagAfter.classList.remove('hidden');
                    this.splitHandle.classList.remove('hidden');
                    this.updateSplitPosition(0.5);
                });

                // Split Handle Dragging
                let isDraggingSplit = false;
                this.splitHandle.addEventListener('mousedown', () => isDraggingSplit = true);
                window.addEventListener('mouseup', () => isDraggingSplit = false);
                window.addEventListener('mousemove', (e) => {
                    if (!isDraggingSplit) return;
                    const rect = this.canvasWrapper.getBoundingClientRect();
                    let pos = (e.clientX - rect.left) / rect.width;
                    pos = Math.max(0, Math.min(1, pos));
                    this.updateSplitPosition(pos);
                });

                // Tools & Utilities
                document.querySelectorAll('.crop-preset-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => this.applyCropRatio(e.currentTarget.dataset.ratio));
                });
                document.getElementById('cropCancelBtn').addEventListener('click', () => {
                    if (this.originalImage) this.loadImageFile(this.currentFileObject);
                });

                document.getElementById('applyWatermarkBtn').addEventListener('click', () => this.applyWatermark());

                // Export Modal
                document.getElementById('exportBtn').addEventListener('click', () => this.openExportModal());
                document.getElementById('closeExportModal').addEventListener('click', () => this.closeExportModal());
                document.getElementById('cancelExportBtn').addEventListener('click', () => this.closeExportModal());
                document.getElementById('exportQuality').addEventListener('input', (e) => {
                    document.getElementById('exportQualityVal').textContent = `${e.target.value}%`;
                });
                document.getElementById('downloadFinalBtn').addEventListener('click', () => this.downloadExportedImage());
            }

            renderPresetsList() {
                const grid = document.getElementById('presetsGrid');
                grid.innerHTML = '';
                this.presets.forEach(p => {
                    const btn = document.createElement('button');
                    btn.className = "flex items-center space-x-2.5 bg-slate-950/60 hover:bg-slate-800 border border-slate-800/80 hover:border-slate-700 p-2.5 rounded-xl transition text-left group";
                    btn.innerHTML = `
                        <div class="w-8 h-8 rounded-lg bg-slate-900 border border-slate-700 flex items-center justify-center text-indigo-400 group-hover:scale-110 transition">
                            <i class="fa-solid ${p.icon} text-xs"></i>
                        </div>
                        <div>
                            <div class="text-xs font-semibold text-slate-200 leading-tight">${p.name}</div>
                        </div>
                    `;
                    btn.addEventListener('click', () => this.applyPreset(p));
                    grid.appendChild(btn);
                });
            }

            handleFileSelect(e) {
                if (e.target.files.length) {
                    this.loadImageFile(e.target.files[0]);
                }
            }

            loadImageFile(file) {
                if (!file || !file.type.startsWith('image/')) return;
                this.currentFileObject = file;
                const reader = new FileReader();
                reader.onload = (event) => {
                    const img = new Image();
                    img.onload = () => {
                        this.originalImage = img;
                        
                        // Setup Canvases
                        this.mainCanvas.width = img.width;
                        this.mainCanvas.height = img.height;
                        this.originalCanvas.width = img.width;
                        this.originalCanvas.height = img.height;

                        // Draw Original Image to Original Canvas for split compare
                        const origCtx = this.originalCanvas.getContext('2d');
                        origCtx.drawImage(img, 0, 0);

                        this.uploadPlaceholder.classList.add('hidden');
                        this.canvasWrapper.classList.remove('hidden');

                        // Update metadata specs
                        const mp = ((img.width * img.height) / 1000000).toFixed(1);
                        this.specRes.innerHTML = `<i class="fa-solid fa-expand text-slate-500 mr-1"></i>${img.width}x${img.height} px`;
                        this.specMp.innerHTML = `<i class="fa-solid fa-microchip text-slate-500 mr-1"></i>${mp} MP`;
                        this.specRatio.innerHTML = `<i class="fa-solid fa-crop text-slate-500 mr-1"></i>${this.getAspectRatioText(img.width, img.height)}`;
                        
                        this.exifName.textContent = file.name || 'Image';
                        this.exifSize.textContent = `${(file.size / (1024 * 1024)).toFixed(2)} MB`;

                        this.resetAdjustments();
                        this.saveHistoryState();
                        this.showToast('Фото успішно завантажено');
                    };
                    img.src = event.target.result;
                };
                reader.readAsDataURL(file);
            }

            getAspectRatioText(w, h) {
                const gcd = (a, b) => b === 0 ? a : gcd(b, a % b);
                const divisor = gcd(w, h);
                return `${w/divisor}:${h/divisor}`;
            }

            // Apply Adjustments & Filters
            applyProcessing() {
                if (!this.originalImage) return;

                const w = this.mainCanvas.width;
                const h = this.mainCanvas.height;

                // Draw base image
                this.ctx.clearRect(0, 0, w, h);
                this.ctx.drawImage(this.originalImage, 0, 0, w, h);

                let imgData = this.ctx.getImageData(0, 0, w, h);
                let data = imgData.data;

                const exp = this.adjustments.exposure * 2.55;
                const con = (this.adjustments.contrast + 100) / 100;
                const sat = (this.adjustments.saturation + 100) / 100;
                const hl = this.adjustments.highlights;
                const sh = this.adjustments.shadows;
                const temp = this.adjustments.temp;

                for (let i = 0; i < data.length; i += 4) {
                    let r = data[i];
                    let g = data[i + 1];
                    let b = data[i + 2];

                    // Temperature (Color Warmth)
                    if (temp > 0) {
                        r += temp * 0.5;
                        b -= temp * 0.3;
                    } else if (temp < 0) {
                        r += temp * 0.3;
                        b -= temp * 0.5;
                    }

                    // Exposure
                    r += exp;
                    g += exp;
                    b += exp;

                    // Contrast
                    r = ((r / 255 - 0.5) * con + 0.5) * 255;
                    g = ((g / 255 - 0.5) * con + 0.5) * 255;
                    b = ((b / 255 - 0.5) * con + 0.5) * 255;

                    // Saturation
                    const gray = 0.2989 * r + 0.5870 * g + 0.1140 * b;
                    r = gray + sat * (r - gray);
                    g = gray + sat * (g - gray);
                    b = gray + sat * (b - gray);

                    // Highlights & Shadows
                    const luminance = (r + g + b) / 3;
                    if (luminance > 128 && hl !== 0) {
                        const factor = (luminance - 128) / 127;
                        r += hl * factor;
                        g += hl * factor;
                        b += hl * factor;
                    } else if (luminance <= 128 && sh !== 0) {
                        const factor = (128 - luminance) / 128;
                        r += sh * factor;
                        g += sh * factor;
                        b += sh * factor;
                    }

                    // Clamp values
                    data[i] = Math.min(255, Math.max(0, r));
                    data[i + 1] = Math.min(255, Math.max(0, g));
                    data[i + 2] = Math.min(255, Math.max(0, b));
                }

                this.ctx.putImageData(imgData, 0, 0);

                // Vignette Effect
                if (this.adjustments.vignette > 0) {
                    const gradient = this.ctx.createRadialGradient(
                        w / 2, h / 2, Math.max(w, h) * 0.3,
                        w / 2, h / 2, Math.max(w, h) * 0.7
                    );
                    gradient.addColorStop(0, 'rgba(0,0,0,0)');
                    gradient.addColorStop(1, `rgba(0,0,0,${this.adjustments.vignette / 100})`);
                    this.ctx.fillStyle = gradient;
                    this.ctx.fillRect(0, 0, w, h);
                }

                // Blur FX Suite (Krita-style multi-pass algorithms)
                if (this.adjustments.blurType !== 'none' && this.adjustments.blurRadius > 0) {
                    this.applyBlurEffect(this.adjustments.blurType, this.adjustments.blurRadius);
                }

                this.renderHistogram();
            }

            // Krita-Style Blur Engine Implementation
            applyBlurEffect(type, radius) {
                const w = this.mainCanvas.width;
                const h = this.mainCanvas.height;

                if (type === 'gaussian' || type === 'bokeh') {
                    this.ctx.filter = `blur(${radius}px)`;
                    this.ctx.drawImage(this.mainCanvas, 0, 0);
                    this.ctx.filter = 'none';
                } else if (type === 'motion') {
                    // Motion blur simulation via multi-directional offset
                    this.ctx.globalAlpha = 0.2;
                    for (let i = 1; i <= radius; i += 2) {
                        this.ctx.drawImage(this.mainCanvas, i, 0, w, h);
                        this.ctx.drawImage(this.mainCanvas, -i, 0, w, h);
                    }
                    this.ctx.globalAlpha = 1.0;
                } else if (type === 'radial') {
                    // Radial Zoom Blur
                    const steps = Math.min(10, radius);
                    this.ctx.globalAlpha = 0.15;
                    for (let i = 0; i < steps; i++) {
                        const scale = 1 + (i * 0.01);
                        const nw = w * scale;
                        const nh = h * scale;
                        const nx = (w - nw) / 2;
                        const ny = (h - nh) / 2;
                        this.ctx.drawImage(this.mainCanvas, nx, ny, nw, nh);
                    }
                    this.ctx.globalAlpha = 1.0;
                }
            }

            // RGB & Luminance Dynamic Histogram Generator
            renderHistogram() {
                const hCtx = this.histogramCanvas.getContext('2d');
                const hw = this.histogramCanvas.width = this.histogramCanvas.parentElement.clientWidth;
                const hh = this.histogramCanvas.height = this.histogramCanvas.parentElement.clientHeight;

                hCtx.clearRect(0, 0, hw, hh);

                const imgData = this.ctx.getImageData(0, 0, this.mainCanvas.width, this.mainCanvas.height);
                const data = imgData.data;

                const rHist = new Array(256).fill(0);
                const gHist = new Array(256).fill(0);
                const bHist = new Array(256).fill(0);

                // Sample every 4th pixel for high performance
                for (let i = 0; i < data.length; i += 16) {
                    rHist[data[i]]++;
                    gHist[data[i + 1]]++;
                    bHist[data[i + 2]]++;
                }

                const max = Math.max(...rHist, ...gHist, ...bHist);

                // Draw Channel Graphs
                const drawChannel = (hist, color) => {
                    hCtx.fillStyle = color;
                    hCtx.beginPath();
                    hCtx.moveTo(0, hh);
                    for (let i = 0; i < 256; i++) {
                        const x = (i / 255) * hw;
                        const y = hh - ((hist[i] / max) * hh);
                        hCtx.lineTo(x, y);
                    }
                    hCtx.lineTo(hw, hh);
                    hCtx.closePath();
                    hCtx.fill();
                };

                hCtx.globalCompositeOperation = 'screen';
                drawChannel(rHist, 'rgba(239, 68, 68, 0.5)');
                drawChannel(gHist, 'rgba(34, 197, 94, 0.5)');
                drawChannel(bHist, 'rgba(59, 130, 246, 0.5)');
                hCtx.globalCompositeOperation = 'source-over';
            }

            // Presets Application
            applyPreset(preset) {
                this.resetAdjustments(false);
                this.adjustments.preset = preset.name;
                this.activePresetBadge.textContent = preset.name;

                Object.assign(this.adjustments, preset.adj);

                // Update UI Sliders
                Object.keys(preset.adj).forEach(key => {
                    const capitalized = key.charAt(0).toUpperCase() + key.slice(1);
                    const slider = document.getElementById(`adj${capitalized}`);
                    const valEl = document.getElementById(`val${capitalized}`);
                    if (slider && valEl) {
                        slider.value = preset.adj[key];
                        valEl.textContent = preset.adj[key];
                    }
                });

                this.applyProcessing();
                this.saveHistoryState();
                this.showToast(`Застосовано пресет: ${preset.name}`);
            }

            resetAdjustments(redraw = true) {
                this.adjustments = {
                    exposure: 0,
                    contrast: 0,
                    saturation: 0,
                    highlights: 0,
                    shadows: 0,
                    temp: 0,
                    vignette: 0,
                    sharpen: 0,
                    blurType: 'none',
                    blurRadius: 0,
                    preset: 'Оригінал'
                };

                this.activePresetBadge.textContent = 'Оригінал';

                const sliders = ['Exposure', 'Contrast', 'Saturation', 'Highlights', 'Shadows', 'Temp', 'Vignette', 'Sharpen'];
                sliders.forEach(adj => {
                    const el = document.getElementById(`adj${adj}`);
                    const valEl = document.getElementById(`val${adj}`);
                    if (el && valEl) {
                        el.value = 0;
                        valEl.textContent = '0';
                    }
                });

                document.getElementById('blurTypeSelect').value = 'none';
                document.getElementById('blurRadiusInput').value = 0;
                document.getElementById('valBlurRadius').textContent = '0 px';

                if (redraw) this.applyProcessing();
            }

            // Split View Controller
            updateSplitPosition(ratio) {
                const w = this.canvasWrapper.clientWidth;
                this.compareOverlay.style.width = `${ratio * 100}%`;
                this.splitHandle.style.left = `${ratio * 100}%`;
            }

            // Crop Utility
            applyCropRatio(ratioStr) {
                if (!this.originalImage) return;
                const [rw, rh] = ratioStr.split(':').map(Number);
                const targetRatio = rw / rh;

                let cw = this.mainCanvas.width;
                let ch = this.mainCanvas.height;

                if (cw / ch > targetRatio) {
                    cw = ch * targetRatio;
                } else {
                    ch = cw / targetRatio;
                }

                const tempCanvas = document.createElement('canvas');
                tempCanvas.width = cw;
                tempCanvas.height = ch;
                const tempCtx = tempCanvas.getContext('2d');
                
                tempCtx.drawImage(this.mainCanvas, (this.mainCanvas.width - cw) / 2, (this.mainCanvas.height - ch) / 2, cw, ch, 0, 0, cw, ch);

                this.mainCanvas.width = cw;
                this.mainCanvas.height = ch;
                this.ctx.drawImage(tempCanvas, 0, 0);

                this.saveHistoryState();
                this.showToast(`Кадровано під пропорцію ${ratioStr}`);
            }

            // Watermark Utility
            applyWatermark() {
                const text = document.getElementById('watermarkText').value.trim();
                if (!text || !this.originalImage) return;

                const opacity = parseInt(document.getElementById('watermarkOpacity').value) / 100;
                
                this.ctx.save();
                this.ctx.globalAlpha = opacity;
                this.ctx.font = `bold ${Math.max(16, Math.floor(this.mainCanvas.width * 0.03))}px Inter, sans-serif`;
                this.ctx.fillStyle = '#ffffff';
                this.ctx.shadowColor = 'rgba(0, 0, 0, 0.8)';
                this.ctx.shadowBlur = 6;
                this.ctx.shadowOffsetX = 2;
                this.ctx.shadowOffsetY = 2;

                const margin = this.mainCanvas.width * 0.04;
                this.ctx.fillText(text, margin, this.mainCanvas.height - margin);
                this.ctx.restore();

                this.saveHistoryState();
                this.showToast('Водяний знак додано');
            }

            // Undo / Redo System
            saveHistoryState() {
                if (!this.originalImage) return;
                if (this.historyIndex < this.history.length - 1) {
                    this.history = this.history.slice(0, this.historyIndex + 1);
                }
                if (this.history.length >= this.maxHistory) {
                    this.history.shift();
                }
                this.history.push(this.ctx.getImageData(0, 0, this.mainCanvas.width, this.mainCanvas.height));
                this.historyIndex = this.history.length - 1;
            }

            undo() {
                if (this.historyIndex > 0) {
                    this.historyIndex--;
                    this.ctx.putImageData(this.history[this.historyIndex], 0, 0);
                    this.renderHistogram();
                    this.showToast('Скасовано дію (Undo)');
                }
            }

            redo() {
                if (this.historyIndex < this.history.length - 1) {
                    this.historyIndex++;
                    this.ctx.putImageData(this.history[this.historyIndex], 0, 0);
                    this.renderHistogram();
                    this.showToast('Повторено дію (Redo)');
                }
            }

            // Copy & Paste Clipboard Utilities
            copyToClipboard() {
                if (!this.originalImage) return;
                this.mainCanvas.toBlob((blob) => {
                    try {
                        const item = new ClipboardItem({ 'image/png': blob });
                        navigator.clipboard.write([item]);
                        this.showToast('Скопійовано у буфер обміну');
                    } catch (err) {
                        this.showToast('Помилка копіювання у буфер', 'error');
                    }
                });
            }

            pasteFromClipboard() {
                navigator.clipboard.read().then(items => {
                    for (const item of items) {
                        for (const type of item.types) {
                            if (type.startsWith('image/')) {
                                item.getType(type).then(blob => {
                                    this.loadImageFile(blob);
                                });
                                return;
                            }
                        }
                    }
                }).catch(() => {
                    this.showToast('Надайте доступ до буферу або вставити не вдалося', 'error');
                });
            }

            // Export Modal Engine
            openExportModal() {
                if (!this.originalImage) {
                    this.showToast('Спочатку відкрийте зображення!', 'error');
                    return;
                }
                document.getElementById('exportModal').classList.remove('hidden');
            }

            closeExportModal() {
                document.getElementById('exportModal').classList.add('hidden');
            }

            downloadExportedImage() {
                const format = document.getElementById('exportFormat').value;
                const quality = parseInt(document.getElementById('exportQuality').value) / 100;
                const ext = format.split('/')[1];

                const link = document.createElement('a');
                link.download = `photo_edited_${Date.now()}.${ext}`;
                link.href = this.mainCanvas.toDataURL(format, quality);
                link.click();

                this.closeExportModal();
                this.showToast('Зображення успішно експортовано');
            }

            // Toast Notifications
            showToast(msg, type = 'info') {
                const container = document.getElementById('toastContainer');
                const toast = document.createElement('div');
                toast.className = `px-4 py-2.5 rounded-xl text-xs font-medium border flex items-center space-x-2 shadow-xl transition-all duration-300 transform translate-y-2 opacity-0 pointer-events-auto ${
                    type === 'error' ? 'bg-red-950/90 border-red-500/30 text-red-200' : 'bg-slate-900/90 border-slate-700 text-slate-200'
                }`;
                toast.innerHTML = `
                    <i class="fa-solid ${type === 'error' ? 'fa-triangle-exclamation text-red-400' : 'fa-circle-check text-indigo-400'}"></i>
                    <span>${msg}</span>
                `;
                container.appendChild(toast);

                setTimeout(() => toast.classList.remove('translate-y-2', 'opacity-0'), 10);
                setTimeout(() => {
                    toast.classList.add('opacity-0', 'translate-y-2');
                    setTimeout(() => toast.remove(), 300);
                }, 2500);
            }
        }

        // Initialize Photo Studio
        window.addEventListener('load', () => {
            window.photoStudio = new PhotoStudio();
        });
    </script>
</body>
</html>
