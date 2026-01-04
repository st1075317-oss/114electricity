[index.html](https://github.com/user-attachments/files/24420207/index.html)
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>華語 AI 學習生態系 | Chinese AI Learning Ecosystem</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- React & ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
    
    <!-- Babel for JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    
    <!-- Lucide Icons (UMD) -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #F8FAFC; }
        .glass-effect {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
        }
        .gradient-text {
            background: linear-gradient(135deg, #0f172a 0%, #334155 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .animate-float {
            animation: float 6s ease-in-out infinite;
        }
        @keyframes float {
            0% { transform: translateY(0px); }
            50% { transform: translateY(-20px); }
            100% { transform: translateY(0px); }
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // --- Icon Adapter System ---
        const Lucide = window.lucide || {};
        
        const Icon = ({ name, size = 24, className, ...props }) => {
            const iconData = Lucide.icons ? Lucide.icons[name] : null;
            if (!iconData) return null;

            return React.createElement(
                'svg',
                {
                    width: size,
                    height: size,
                    viewBox: "0 0 24 24",
                    fill: "none",
                    stroke: "currentColor",
                    strokeWidth: "2",
                    strokeLinecap: "round",
                    strokeLinejoin: "round",
                    className: className,
                    ...props
                },
                iconData.map((data, index) => 
                    React.createElement(data[0], { ...data[1], key: index })
                )
            );
        };

        // 定義所有使用到的圖標組件
        const Users = (props) => <Icon name="users" {...props} />;
        const BookOpen = (props) => <Icon name="book-open" {...props} />;
        const MessageCircle = (props) => <Icon name="message-circle" {...props} />;
        const PenTool = (props) => <Icon name="pen-tool" {...props} />;
        const GraduationCap = (props) => <Icon name="graduation-cap" {...props} />;
        const Globe = (props) => <Icon name="globe" {...props} />;
        const Briefcase = (props) => <Icon name="briefcase" {...props} />;
        const Heart = (props) => <Icon name="heart" {...props} />;
        const Plane = (props) => <Icon name="plane" {...props} />;
        const Cpu = (props) => <Icon name="cpu" {...props} />;
        const ArrowRight = (props) => <Icon name="arrow-right" {...props} />;
        const CheckCircle2 = (props) => <Icon name="check-circle-2" {...props} />;
        const Sparkles = (props) => <Icon name="sparkles" {...props} />;
        const Zap = (props) => <Icon name="zap" {...props} />;
        const ShieldCheck = (props) => <Icon name="shield-check" {...props} />;
        const Target = (props) => <Icon name="target" {...props} />;
        const Fingerprint = (props) => <Icon name="fingerprint" {...props} />;
        const Coffee = (props) => <Icon name="coffee" {...props} />;
        const BookHeart = (props) => <Icon name="book-heart" {...props} />;
        const ThumbsUp = (props) => <Icon name="thumbs-up" {...props} />; // 新增

        // --- 商業邏輯數據 ---
        
        // 1. 身分導航數據 (Persona Mapping) - 新增 secondaryPlatform
        const PERSONAS = [
            { id: 'tourist', label: '短期旅遊 / 背包客', icon: <Plane size={20}/>, targetPlatform: 1, secondaryPlatform: 2, desc: '快速學會生存對話，並能簡單互動。' },
            { id: 'spouse', label: '外籍配偶 (新住民)', icon: <Heart size={20}/>, targetPlatform: 2, secondaryPlatform: 1, desc: '融入家庭生活，從聽懂開始，逐步識字。' },
            { id: 'student', label: '國際留學生', icon: <GraduationCap size={20}/>, targetPlatform: 3, secondaryPlatform: 4, desc: '應對學術報告，並深入了解校園文化。' },
            { id: 'business', label: '商務 / 外派主管', icon: <Briefcase size={20}/>, targetPlatform: 4, secondaryPlatform: 2, desc: '職場談判為主，日常社交口語為輔。' },
            { id: 'worker', label: '外籍工作者', icon: <Users size={20}/>, targetPlatform: 2, secondaryPlatform: 1, desc: '聽懂工廠指令，滿足基礎生活需求。' },
            { id: 'heritage', label: '華裔子弟 (Heritage)', icon: <Fingerprint size={20}/>, targetPlatform: 3, secondaryPlatform: 4, desc: '強化讀寫能力，並進行深度文化尋根。' },
            { id: 'tech', label: '科技新貴 (工程師)', icon: <Cpu size={20}/>, targetPlatform: 4, secondaryPlatform: 3, desc: '熟悉產業術語，並能閱讀技術文件。' },
            { id: 'wh', label: '打工度假 (WH)', icon: <Coffee size={20}/>, targetPlatform: 1, secondaryPlatform: 2, desc: '邊玩邊工作，實用生活與交友華語。' },
            { id: 'missionary', label: '宗教志工 / 傳教', icon: <BookHeart size={20}/>, targetPlatform: 2, secondaryPlatform: 4, desc: '深耕在地社區互動，深入文化底蘊。' },
            { id: 'beginner', label: '非華語系初學者', icon: <Globe size={20}/>, targetPlatform: 1, secondaryPlatform: 2, desc: '從零開始建立信心，嘗試開口說話。' },
        ];

        // 2. 平台矩陣數據 (Platform Matrix)
        const PLATFORMS = [
            {
                id: 1,
                name: "Platform No.1",
                tagline: "基礎建設孵化器",
                role: "The Foundation Builder",
                level: "A1 - A2 入門",
                features: ["零基礎友善介面", "生存短句生成", "無挫折感設計"],
                color: "bg-red-500",
                lightColor: "bg-red-50",
                textColor: "text-red-600",
                borderColor: "border-red-200",
                icon: <BookOpen size={32}/>,
                link: "page1.html"  // <--- 修改這裡 (原本是 page1.html)
            },
            {
                id: 2,
                name: "Platform No.2",
                tagline: "互動口語教練",
                role: "The Conversation Partner",
                level: "A2 - B1 生活",
                features: ["AI 語音對話模擬", "即時語法糾錯", "聽力強化訓練"],
                color: "bg-orange-500",
                lightColor: "bg-orange-50",
                textColor: "text-orange-600",
                borderColor: "border-orange-200",
                icon: <MessageCircle size={32}/>,
                link: "page2.html" // <--- 修改這裡 (原本是 page2.html)
            },
            {
                id: 3,
                name: "Platform No.3",
                tagline: "讀寫素養專家",
                role: "The Literacy Expert",
                level: "B1 - C1 學術",
                features: ["文章結構深度解析", "正式/學術語氣改寫", "閱讀測驗自動生成"],
                color: "bg-amber-500",
                lightColor: "bg-amber-50",
                textColor: "text-amber-600",
                borderColor: "border-amber-200",
                icon: <PenTool size={32}/>,
                link: "page3.html" // <--- 修改這裡 (原本是 page3.html)
            },
            {
                id: 4,
                name: "Platform No.4",
                tagline: "文化與職涯顧問",
                role: "The Career Guide",
                level: "C1 - C6 高階",
                features: ["多模態情境 (圖轉文)", "商務談判模擬", "雙引擎 (體驗/正式)"],
                color: "bg-teal-600",
                lightColor: "bg-teal-50",
                textColor: "text-teal-700",
                borderColor: "border-teal-200",
                icon: <Briefcase size={32}/>,
                link: "page4.html" // <--- 修改這裡 (原本是 page4.html)
            }
        ];
const SettingsModal = ({ isOpen, onClose, apiKey, setApiKey }) => {
    const [inputKey, setInputKey] = useState(apiKey);
    const [showKey, setShowKey] = useState(false);

    useEffect(() => { setInputKey(apiKey); }, [apiKey]);

    const handleSave = () => {
        localStorage.setItem('hua_yu_api_key', inputKey);
        localStorage.setItem('gemini_api_key', inputKey);
        setApiKey(inputKey);
        alert("✅ API Key 已儲存！所有子頁面皆可共用。");
        onClose();
    };

    const handleClear = () => {
        localStorage.removeItem('hua_yu_api_key');
        localStorage.removeItem('gemini_api_key');
        setApiKey('');
        setInputKey('');
        alert("🗑️ API Key 已清除");
    };

    if (!isOpen) return null;

    return (
        <div className="fixed inset-0 z-[100] flex items-center justify-center bg-black/50 backdrop-blur-sm p-4" style={{animation: 'none'}}>
            <div className="bg-white rounded-2xl shadow-2xl w-full max-w-md overflow-hidden p-6">
                <div className="flex justify-between items-center mb-6">
                    <h3 className="text-xl font-bold text-slate-900 flex items-center gap-2">
                        <Zap className="text-yellow-500" size={24}/> 全域 API 設定
                    </h3>
                    <button onClick={onClose}><span className="text-2xl">&times;</span></button>
                </div>
                
                <div className="space-y-4">
                    {/* 原本的藍色說明區塊已移除 */}
                    
                    <div>
                        <label className="block text-sm font-bold text-slate-700 mb-2">API Key</label>
                        <div className="relative">
                            <input 
                                type={showKey ? "text" : "password"} 
                                value={inputKey}
                                onChange={(e) => setInputKey(e.target.value)}
                                className="w-full border border-slate-300 rounded-lg pl-4 pr-10 py-3 focus:ring-2 focus:ring-blue-500 outline-none"
                                placeholder="貼上您的 Key..."
                            />
                            <button 
                                onClick={() => setShowKey(!showKey)}
                                className="absolute right-3 top-3.5 text-slate-400 hover:text-slate-600"
                            >
                                {showKey ? <span className="text-xs">隱藏</span> : <span className="text-xs">顯示</span>}
                            </button>
                        </div>
                        
                        {/* --- 修改重點：移動說明文字至此，並加入連結 --- */}
                        <p className="text-xs text-slate-500 mt-2 leading-relaxed">
                            在此輸入 Google Gemini API Key，將自動同步至所有子分頁（1~4頁）。
                            <a 
                                href="https://aistudio.google.com/" 
                                target="_blank" 
                                rel="noreferrer" 
                                className="text-blue-600 hover:underline font-bold ml-1"
                            >
                                取得 API Key
                            </a>
                        </p>
                        {/* ------------------------------------------- */}
                    </div>

                    <div className="flex gap-3 pt-4">
                        <button onClick={handleClear} className="flex-1 py-2 text-red-500 hover:bg-red-50 rounded-lg font-bold border border-transparent hover:border-red-100 transition">
                            清除
                        </button>
                        <button onClick={handleSave} className="flex-[2] py-2 bg-slate-900 text-white rounded-lg font-bold hover:bg-slate-800 transition shadow-lg">
                            儲存並同步
                        </button>
                    </div>
                </div>
            </div>
        </div>
    );
};
        function App() {
const [isSettingsOpen, setIsSettingsOpen] = useState(false);
    const [apiKey, setApiKey] = useState('');

    // 初始化時檢查是否有 Key (任一 Key 存在即可)
    useEffect(() => {
        const key1 = localStorage.getItem('hua_yu_api_key');
        const key2 = localStorage.getItem('gemini_api_key');
        if (key1) setApiKey(key1);
        else if (key2) setApiKey(key2);
    }, []);
            const [selectedPersona, setSelectedPersona] = useState(null);
            
            // 當選擇身分時，自動捲動到主要推薦平台
            useEffect(() => {
                if (selectedPersona) {
                    const element = document.getElementById(`platform-${selectedPersona.targetPlatform}`);
                    if (element) {
                        element.scrollIntoView({ behavior: 'smooth', block: 'center' });
                    }
                }
            }, [selectedPersona]);

            return (
                <div className="min-h-screen flex flex-col">
                    
                    {/* Navigation */}
                    <nav className="glass-effect fixed w-full z-50 border-b border-slate-200">
                        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                            <div className="flex justify-between h-16 items-center">
                                <div className="flex items-center gap-2">
                                    <div className="w-8 h-8 bg-slate-900 rounded-lg flex items-center justify-center text-white font-bold">AI</div>
                                    <span className="font-bold text-xl tracking-tight text-slate-900">華語 AI 學習生態系</span>
                                </div>
                                <div className="hidden md:flex space-x-8">
                                    <a href="#matrix" className="text-slate-600 hover:text-slate-900 font-medium">全平台矩陣</a>
                                    <a href="#pricing" className="text-slate-600 hover:text-slate-900 font-medium">商業方案</a>
                                   <button 
    onClick={() => setIsSettingsOpen(true)}
    className={`px-5 py-2 rounded-full text-sm font-bold transition flex items-center gap-2 ${apiKey ? 'bg-green-500 text-white hover:bg-green-600 shadow-lg shadow-green-200' : 'bg-slate-900 text-white hover:bg-slate-800'}`}
>
    {apiKey ? <CheckCircle2 size={16}/> : <Zap size={16}/>}
    {apiKey ? 'API 已就緒' : '設定 API Key'}
</button>
                                </div>
                            </div>
                        </div>
                    </nav>

                    {/* Hero Section */}
                    <section className="pt-32 pb-20 px-4 relative overflow-hidden">
                        <div className="absolute top-0 right-0 -mr-20 -mt-20 w-96 h-96 bg-blue-100 rounded-full blur-3xl opacity-50"></div>
                        <div className="absolute bottom-0 left-0 -ml-20 -mb-20 w-80 h-80 bg-orange-100 rounded-full blur-3xl opacity-50"></div>
                        
                        <div className="max-w-7xl mx-auto text-center relative z-10">
                            <div className="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-slate-100 text-slate-600 text-sm font-medium mb-8">
                                <span className="w-2 h-2 rounded-full bg-green-500 animate-pulse"></span>
                                AI 驅動 ・ 分層滲透 ・ 精準導流
                            </div>
                            <h1 className="text-5xl md:text-7xl font-black text-slate-900 mb-6 leading-tight">
                                為您量身打造的<br/>
                                <span className="text-transparent bg-clip-text bg-gradient-to-r from-blue-600 to-teal-500">華語學習路徑</span>
                            </h1>
                            <p className="text-xl text-slate-500 max-w-2xl mx-auto mb-12">
                                我們不提供「一體適用」的軟體。這是一個由四個垂直領域組成的 AI 生態系，無論您是遊客、商務人士或留學生，都能找到專屬的解決方案。
                            </p>

                            {/* Identity Selector */}
                            <div className="bg-white p-8 rounded-3xl shadow-xl border border-slate-100 max-w-6xl mx-auto">
                                <h3 className="text-lg font-bold text-slate-800 mb-6 flex items-center justify-center gap-2">
                                    <Target className="text-blue-600"/> 請選擇您的身分，AI 將為您導航：
                                </h3>
                                <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4">
                                    {PERSONAS.map(p => (
                                        <button 
                                            key={p.id}
                                            onClick={() => setSelectedPersona(p)}
                                            className={`p-4 rounded-xl border transition-all duration-300 flex flex-col items-center gap-3 hover:shadow-md ${
                                                selectedPersona?.id === p.id 
                                                    ? 'bg-slate-900 text-white border-slate-900 transform scale-105' 
                                                    : 'bg-white text-slate-600 border-slate-200 hover:border-slate-400'
                                            }`}
                                        >
                                            <div className={`${selectedPersona?.id === p.id ? 'text-white' : 'text-slate-900'}`}>{p.icon}</div>
                                            <span className="text-sm font-bold whitespace-nowrap">{p.label}</span>
                                        </button>
                                    ))}
                                </div>
                                {selectedPersona && (
                                    <div className="mt-6 p-4 bg-slate-50 rounded-xl border border-slate-200 flex items-center justify-between animate-fade-in">
                                        <div className="text-left">
                                            <span className="text-xs font-bold text-slate-400 uppercase">AI 推薦方案</span>
                                            <div className="font-bold text-slate-800">{selectedPersona.desc}</div>
                                        </div>
                                        <div className="flex items-center gap-3">
                                            <div className="text-right hidden md:block">
                                                <div className="text-sm text-slate-500">最佳匹配平台</div>
                                                <div className="text-xl font-black text-blue-600">Platform No.{selectedPersona.targetPlatform}</div>
                                            </div>
                                            <a href={`#platform-${selectedPersona.targetPlatform}`} className="p-3 bg-blue-600 text-white rounded-full hover:bg-blue-700 transition">
                                                <ArrowRight />
                                            </a>
                                        </div>
                                    </div>
                                )}
                            </div>
                        </div>
                    </section>

                    {/* Platform Matrix Section */}
                    <section id="matrix" className="py-20 bg-white">
                        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                            <div className="text-center mb-16">
                                <h2 className="text-3xl font-bold text-slate-900">四大垂直領域平台</h2>
                                <p className="text-slate-500 mt-4">從入門到精通的完整產品矩陣</p>
                            </div>

                            <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-8">
                                {PLATFORMS.map((platform) => {
                                    const isRecommended = selectedPersona?.targetPlatform === platform.id;
                                    const isSecondary = selectedPersona?.secondaryPlatform === platform.id;
                                    
                                    // 動態樣式
                                    let containerClass = "border-slate-100 hover:border-slate-300 hover:shadow-xl bg-slate-50";
                                    let scaleClass = "";
                                    
                                    if (isRecommended) {
                                        containerClass = `border-4 ${platform.borderColor.replace('border', 'border')} shadow-2xl z-10 bg-white`;
                                        scaleClass = "scale-105";
                                    } else if (isSecondary) {
                                        containerClass = `border-2 border-blue-400 shadow-xl z-0 bg-blue-50/30`;
                                        scaleClass = "scale-102";
                                    }

                                    return (
                                        <div 
                                            key={platform.id} 
                                            id={`platform-${platform.id}`}
                                            className={`relative rounded-3xl p-6 border transition-all duration-500 flex flex-col h-full transform ${containerClass} ${scaleClass}`}
                                        >
                                            {/* AI Primary Recommendation Badge */}
                                            {isRecommended && (
                                                <div className="absolute -top-4 left-1/2 transform -translate-x-1/2 bg-slate-900 text-white text-xs font-bold px-4 py-1 rounded-full flex items-center gap-1 shadow-lg whitespace-nowrap">
                                                    <Sparkles size={12} className="text-yellow-400"/> AI 強力推薦
                                                </div>
                                            )}

                                            {/* Secondary Recommendation Badge */}
                                            {isSecondary && (
                                                <div className="absolute -top-4 left-1/2 transform -translate-x-1/2 bg-white text-blue-600 border border-blue-200 text-xs font-bold px-4 py-1 rounded-full flex items-center gap-1 shadow-md whitespace-nowrap">
                                                    <ThumbsUp size={12} className="text-blue-500"/> 搭配學習推薦
                                                </div>
                                            )}
                                            
                                            {/* Header */}
                                            <div className={`w-16 h-16 rounded-2xl flex items-center justify-center mb-6 ${platform.lightColor} ${platform.textColor}`}>
                                                {platform.icon}
                                            </div>
                                            
                                            <div className="mb-2 flex items-center gap-2">
                                                <span className={`text-xs font-bold px-2 py-1 rounded-md ${platform.lightColor} ${platform.textColor}`}>
                                                    {platform.level}
                                                </span>
                                            </div>
                                            
                                            <h3 className="text-2xl font-black text-slate-900 mb-1">{platform.name}</h3>
                                            <div className={`text-sm font-bold mb-4 ${platform.textColor}`}>{platform.tagline}</div>
                                            <p className="text-slate-500 text-sm mb-6 min-h-[40px]">{platform.role}</p>

                                            {/* Features */}
                                            <ul className="space-y-3 mb-8 flex-grow">
                                                {platform.features.map((feat, i) => (
                                                    <li key={i} className="flex items-start gap-2 text-sm text-slate-600">
                                                        <CheckCircle2 size={16} className={`mt-0.5 flex-shrink-0 ${platform.textColor}`}/>
                                                        {feat}
                                                    </li>
                                                ))}
                                            </ul>

                                            {/* CTA */}
                                            <a 
                                                href={platform.link} 
                                                                                                className={`w-full py-3 rounded-xl font-bold text-center transition-all ${
                                                    isRecommended 
                                                        ? `${platform.color} text-white hover:opacity-90 shadow-lg` 
                                                        : isSecondary
                                                            ? 'bg-blue-600 text-white hover:bg-blue-700 shadow-md'
                                                            : 'bg-white border border-slate-200 text-slate-700 hover:bg-slate-100'
                                                }`}
                                            >
                                                進入平台
                                            </a>
                                        </div>
                                    );
                                })}
                            </div>
                        </div>
                    </section>

                    {/* Business Model / Pricing Hint */}
                    <section id="pricing" className="py-20 bg-slate-900 text-white overflow-hidden relative">
                        {/* Background Decoration */}
                        <div className="absolute top-0 left-0 w-full h-full overflow-hidden opacity-20 pointer-events-none">
                            <div className="absolute top-10 left-10 w-64 h-64 bg-blue-500 rounded-full blur-3xl"></div>
                            <div className="absolute bottom-10 right-10 w-64 h-64 bg-teal-500 rounded-full blur-3xl"></div>
                        </div>

                        <div className="max-w-7xl mx-auto px-4 relative z-10">
                            <div className="grid md:grid-cols-2 gap-16 items-center">
                                <div>
                                    <h2 className="text-3xl md:text-4xl font-black mb-6">Freemium 雙引擎模式</h2>
                                    <p className="text-slate-400 text-lg mb-8 leading-relaxed">
                                        我們採用靈活的商業模式。所有平台皆提供免費的「模擬體驗版」，讓使用者零成本試用。當您準備好追求無限生成與即時 AI 互動時，可隨時升級至正式版。
                                    </p>
                                    <div className="flex flex-col gap-4">
                                        <div className="flex items-center gap-4 p-4 rounded-xl bg-slate-800/50 border border-slate-700">
                                            <div className="p-3 bg-green-500/20 text-green-400 rounded-lg"><Zap size={24}/></div>
                                            <div>
                                                <div className="font-bold text-white">體驗版 (Trial Mode)</div>
                                                <div className="text-sm text-slate-400">高擬真模擬數據，適合初次體驗與教學演示。</div>
                                            </div>
                                        </div>
                                        <div className="flex items-center gap-4 p-4 rounded-xl bg-slate-800/50 border border-slate-700">
                                            <div className="p-3 bg-purple-500/20 text-purple-400 rounded-lg"><Cpu size={24}/></div>
                                            <div>
                                                <div className="font-bold text-white">正式版 (Official / API)</div>
                                                <div className="text-sm text-slate-400">連接 Gemini AI，無限生成客製化內容，商務首選。</div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div className="bg-white/5 backdrop-blur-lg rounded-3xl p-8 border border-white/10">
                                    <h3 className="text-xl font-bold mb-6 text-center">企業與教育機構方案</h3>
                                    <ul className="space-y-4 mb-8">
                                        <li className="flex items-center gap-3 text-sm text-slate-300">
                                            <ShieldCheck className="text-teal-400" size={18}/> 跨平台數據整合 (API Access)
                                        </li>
                                        <li className="flex items-center gap-3 text-sm text-slate-300">
                                            <ShieldCheck className="text-teal-400" size={18}/> 員工/學生學習歷程追蹤
                                        </li>
                                        <li className="flex items-center gap-3 text-sm text-slate-300">
                                            <ShieldCheck className="text-teal-400" size={18}/> 專屬客製化情境 (Factory/Office)
                                        </li>
                                    </ul>
                                    <button className="w-full py-4 bg-gradient-to-r from-blue-600 to-teal-500 rounded-xl font-bold text-white hover:opacity-90 transition shadow-lg">
                                        聯繫業務團隊
                                    </button>
                                </div>
                            </div>
                        </div>
                    </section>

                    {/* Footer */}
                    <footer className="bg-slate-50 py-12 border-t border-slate-200">
                        <div className="max-w-7xl mx-auto px-4 text-center">
                            <div className="flex items-center justify-center gap-2 mb-4">
                                <div className="w-6 h-6 bg-slate-900 rounded flex items-center justify-center text-white text-xs font-bold">AI</div>
                                <span className="font-bold text-slate-900">華語 AI 學習生態系</span>
                            </div>
                            <p className="text-slate-500 text-sm mb-6">賦能華語學習，連結未來機遇。</p>

                            <div className="flex justify-center gap-6 text-sm text-slate-600">
                                <a href="#" className="hover:text-blue-600">關於我們</a>
                                <a href="#" className="hover:text-blue-600">服務條款</a>
                                <a href="#" className="hover:text-blue-600">隱私權政策</a>                                 
                            </div>


                            <div className="mt-8 text-xs text-slate-400">
                                © 2025 Chinese AI Learning Ecosystem. All rights reserved.
                            </div>

                        </div>
                    </footer>
<SettingsModal 
                        isOpen={isSettingsOpen} 
                        onClose={() => setIsSettingsOpen(false)} 
                        apiKey={apiKey} 
                        setApiKey={setApiKey} 
                    />

                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
