# DESC.test
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DISC風格測驗</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }

        .question {
            background-color: white;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .options {
            display: grid;
            gap: 10px;
            margin-top: 15px;
        }

        .option {
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .option:hover {
            background-color: #f0f0f0;
        }

        .option.selected {
            background-color: #4CAF50;
            color: white;
            border-color: #45a049;
        }

        .button-group {
            text-align: center;
            margin: 30px 0;
        }

        button {
            padding: 12px 30px;
            margin: 0 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            transition: all 0.3s ease;
        }

        #submitBtn {
            background-color: #2196F3;
            color: white;
        }

        #resetBtn {
            background-color: #ff9800;
            color: white;
        }

        #result {
            display: none;
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            margin-top: 30px;
            text-align: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .type-box {
            display: inline-block;
            padding: 20px 40px;
            margin: 10px;
            border-radius: 10px;
            font-size: 24px;
            font-weight: bold;
            color: white;
        }
    </style>
</head>
<body>
    <h1 style="text-align: center; color: #333;">DISC風格測驗</h1>
    <div id="questionsContainer"></div>
    <div class="button-group">
        <button id="submitBtn" onclick="submitTest()">立即測驗</button>
        <button id="resetBtn" onclick="resetTest()">重新測試</button>
    </div>
    <div id="result"></div>

    <script>
        const questions = [
            {
                question: "❶ 當團隊面臨緊急狀況時，你會怎麼做？",
                options: [
                    { text: "A. 立刻出面做決策，引導大家行動", type: "D" },
                    { text: "B. 鼓舞團隊士氣，讓氣氛不要太緊繃", type: "I" },
                    { text: "C. 保持穩定節奏，照原計畫處理", type: "S" },
                    { text: "D. 仔細分析問題後，再建議可行方案", type: "C" }
                ]
            },
            {
                question: "❷ 如果你參與一個新團隊，你會：",
                options: [
                    { text: "A. 馬上主動領導，設定方向", type: "D" },
                    { text: "B. 和每個人打招呼，快速變熟", type: "I" },
                    { text: "C. 安靜觀察一陣子，再融入團隊", type: "S" },
                    { text: "D. 詢問工作規則與細節，先搞清楚流程", type: "C" }
                ]
            },
            {
                question: "❸ 你最在意以下哪件事？",
                options: [
                    { text: "A. 目標能不能完成、有沒有成果", type: "D" },
                    { text: "B. 團隊氣氛好不好、有沒有互動", type: "I" },
                    { text: "C. 關係是否和諧、彼此有沒有信任", type: "S" },
                    { text: "D. 資訊是否完整、流程有沒有瑕疵", type: "C" }
                ]
            },
            {
                question: "❹ 當你收到一個新的任務時，你通常會：",
                options: [
                    { text: "A. 直接分配人力、立刻開始執行", type: "D" },
                    { text: "B. 找人一起討論點子，集思廣益", type: "I" },
                    { text: "C. 先了解影響範圍，再穩定安排時程", type: "S" },
                    { text: "D. 研究細節並整理清單再動手", type: "C" }
                ]
            },
            {
                question: "❺ 當你表達想法時，你傾向：",
                options: [
                    { text: "A. 有力地表達立場，不怕衝突", type: "D" },
                    { text: "B. 生動地分享，講得大家都笑出來", type: "I" },
                    { text: "C. 委婉地說，顧慮他人感受", type: "S" },
                    { text: "D. 條理清晰、有條有理", type: "C" }
                ]
            },
            {
                question: "❻ 面對別人的批評時，你最常的反應是？",
                options: [
                    { text: "A. 不服氣，會為自己辯護", type: "D" },
                    { text: "B. 會試著幽默帶過或緩和氣氛", type: "I" },
                    { text: "C. 表面接受，私下可能悶悶不樂", type: "S" },
                    { text: "D. 會檢討細節、釐清是非邏輯", type: "C" }
                ]
            },
            {
                question: "❼ 你對「變化」的態度是？",
                options: [
                    { text: "A. 喜歡挑戰，變化讓我有動力", type: "D" },
                    { text: "B. 覺得新奇有趣，有新機會就來", type: "I" },
                    { text: "C. 有點抗拒，喜歡有秩序的流程", type: "S" },
                    { text: "D. 要先理解變化背後的邏輯再說", type: "C" }
                ]
            }
        ];

        let userAnswers = new Array(questions.length);
        const typeColors = {
            D: "#e74c3c",
            I: "#f1c40f",
            S: "#2ecc71",
            C: "#3498db"
        };

        // 初始化題目
        function initQuestions() {
            const container = document.getElementById('questionsContainer');
            
            questions.forEach((q, index) => {
                const questionDiv = document.createElement('div');
                questionDiv.className = 'question';
                questionDiv.innerHTML = `<h3>${q.question}</h3>`;
                
                const optionsDiv = document.createElement('div');
                optionsDiv.className = 'options';
                
                q.options.forEach(option => {
                    const optionBtn = document.createElement('div');
                    optionBtn.className = 'option';
                    optionBtn.textContent = option.text;
                    optionBtn.dataset.type = option.type;
                    optionBtn.dataset.questionIndex = index;
                    optionBtn.onclick = handleOptionClick;
                    optionsDiv.appendChild(optionBtn);
                });
                
                questionDiv.appendChild(optionsDiv);
                container.appendChild(questionDiv);
            });
        }

        // 處理選項點擊
        function handleOptionClick(event) {
            const questionIndex = parseInt(event.target.dataset.questionIndex);
            const selectedType = event.target.dataset.type;
            
            // 移除同一題其他選項的選中狀態
            const options = document.querySelectorAll(`[data-question-index="${questionIndex}"]`);
            options.forEach(opt => opt.classList.remove('selected'));
            
            // 設置當前選中狀態
            event.target.classList.add('selected');
            userAnswers[questionIndex] = selectedType;
        }

        // 提交測驗
        function submitTest() {
            // 檢查是否所有題目都已回答
            if (userAnswers.includes(undefined)) {
                alert('請完成所有題目後再提交！');
                return;
            }

            // 計算分數
            const scores = { D:0, I:0, S:0, C:0 };
            userAnswers.forEach(type => scores[type]++);

            // 找出最高分類型
            const maxScore = Math.max(...Object.values(scores));
            const resultTypes = Object.keys(scores).filter(type => scores[type] === maxScore);

            // 顯示結果
            showResult(resultTypes);
        }

        // 顯示結果
        function showResult(types) {
            const resultDiv = document.getElementById('result');
            resultDiv.style.display = 'block';
            
            let resultHTML = '<h2>測驗結果</h2>';
            types.forEach(type => {
                resultHTML += `
                    <div class="type-box" style="background-color: ${typeColors[type]}">
                        ${type}型
                    </div>
                `;
            });
            
            resultHTML += `
                <p>你的主要行為風格類型是：${types.join('、')}型</p>
                <p style="margin-top: 20px;">${getTypeDescription(types)}</p>
            `;
            
            resultDiv.innerHTML = resultHTML;
            window.scrollTo(0, document.body.scrollHeight);
        }

        // 重設測驗
        function resetTest() {
            userAnswers = new Array(questions.length);
            document.querySelectorAll('.option').forEach(opt => opt.classList.remove('selected'));
            document.getElementById('result').style.display = 'none';
        }

        // 類型描述
        function getTypeDescription(types) {
            const descriptions = {
                D: "果斷直接，重視結果，喜歡挑戰",
                I: "熱情開朗，善於交際，富有創意",
                S: "穩健可靠，注重合作，避免衝突",
                C: "謹慎細心，講求精確，重視邏輯"
            };
            
            return types.map(type => `${type}型：${descriptions[type]}`).join('<br>');
        }

        // 初始化
        initQuestions();
    </script>
</body>
</html>
