<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NAA_Clock</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --time-color: #FFFFFF;
            --date-color: #a3d5ff;
            --week-color: #a3d5ff;
            --slogan-color: #a3d5ff;
            --bg-base: #12121e;
            --menu-bg: rgba(25, 30, 45, 0.95);
            --menu-border: rgba(100, 150, 255, 0.3);
            --shadow: 0 6px 15px rgba(0, 0, 0, 0.5);
            --bg-opacity: 1;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', 'Arial', sans-serif;
        }
        
        body {
            background: var(--bg-base);
            background-image: var(--bg-image, none);
            background-size: cover;
            background-position: center;
            height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            transition: background 0.5s ease;
            position: relative;
        }
        
        body::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, calc(1 - var(--bg-opacity)));
            z-index: 0;
            transition: background 0.3s ease;
        }
        
        .container {
            width: 100%;
            max-width: 1200px;
            text-align: center;
            padding: 0 20px;
            position: relative;
            z-index: 2;
        }
        
        #time-display {
            color: var(--time-color);
            font-size: clamp(4rem, 15vw, 15rem);
            font-weight: 700;
            letter-spacing: 0.02em;
            line-height: 1;
            text-align: center;
            margin: 1rem 0;
            font-family: 'Arial', sans-serif;
            text-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
        }
        
        #date-week-container {
            position: absolute;
            bottom: 20px;
            left: 20px;
            display: flex;
            flex-direction: row;
            gap: 1rem;
            font-size: clamp(0.8rem, 2vw, 1.2rem);
            color: var(--date-color);
            opacity: 0.9;
            z-index: 10;
            background: rgba(0, 0, 0, 0.3);
            padding: 0.5rem 1rem;
            border-radius: 20px;
        }
        
        #slogan-container {
            position: absolute;
            bottom: 20px;
            right: 20px;
            font-size: clamp(0.8rem, 2vw, 1.2rem);
            color: var(--slogan-color);
            opacity: 0.9;
            z-index: 10;
            background: rgba(0, 0, 0, 0.3);
            padding: 0.5rem 1rem;
            border-radius: 20px;
            max-width: 40%;
            text-align: right;
        }
        
        #portrait-warning {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 10000;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            color: white;
            font-size: 1.5rem;
            text-align: center;
            padding: 20px;
        }
        
        #portrait-warning i {
            font-size: 3rem;
            margin-bottom: 20px;
            animation: rotate 2s linear infinite;
        }
        
        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }
        
        @media (orientation: portrait) {
            #portrait-warning {
                display: flex;
            }
        }
        
        #menu-button {
            position: absolute;
            top: 2.5rem;
            left: 1.5rem;
            background: transparent;
            border: none;
            cursor: pointer;
            z-index: 100;
            width: 40px;
            height: 40px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            transition: all 0.3s;
            border-radius: 50%;
        }
        
        #menu-button:hover {
            transform: scale(1.1);
            background: rgba(255, 255, 255, 0.2);
        }
        
        .menu-icon-bar {
            display: block;
            width: 20px;
            height: 2px;
            margin: 2px 0;
            background-color: rgba(255, 255, 255, 0.9);
            border-radius: 2px;
            transition: all 0.3s ease;
        }
        
        .menu-panel {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: var(--menu-bg);
            color: #fff;
            border-radius: 10px;
            padding: 16px;
            z-index: 1000;
            box-shadow: var(--shadow);
            width: 90%;
            max-width: 320px;
            backdrop-filter: blur(10px);
            display: none;
            border: 1px solid var(--menu-border);
            animation: fadeIn 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
        }
        
        @keyframes fadeIn {
            from { 
                opacity: 0; 
                transform: translate(-50%, -50%) scale(0.9);
            }
            to { 
                opacity: 1; 
                transform: translate(-50%, -50%) scale(1);
            }
        }
        
        .menu-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 14px;
            padding-bottom: 8px;
            border-bottom: 1px solid var(--menu-border);
        }
        
        .menu-title {
            font-size: 1.1rem;
            color: #fff;
            font-weight: 500;
            text-align: center;
            flex: 1;
        }
        
        .close-button {
            background: none;
            border: none;
            color: #aaa;
            font-size: 1.2rem;
            cursor: pointer;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: background 0.2s;
        }
        
        .close-button:hover {
            background: rgba(255, 255, 255, 0.1);
        }
        
        .menu-content {
            padding: 5px 0;
            max-height: 70vh;
            overflow-y: auto;
        }
        
        .menu-section {
            margin-bottom: 14px;
        }
        
        .section-title {
            font-size: 0.95rem;
            color: #a3d5ff;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .menu-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 8px;
        }
        
        .menu-option {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 14px;
            background: rgba(40, 50, 75, 0.7);
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .menu-option:hover {
            background: rgba(50, 60, 90, 0.8);
            transform: translateY(-2px);
        }
        
        .option-title {
            display: flex;
            align-items: center;
            gap: 12px;
            font-size: 0.95rem;
            color: #e6f7ff;
        }
        
        .option-icon {
            font-size: 1.1rem;
            width: 24px;
            height: 24px;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 6px;
        }
        
        .switch-container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 14px;
            background: rgba(40, 50, 75, 0.7);
            border-radius: 8px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .switch-label {
            font-size: 0.95rem;
            color: #e6f7ff;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .switch {
            position: relative;
            display: inline-block;
            width: 48px;
            height: 24px;
        }
        
        .switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }
        
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: #555;
            transition: .4s;
            border-radius: 24px;
        }
        
        .slider:before {
            position: absolute;
            content: "";
            height: 18px;
            width: 18px;
            left: 3px;
            bottom: 3px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }
        
        input:checked + .slider {
            background-color: #4a80f0;
        }
        
        input:checked + .slider:before {
            transform: translateX(24px);
        }
        
        .font-select-container {
            padding: 12px 14px;
            background: rgba(40, 50, 75, 0.7);
            border-radius: 8px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .font-select-label {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 0.95rem;
            color: #e6f7ff;
            margin-bottom: 8px;
        }
        
        .font-select {
            width: 100%;
            padding: 8px 12px;
            background: rgba(0, 0, 0, 0.3);
            color: #fff;
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 6px;
            appearance: none;
            background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 12 12'%3E%3Cpath fill='%23a3d5ff' d='M6 8.5L2.5 5l.7-.7L6 7.1l2.8-2.8.7.7z'/%3E%3C/svg%3E");
            background-repeat: no-repeat;
            background-position: right 10px center;
            background-size: 12px;
            cursor: pointer;
        }
        
        .color-picker-panel {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        
        .color-picker-area {
            width: 100%;
            height: 120px;
            border-radius: 6px;
            margin: 6px 0;
            position: relative;
            overflow: hidden;
            background: #12121e;
            cursor: crosshair;
        }
        
        .color-rectangle {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(to right, 
                rgba(255,0,0,1), rgba(255,255,0,1), 
                rgba(0,255,0,1), rgba(0,255,255,1), 
                rgba(0,0,255,1), rgba(255,0,255,1), 
                rgba(255,0,0,1));
        }
        
        .color-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(to top, rgba(0,0,0,1), rgba(0,0,0,0));
        }
        
        .color-pointer {
            position: absolute;
            width: 12px;
            height: 12px;
            border: 2px solid white;
            border-radius: 50%;
            transform: translate(-6px, -6px);
            pointer-events: none;
            box-shadow: 0 0 2px rgba(0,0,0,0.5);
        }
        
        .color-controls {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        
        .current-color-row {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 8px;
        }
        
        .current-color-title {
            font-size: 0.85rem;
        }
        
        .current-color {
            width: 36px;
            height: 36px;
            border-radius: 6px;
            border: 1px solid rgba(255,255,255,0.2);
        }
        
        .action-button {
            padding: 10px 14px;
            border-radius: 8px;
            border: none;
            font-size: 0.95rem;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-weight: 500;
            margin-top: 8px;
        }
        
        .reset-button {
            background: rgba(255, 255, 255, 0.1);
            color: white;
        }
        
        .confirm-button {
            background: #4a80f0;
            color: white;
        }
        
        .file-button {
            background: #6b5b95;
            color: white;
        }
        
        .bg-option {
            display: flex;
            flex-direction: column;
            gap: 12px;
            padding: 14px;
            background: rgba(40, 50, 75, 0.7);
            border-radius: 8px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .bg-preview {
            width: 100%;
            height: 120px;
            border-radius: 8px;
            overflow: hidden;
            background: var(--bg-base);
            display: flex;
            align-items: center;
            justify-content: center;
            color: #aaa;
            font-size: 0.9rem;
            position: relative;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .bg-preview img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .bg-placeholder {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.2);
            font-size: 0.9rem;
        }
        
        .opacity-control {
            padding: 14px;
            background: rgba(40, 50, 75, 0.7);
            border-radius: 8px;
            margin-top: 12px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .opacity-label {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.95rem;
            color: #e6f7ff;
            margin-bottom: 10px;
        }
        
        .opacity-value {
            font-size: 0.95rem;
            min-width: 40px;
            text-align: right;
            background: rgba(0, 0, 0, 0.2);
            padding: 4px 8px;
            border-radius: 4px;
        }
        
        .opacity-slider {
            width: 100%;
            height: 8px;
            border-radius: 4px;
            outline: none;
            appearance: none;
            background: linear-gradient(to right, rgba(100, 150, 255, 0.3), rgba(100, 150, 255, 0.8));
        }
        
        .opacity-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 22px;
            height: 22px;
            border-radius: 50%;
            background: #4a80f0;
            cursor: pointer;
            box-shadow: 0 0 6px rgba(0,0,0,0.5);
            border: 2px solid #fff;
        }
        
        .menu-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 999;
            display: none;
        }
        
        .menu-panel::-webkit-scrollbar {
            display: none;
        }
        
        #main-menu { z-index: 1000; }
        #display-settings, #color-settings, #bg-settings { z-index: 1001; }
        #color-picker, #bg-picker, #slogan-picker { z-index: 1002; }
        
        /* 新增标语输入样式 */
        .slogan-input {
            width: 100%;
            padding: 12px;
            margin-bottom: 15px;
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            color: white;
            font-size: 1rem;
        }
        
        .action-buttons {
            display: flex;
            gap: 10px;
            justify-content: flex-end;
        }
    </style>
</head>
<body>
    <!-- 竖屏提示 -->
    <div id="portrait-warning">
        <i class="fas fa-sync-alt"></i>
        <p>请将设备旋转至横屏<br>以获得更佳体验</p>
    </div>
    
    <div class="container">
        <div id="time-display">00:00:00</div>
    </div>
    
    <div id="date-week-container">
        <div id="date-display">2023/06/15</div>
        <div id="week-display">Sunday</div>
    </div>
    
    <!-- 标语容器 -->
    <div id="slogan-container">NAA_Clock</div>
    
    <button id="menu-button" aria-label="菜单">
        <span class="menu-icon-bar"></span>
        <span class="menu-icon-bar"></span>
        <span class="menu-icon-bar"></span>
    </button>
    
    <!-- 主菜单 - 一级弹窗 -->
    <div id="main-menu" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">时钟设置</h2>
            <button class="close-button" id="close-main-menu">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="menu-grid">
                <div class="menu-option" id="open-display-settings">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-calendar-alt"></i></span>
                        <span>显示设置</span>
                    </div>
                </div>
                
                <div class="menu-option" id="open-color-settings">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-palette"></i></span>
                        <span>颜色设置</span>
                    </div>
                </div>
                
                <div class="menu-option" id="open-bg-settings">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-image"></i></span>
                        <span>背景设置</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- 显示设置 - 二级弹窗 -->
    <div id="display-settings" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">显示设置</h2>
            <button class="close-button" id="close-display-settings">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="menu-section">
                <div class="menu-grid">
                    <div class="font-select-container">
                        <label class="font-select-label">
                            <i class="fas fa-font"></i>
                            时间字体
                        </label>
                        <select class="font-select" id="time-font">
                            <option value="Arial, sans-serif">Arial</option>
                            <option value="'Microsoft YaHei', sans-serif">微软雅黑</option>
                            <option value="'SimHei', sans-serif">黑体</option>
                            <option value="'SimSun', serif">宋体</option>
                            <option value="'Courier New', monospace">等宽字体</option>
                            <option value="'Times New Roman', serif">衬线字体</option>
                        </select>
                    </div>
                    
                    <div class="switch-container">
                        <label class="switch-label">
                            <i class="fas fa-calendar-day"></i>
                            显示日期
                        </label>
                        <label class="switch">
                            <input type="checkbox" id="show-date" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <div class="switch-container">
                        <label class="switch-label">
                            <i class="fas fa-calendar"></i>
                            显示年份
                        </label>
                        <label class="switch">
                            <input type="checkbox" id="show-year" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <div class="switch-container">
                        <label class="switch-label">
                            <i class="fas fa-calendar-week"></i>
                            显示星期
                        </label>
                        <label class="switch">
                            <input type="checkbox" id="show-week" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <div class="switch-container">
                        <label class="switch-label">
                            <i class="fas fa-calendar"></i>
                            显示月日
                        </label>
                        <label class="switch">
                            <input type="checkbox" id="show-month-day" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <div class="switch-container">
                        <label class="switch-label">
                            <i class="fas fa-quote-left"></i>
                            显示标语
                        </label>
                        <label class="switch">
                            <input type="checkbox" id="show-slogan" checked>
                            <span class="slider"></span>
                        </label>
                    </div>
                    
                    <button class="action-button file-button" id="open-slogan-picker" style="width: 100%;">
                        <i class="fas fa-edit"></i> 编辑标语
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- 颜色设置 - 二级弹窗 -->
    <div id="color-settings" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">颜色设置</h2>
            <button class="close-button" id="close-color-settings">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="menu-grid">
                <div class="menu-option color-option" data-target="time">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-clock"></i></span>
                        <span>时间颜色</span>
                    </div>
                    <div class="color-preview" style="width: 16px; height: 16px; background: #ffffff; border-radius: 50%;"></div>
                </div>
                
                <div class="menu-option color-option" data-target="date">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-calendar-day"></i></span>
                        <span>日期颜色</span>
                    </div>
                    <div class="color-preview" style="width: 16px; height: 16px; background: #a3d5ff; border-radius: 50%;"></div>
                </div>
                
                <div class="menu-option color-option" data-target="week">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-calendar-week"></i></span>
                        <span>星期颜色</span>
                    </div>
                    <div class="color-preview" style="width: 16px; height: 16px; background: #a3d5ff; border-radius: 50%;"></div>
                </div>
                
                <div class="menu-option color-option" data-target="slogan">
                    <div class="option-title">
                        <span class="option-icon"><i class="fas fa-font"></i></span>
                        <span>标语颜色</span>
                    </div>
                    <div class="color-preview" style="width: 16px; height: 16px; background: #a3d5ff; border-radius: 50%;"></div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- 背景设置 - 二级弹窗 -->
    <div id="bg-settings" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">背景设置</h2>
            <button class="close-button" id="close-bg-settings">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="menu-section">
                <div class="bg-option">
                    <div class="bg-preview" id="bg-preview">
                        <div class="bg-placeholder">背景预览</div>
                    </div>
                    <button class="action-button file-button" id="open-bg-picker">
                        <i class="fas fa-image"></i> 选择图片
                    </button>
                </div>
            </div>
            
            <div class="menu-section opacity-control">
                <div class="opacity-label">
                    <span><i class="fas fa-adjust"></i> 背景透明度</span>
                    <span id="opacity-value" class="opacity-value">100%</span>
                </div>
                <input type="range" min="0" max="1" step="0.01" value="1" class="opacity-slider" id="opacity-slider">
            </div>
        </div>
    </div>
    
    <!-- 颜色选择器 - 三级弹窗 -->
    <div id="color-picker" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">选择颜色</h2>
            <button class="close-button" id="close-color-picker">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="color-picker-panel">
                <div class="color-picker-area" id="color-area">
                    <div class="color-rectangle"></div>
                    <div class="color-overlay"></div>
                    <div class="color-pointer" id="color-pointer"></div>
                </div>
                
                <div class="color-controls">
                    <div class="current-color-row">
                        <span class="current-color-title">当前颜色:</span>
                        <div class="current-color" id="current-color"></div>
                        <div>
                            <button class="action-button reset-button" id="reset-color">
                                <i class="fas fa-undo"></i> 重置
                            </button>
                            <button class="action-button confirm-button" id="confirm-color">
                                <i class="fas fa-check"></i> 应用
                            </button>
                        </div>
                    </div>
                    
                    <div class="color-values">
                        <div class="color-component">
                            <div class="color-label">
                                <span>H (色调)</span>
                                <span class="color-value" id="h-value">182</span>
                            </div>
                            <div class="color-slider-container">
                                <input type="range" min="0" max="360" value="182" class="color-slider h-slider" id="h-slider">
                            </div>
                        </div>
                        
                        <div class="color-component">
                            <div class="color-label">
                                <span>S (饱和度)</span>
                                <span class="color-value" id="s-value">48</span>
                            </div>
                            <div class="color-slider-container">
                                <input type="range" min="0" max="100" value="48" class="color-slider s-v-slider" id="s-slider">
                            </div>
                        </div>
                        
                        <div class="color-component">
                            <div class="color-label">
                                <span>V (亮度)</span>
                                <span class="color-value" id="v-value">47</span>
                            </div>
                            <div class="color-slider-container">
                                <input type="range" min="0", max="100" value="47" class="color-slider s-v-slider v-slider" id="v-slider">
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- 背景选择器 - 三级弹窗 -->
    <div id="bg-picker" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">自定义背景</h2>
            <button class="close-button" id="close-bg-picker">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="bg-option" style="margin-bottom: 12px;">
                <div class="bg-preview" id="large-bg-preview">
                    <div class="bg-placeholder">选择图片后将显示在这里</div>
                </div>
            </div>
            
            <div class="menu-section">
                <label for="bg-file" class="action-button file-button" style="width: 100%; justify-content: center;">
                    <i class="fas fa-folder-open"></i> 从设备选择图片
                </label>
                <input type="file" id="bg-file" accept="image/*" style="display: none;">
            </div>
            
            <div class="menu-section" style="display: flex; gap: 8px;">
                <button class="action-button reset-button" id="reset-bg">
                    <i class="fas fa-undo"></i> 重置背景
                </button>
                <button class="action-button confirm-button" id="apply-bg">
                    <i class="fas fa-check"></i> 应用背景
                </button>
            </div>
        </div>
    </div>

    <!-- 标语设置弹窗 - 三级弹窗 -->
    <div id="slogan-picker" class="menu-panel">
        <div class="menu-header">
            <h2 class="menu-title">自定义标语</h2>
            <button class="close-button" id="close-slogan-picker">&times;</button>
        </div>
        
        <div class="menu-content">
            <div class="menu-section">
                <input type="text" id="slogan-input" placeholder="输入标语内容" 
                       value="NAA_Clock" class="slogan-input">
                <div class="action-buttons">
                    <button class="action-button reset-button" id="reset-slogan">
                        <i class="fas fa-undo"></i> 重置
                    </button>
                    <button class="action-button confirm-button" id="confirm-slogan">
                        <i class="fas fa-check"></i> 应用
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 图片大小限制提示 -->
    <div id="file-too-large" class="menu-panel" style="max-width: 240px; padding: 20px; text-align: center; display: none;">
        <div class="menu-header" style="border-bottom: none; padding-bottom: 0; margin-bottom: 15px;">
            <i class="fas fa-exclamation-triangle" style="color: #ff9800; font-size: 1.5rem; margin-right: 10px;"></i>
            <h2 class="menu-title" style="flex: none;">错误</h2>
        </div>
        <p style="color: #ffcccc; margin-bottom: 20px;">图片大小不能超过1MB，请选择更小的图片。</p>
        <button class="action-button confirm-button" style="width: 100%;" id="close-file-error">
            <i class="fas fa-times"></i> 关闭
        </button>
    </div>

    <!-- 点击外部关闭的遮罩层 -->
    <div class="menu-overlay" id="menu-overlay"></div>

    <script>
        // DOM元素引用
        const timeDisplay = document.getElementById('time-display');
        const dateDisplay = document.getElementById('date-display');
        const weekDisplay = document.getElementById('week-display');
        const sloganDisplay = document.getElementById('slogan-container');
        const dateWeekContainer = document.getElementById('date-week-container');
        const menuButton = document.getElementById('menu-button');
        const menuOverlay = document.getElementById('menu-overlay');
        const bgPreview = document.getElementById('bg-preview');
        const largeBgPreview = document.getElementById('large-bg-preview');
        const opacitySlider = document.getElementById('opacity-slider');
        const opacityValue = document.getElementById('opacity-value');
        const timeFontSelect = document.getElementById('time-font');
        
        const showDateCheckbox = document.getElementById('show-date');
        const showYearCheckbox = document.getElementById('show-year');
        const showWeekCheckbox = document.getElementById('show-week');
        const showMonthDayCheckbox = document.getElementById('show-month-day');
        const showSloganCheckbox = document.getElementById('show-slogan');
        
        const colorArea = document.getElementById('color-area');
        const colorPointer = document.getElementById('color-pointer');
        const currentColor = document.getElementById('current-color');
        const hSlider = document.getElementById('h-slider');
        const sSlider = document.getElementById('s-slider');
        const vSlider = document.getElementById('v-slider');
        const hValue = document.getElementById('h-value');
        const sValue = document.getElementById('s-value');
        const vValue = document.getElementById('v-value');
        const resetColorButton = document.getElementById('reset-color');
        const confirmColorButton = document.getElementById('confirm-color');
        
        const bgFileInput = document.getElementById('bg-file');
        const resetBgButton = document.getElementById('reset-bg');
        const applyBgButton = document.getElementById('apply-bg');
        const fileTooLarge = document.getElementById('file-too-large');
        const closeFileError = document.getElementById('close-file-error');
        
        // 新增标语相关元素
        const sloganInput = document.getElementById('slogan-input');
        const resetSloganBtn = document.getElementById('reset-slogan');
        const confirmSloganBtn = document.getElementById('confirm-slogan');
        const openSloganPicker = document.getElementById('open-slogan-picker');
        const closeSloganPicker = document.getElementById('close-slogan-picker');
        const sloganPicker = document.getElementById('slogan-picker');
        
        const weekDays = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
        
        const colors = {
            time: "#FFFFFF",
            date: "#a3d5ff",
            week: "#a3d5ff",
            slogan: "#a3d5ff",
            background: "#12121e"
        };
        
        // 字体设置
        const defaultFont = 'Arial, sans-serif';
        let currentFont = defaultFont;
        
        let currentColorTarget = null;
        let isDragging = false;
        let targetInitialColor = null;
        let activeMenu = null;
        let selectedBgImage = null;
        let sloganText = "NAA_clock"; // 默认标语
        
        // 菜单元素
        const mainMenu = document.getElementById('main-menu');
        const displaySettings = document.getElementById('display-settings');
        const colorSettings = document.getElementById('color-settings');
        const bgSettings = document.getElementById('bg-settings');
        const colorPicker = document.getElementById('color-picker');
        const bgPicker = document.getElementById('bg-picker');
        
        // 菜单按钮
        const openDisplaySettings = document.getElementById('open-display-settings');
        const openColorSettings = document.getElementById('open-color-settings');
        const openBgSettings = document.getElementById('open-bg-settings');
        const openBgPicker = document.getElementById('open-bg-picker');
        
        // 关闭按钮
        const closeMainMenu = document.getElementById('close-main-menu');
        const closeDisplaySettings = document.getElementById('close-display-settings');
        const closeColorSettings = document.getElementById('close-color-settings');
        const closeBgSettings = document.getElementById('close-bg-settings');
        const closeColorPicker = document.getElementById('close-color-picker');
        const closeBgPicker = document.getElementById('close-bg-picker');

        // 更新时间函数
        function updateClock() {
            const now = new Date();
            const hours = String(now.getHours()).padStart(2, '0');
            const minutes = String(now.getMinutes()).padStart(2, '0');
            const seconds = String(now.getSeconds()).padStart(2, '0');
            timeDisplay.textContent = `${hours}:${minutes}:${seconds}`;
            
            const year = now.getFullYear();
            const month = String(now.getMonth() + 1).padStart(2, '0');
            const day = String(now.getDate()).padStart(2, '0');
            const weekDay = weekDays[now.getDay()];
            
            let dateParts = [];
            if (showYearCheckbox.checked) dateParts.push(year);
            if (showMonthDayCheckbox.checked) dateParts.push(`${month}/${day}`);
            
            dateDisplay.textContent = dateParts.join('/');
            weekDisplay.textContent = weekDay;
            
            dateDisplay.style.display = showDateCheckbox.checked ? 'block' : 'none';
            weekDisplay.style.display = showWeekCheckbox.checked ? 'block' : 'none';
            
            if (!showDateCheckbox.checked && !showWeekCheckbox.checked) {
                dateWeekContainer.style.display = 'none';
            } else {
                dateWeekContainer.style.display = 'flex';
            }
            
            // 更新标语显示
            sloganDisplay.textContent = sloganText;
            sloganDisplay.style.display = showSloganCheckbox.checked ? 'block' : 'none';
            
            applyColorSettings();
        }
        
        // 应用颜色和字体设置
        function applyColorSettings() {
            timeDisplay.style.color = colors.time;
            timeDisplay.style.fontFamily = currentFont;
            dateDisplay.style.color = colors.date;
            weekDisplay.style.color = colors.week;
            sloganDisplay.style.color = colors.slogan;
            
            // 应用背景
            if (selectedBgImage) {
                document.body.style.setProperty('--bg-image', `url(${selectedBgImage})`);
            } else {
                document.body.style.setProperty('--bg-image', 'none');
            }
            
            // 应用透明度
            updateOpacityValueText();
        }
        
        // 更新透明度值显示文本
        function updateOpacityValueText() {
            const opacity = opacitySlider.value * 100;
            opacityValue.textContent = `${Math.round(opacity)}%`;
        }
        
        // 显示/隐藏菜单
        function showMenu(menu) {
            activeMenu = menu;
            menu.style.display = 'block';
            menuOverlay.style.display = 'block';
            void menu.offsetWidth; // 触发重绘以激活动画
            menu.classList.add('active');
        }
        
        function hideMenu(menu) {
            if (!menu) return;
            menu.classList.remove('active');
            setTimeout(() => { 
                menu.style.display = 'none';
                menuOverlay.style.display = 'none';
                activeMenu = null;
            }, 200);
        }
        
        // 显示文件过大错误
        function showFileError() {
            showMenu(fileTooLarge);
        }
        
        // 隐藏文件过大错误
        function hideFileError() {
            hideMenu(fileTooLarge);
        }
        
        // 颜色转换函数
        function hsvToHex(h, s, v) {
            h = parseInt(h);
            s = parseInt(s) / 100;
            v = parseInt(v) / 100;
            
            let r, g, b, i, f, p, q, t;
            i = Math.floor(h / 60);
            f = h / 60 - i;
            p = v * (1 - s);
            q = v * (1 - f * s);
            t = v * (1 - (1 - f) * s);
            
            switch (i % 6) {
                case 0: r = v, g = t, b = p; break;
                case 1: r = q, g = v, b = p; break;
                case 2: r = p, g = v, b = t; break;
                case 3: r = p, g = q, b = v; break;
                case 4: r = t, g = p, b = v; break;
                case 5: r = v, g = p, b = q; break;
            }
            
            const toHex = x => {
                const hex = Math.round(x * 255).toString(16);
                return hex.length === 1 ? '0' + hex : hex;
            };
            
            return `#${toHex(r)}${toHex(g)}${toHex(b)}`;
        }
        
        function hexToHsv(hex) {
            const r = parseInt(hex.slice(1, 3), 16);
            const g = parseInt(hex.slice(3, 5), 16);
            const b = parseInt(hex.slice(5, 7), 16);
            
            const rNorm = r / 255;
            const gNorm = g / 255;
            const bNorm = b / 255;
            
            const cmax = Math.max(rNorm, gNorm, bNorm);
            const cmin = Math.min(rNorm, gNorm, bNorm);
            const delta = cmax - cmin;
            
            let h = 0;
            let s = 0;
            let v = cmax;
            
            if (delta !== 0) {
                s = delta / cmax;
                
                switch (cmax) {
                    case rNorm: h = ((gNorm - bNorm) / delta) % 6; break;
                    case gNorm: h = (bNorm - rNorm) / delta + 2; break;
                    case bNorm: h = (rNorm - gNorm) / delta + 4; break;
                }
                
                h = Math.round(h * 60);
                if (h < 0) h += 360;
            }
            
            s = Math.round(s * 100);
            v = Math.round(v * 100);
            
            return { h, s, v };
        }
        
        // 初始化颜色选择器
        function initColorPicker() {
            if (currentColorTarget) {
                let initialColor = "#FFFFFF";
                if (currentColorTarget === 'time') initialColor = colors.time;
                if (currentColorTarget === 'date') initialColor = colors.date;
                if (currentColorTarget === 'week') initialColor = colors.week;
                if (currentColorTarget === 'slogan') initialColor = colors.slogan;
                
                targetInitialColor = initialColor;
                
                const { h, s, v } = hexToHsv(initialColor);
                hSlider.value = h;
                sSlider.value = s;
                vSlider.value = v;
                hValue.textContent = h;
                sValue.textContent = s;
                vValue.textContent = v;
                
                updateColorPointerPosition(h, s);
                updateColorPreview(h, s, v);
            }
        }
        
        // 更新颜色指针位置
        function updateColorPointerPosition(h, s) {
            const rect = colorArea.getBoundingClientRect();
            const x = (h / 360) * rect.width;
            const y = ((100 - s) / 100) * rect.height;
            colorPointer.style.left = `${x}px`;
            colorPointer.style.top = `${y}px`;
        }
        
        // 更新颜色预览
        function updateColorPreview(h, s, v) {
            const color = hsvToHex(h, s, v);
            currentColor.style.backgroundColor = color;
        }
        
        // 处理颜色区域交互
        function handleColorInteraction(x, y) {
            const rect = colorArea.getBoundingClientRect();
            x = Math.max(0, Math.min(rect.width, x - rect.left));
            y = Math.max(0, Math.min(rect.height, y - rect.top));
            
            const h = Math.floor((x / rect.width) * 360);
            const s = Math.floor(100 - (y / rect.height) * 100);
            
            hSlider.value = h;
            sSlider.value = s;
            hValue.textContent = h;
            sValue.textContent = s;
            
            updateColorPointerPosition(h, s);
            updateColorPreview(h, s, vSlider.value);
        }
        
        // 更新背景预览
        function updateBgPreview(src) {
            // 清除占位符和之前的图片
            if (largeBgPreview.querySelector('.bg-placeholder')) {
                largeBgPreview.querySelector('.bg-placeholder').remove();
            }
            if (largeBgPreview.querySelector('img')) {
                largeBgPreview.querySelector('img').remove();
            }
            
            // 清除主菜单中的小预览
            if (bgPreview.querySelector('.bg-placeholder')) {
                bgPreview.querySelector('.bg-placeholder').remove();
            }
            if (bgPreview.querySelector('img')) {
                bgPreview.querySelector('img').remove();
            }
            
            // 创建新的预览
            if (src) {
                // 更新大预览
                const largeImg = document.createElement('img');
                largeImg.src = src;
                largeImg.alt = '背景预览';
                largeBgPreview.appendChild(largeImg);
                
                // 更新主菜单中的小预览
                const smallImg = document.createElement('img');
                smallImg.src = src;
                smallImg.alt = '背景预览';
                bgPreview.appendChild(smallImg);
            } else {
                // 没有图片时显示占位符
                const largePlaceholder = document.createElement('div');
                largePlaceholder.className = 'bg-placeholder';
                largePlaceholder.textContent = '背景预览';
                largeBgPreview.appendChild(largePlaceholder);
                
                const smallPlaceholder = document.createElement('div');
                smallPlaceholder.className = 'bg-placeholder';
                smallPlaceholder.textContent = '背景预览';
                bgPreview.appendChild(smallPlaceholder);
            }
        }
        
        // 重置背景
        function resetBackground() {
            selectedBgImage = null;
            updateBgPreview('');
            opacitySlider.value = 1;
            updateOpacityValueText();
            document.documentElement.style.setProperty('--bg-opacity', 1);
            applyColorSettings();
        }
        
        // 初始化
        updateClock();
        updateOpacityValueText();
        currentFont = defaultFont;
        timeFontSelect.value = defaultFont;
        
        // 菜单导航系统
        // 打开二级菜单
        openDisplaySettings.addEventListener('click', () => showMenu(displaySettings));
        openColorSettings.addEventListener('click', () => showMenu(colorSettings));
        openBgSettings.addEventListener('click', () => showMenu(bgSettings));
        openBgPicker.addEventListener('click', () => showMenu(bgPicker));
        
        // 关闭菜单
        closeMainMenu.addEventListener('click', () => hideMenu(mainMenu));
        closeDisplaySettings.addEventListener('click', () => hideMenu(displaySettings));
        closeColorSettings.addEventListener('click', () => hideMenu(colorSettings));
        closeBgSettings.addEventListener('click', () => hideMenu(bgSettings));
        closeColorPicker.addEventListener('click', () => hideMenu(colorPicker));
        closeBgPicker.addEventListener('click', () => hideMenu(bgPicker));
        closeFileError.addEventListener('click', hideFileError);
        
        // 主菜单按钮
        menuButton.addEventListener('click', () => showMenu(mainMenu));
        
        // 点击外部关闭菜单
        menuOverlay.addEventListener('click', () => {
            hideMenu(activeMenu);
        });
        
        // 颜色选项点击 - 打开颜色选择器
        document.querySelectorAll('.color-option').forEach(option => {
            option.addEventListener('click', () => {
                currentColorTarget = option.dataset.target;
                showMenu(colorPicker);
                initColorPicker();
            });
        });
        
        // 标语设置相关事件
        openSloganPicker.addEventListener('click', () => {
            sloganInput.value = sloganText;
            showMenu(sloganPicker);
        });
        
        closeSloganPicker.addEventListener('click', () => hideMenu(sloganPicker));
        
        resetSloganBtn.addEventListener('click', () => {
            sloganInput.value = "NAA_clock";
        });
        
        confirmSloganBtn.addEventListener('click', () => {
            sloganText = sloganInput.value || "NAA_Clock";
            updateClock();
            hideMenu(sloganPicker);
        });
        
        // 背景文件选择 - 添加大小限制
        bgFileInput.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                // 检查文件大小是否超过1MB (1024*1024字节)
                if (file.size > 1024 * 1024) {
                    showFileError();
                    bgFileInput.value = ''; // 清除文件选择
                    return;
                }
                
                const reader = new FileReader();
                reader.onload = (event) => {
                    selectedBgImage = event.target.result;
                    updateBgPreview(selectedBgImage);
                };
                reader.readAsDataURL(file);
            }
        });
        
        // 重置背景
        resetBgButton.addEventListener('click', resetBackground);
        
        // 应用背景
        applyBgButton.addEventListener('click', () => {
            hideMenu(bgPicker);
            applyColorSettings();
        });
        
        // 透明度调整
        opacitySlider.addEventListener('input', function() {
            const opacity = this.value;
            updateOpacityValueText();
            document.documentElement.style.setProperty('--bg-opacity', opacity);
        });
        
        // 时间字体选择
        timeFontSelect.addEventListener('change', function() {
            currentFont = this.value;
            applyColorSettings();
        });
        
        // 颜色区域交互
        colorArea.addEventListener('mousedown', (e) => {
            isDragging = true;
            handleColorInteraction(e.clientX, e.clientY);
            document.body.style.cursor = 'grabbing';
        });
        
        document.addEventListener('mousemove', (e) => {
            if (isDragging) {
                handleColorInteraction(e.clientX, e.clientY);
            }
        });
        
        document.addEventListener('mouseup', () => {
            if (isDragging) {
                isDragging = false;
                document.body.style.cursor = 'default';
            }
        });
        
        colorArea.addEventListener('touchstart', (e) => {
            isDragging = true;
            handleColorInteraction(e.touches[0].clientX, e.touches[0].clientY);
            e.preventDefault();
            document.body.style.cursor = 'grabbing';
        }, { passive: false });
        
        document.addEventListener('touchmove', (e) => {
            if (isDragging) {
                handleColorInteraction(e.touches[0].clientX, e.touches[0].clientY);
                e.preventDefault();
            }
        }, { passive: false });
        
        document.addEventListener('touchend', () => {
            if (isDragging) {
                isDragging = false;
                document.body.style.cursor = 'default';
            }
        });
        
        // 滑块事件
        function updateColorFromSlider() {
            const h = hSlider.value;
            const s = sSlider.value;
            const v = vSlider.value;
            hValue.textContent = h;
            sValue.textContent = s;
            vValue.textContent = v;
            
            updateColorPointerPosition(h, s);
            updateColorPreview(h, s, v);
        }
        
        hSlider.addEventListener('input', updateColorFromSlider);
        sSlider.addEventListener('input', updateColorFromSlider);
        vSlider.addEventListener('input', updateColorFromSlider);
        
        // 重置颜色
        resetColorButton.addEventListener('click', () => {
            if (currentColorTarget) {
                switch (currentColorTarget) {
                    case 'time': hSlider.value = 0; sSlider.value = 0; vSlider.value = 100; break;
                    case 'date': case 'week': case 'slogan': hSlider.value = 200; sSlider.value = 70; vSlider.value = 90; break;
                }
                updateColorFromSlider();
            }
        });
        
        // 确认颜色选择
        confirmColorButton.addEventListener('click', () => {
            if (currentColorTarget) {
                const color = hsvToHex(hSlider.value, sSlider.value, vSlider.value);
                colors[currentColorTarget] = color;
                hideMenu(colorPicker);
                applyColorSettings();
            }
        });
        
        // 每秒更新时钟
        setInterval(updateClock, 1000);
    </script>
</body>
</html>
