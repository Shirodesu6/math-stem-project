<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>CYBER-CALC v6.0 | NEURAL TERMINAL</title>
    <script src="https://cdn.jsdelivr.net/npm/nerdamer@1.1.13/nerdamer.core.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/nerdamer@1.1.13/Algebra.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/nerdamer@1.1.13/Calculus.js"></script>
    <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Fira+Code&display=swap" rel="stylesheet">
    <style>
        :root { --cyan: #00f3ff; --pink: #ff00ff; --dark: #08080c; }
        body { background: var(--dark); color: #fff; font-family: 'Orbitron', sans-serif; margin: 0; padding: 20px; display: flex; justify-content: center; }
        .terminal {
            width: 100%; max-width: 850px; background: rgba(10, 15, 25, 0.95);
            border: 2px solid var(--cyan); box-shadow: 0 0 30px rgba(0, 243, 255, 0.2);
            padding: 30px; position: relative; overflow: hidden;
        }
        .header { text-align: center; border-bottom: 1px solid var(--cyan); margin-bottom: 25px; padding-bottom: 10px; }
        .cyber-input {
            width: 100%; padding: 15px; background: #000; border: 1px solid #333;
            border-left: 5px solid var(--pink); color: var(--cyan);
            font-family: 'Fira Code', monospace; font-size: 1.2rem; outline: none; box-sizing: border-box;
        }
        .control-panel { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 15px; margin: 25px 0; }
        .btn {
            background: none; border: 1px solid var(--cyan); color: var(--cyan);
            padding: 15px; cursor: pointer; font-family: 'Orbitron'; font-weight: bold;
            text-transform: uppercase; transition: 0.3s;
        }
        .btn:hover { background: var(--cyan); color: #000; box-shadow: 0 0 20px var(--cyan); }
        .btn-pink { border-color: var(--pink); color: var(--pink); }
        .btn-pink:hover { background: var(--pink); color: #000; box-shadow: 0 0 20px var(--pink); }
        
        #display {
            background: rgba(0,0,0,0.8); border: 1px solid #222; padding: 20px;
            min-height: 200px; font-family: 'Fira Code', monospace; position: relative;
        }
        .step-log { color: #888; font-size: 0.85rem; border-left: 2px solid #444; padding-left: 15px; margin-bottom: 20px; }
        .final-result { font-size: 1.5rem; color: #fff; background: rgba(255,255,255,0.05); padding: 15px; border-radius: 5px; }
        .bounds-group { display: flex; gap: 10px; margin-top: 10px; }
        .bound-in { width: 50%; padding: 10px; background: #111; border: 1px solid #333; color: white; outline: none; }
    </style>
</head>
<body>

<div class="terminal">
    <div class="header">
        <h2 style="margin:0; letter-spacing: 10px;">NEURAL CALCULUS SYSTEM</h2>
    </div>

    <label style="font-size: 10px; color: var(--pink);">[INPUT_FUNCTION]</label>
    <input type="text" id="mainInput" class="cyber-input" placeholder="Ví dụ: x^3 + 2x^2 + sin(x)">
    
    <div class="bounds-group">
        <input type="number" id="lowerA" class="bound-in" placeholder="Cận dưới (a)">
        <input type="number" id="upperB" class="bound-in" placeholder="Cận trên (b)">
    </div>

    <div class="control-panel">
        <button class="btn btn-pink" onclick="process('diff')">Đạo Hàm</button>
        <button class="btn btn-pink" onclick="process('int')">Nguyên Hàm</button>
        <button class="btn" onclick="process('def')">Tích Phân XĐ</button>
    </div>

    <div id="display">
        <div id="steps" class="step-log">Sẵn sàng nhận dữ liệu...</div>
        <div id="result" class="final-result"></div>
    </div>
</div>

<script>
    function logStep(msg) {
        const steps = document.getElementById('steps');
        steps.innerHTML += `<div>> ${msg}</div>`;
    }

    function process(mode) {
        let input = document.getElementById('mainInput').value.trim();
        const resDiv = document.getElementById('result');
        const stepsDiv = document.getElementById('steps');
        
        // Reset màn hình
        stepsDiv.innerHTML = "";
        resDiv.innerHTML = "";

        if(!input) {
            logStep("LỖI: Chưa nhập hàm số.");
            return;
        }

        // Chuẩn hóa cú pháp cho Nerdamer
        let cleanInput = input.replace(/(\d)([a-zA-Z\(])/g, '$1*$2');
        logStep("Khởi tạo tiến trình giải tích...");
        logStep(`Hàm số nhận diện: f(x) = ${cleanInput}`);

        try {
            if (mode === 'diff') {
                logStep("Đang áp dụng quy tắc đạo hàm (Power rule, Product rule...)...");
                let sol = nerdamer(`diff(${cleanInput}, x)`);
                logStep("Đơn giản hóa biểu thức...");
                
                resDiv.innerHTML = `f'(x) = \\[ ${nerdamer(sol).toTeX()} \\]`;
            } 
            else if (mode === 'int') {
                logStep("Đang phân tích bảng nguyên hàm cơ bản...");
                logStep("Tìm kiếm phương pháp: Tích phân từng phần / Đổi biến...");
                let sol = nerdamer(`integrate(${cleanInput}, x)`);
                
                resDiv.innerHTML = `∫ f(x) dx = \\[ ${nerdamer(sol).toTeX()} + C \\]`;
            }
            else if (mode === 'def') {
                let a = document.getElementById('lowerA').value;
                let b = document.getElementById('upperB').value;
                
                if(a === "" || b === "") {
                    logStep("LỖI: Thiếu cận tích phân.");
                    return;
                }
                
                logStep(`Tính nguyên hàm F(x)...`);
                let F = nerdamer(`integrate(${cleanInput}, x)`);
                logStep(`Áp dụng định lý Newton-Leibniz: F(${b}) - F(${a})`);
                
                let val = nerdamer(`defint(${cleanInput}, ${a}, ${b}, x)`).evaluate().toString();
                resDiv.innerHTML = `Kết quả: \\[ ${val} \\]`;
            }

            // Gọi MathJax vẽ lại công thức cho đẹp
            MathJax.typesetPromise();
            logStep("HOÀN TẤT.");

        } catch (e) {
            logStep("LỖI: Hệ thống không thể xử lý biểu thức này.");
        }
    }
</script>

</body>
</html>
