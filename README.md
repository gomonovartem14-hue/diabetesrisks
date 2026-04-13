<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Тест: Насколько ты в риске диабета?</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
      margin: 0;
      padding: 20px;
    }
    .container {
      max-width: 700px;
      margin: 0 auto;
      background: white;
      border-radius: 15px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.2);
      overflow: hidden;
    }
    header {
      background: linear-gradient(135deg, #d32f2f, #f44336);
      color: white;
      padding: 25px;
      text-align: center;
    }
    .quiz {
      padding: 30px;
    }
    .question {
      margin-bottom: 25px;
    }
    h2 {
      color: #333;
      margin-bottom: 20px;
    }
    label {
      display: block;
      padding: 14px;
      margin: 10px 0;
      background: #f8f9fa;
      border-radius: 10px;
      cursor: pointer;
      transition: all 0.2s;
      font-size: 17px;
    }
    label:hover {
      background: #e3f2fd;
      transform: translateX(5px);
    }
    button {
      padding: 12px 28px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-right: 10px;
    }
    .next-btn {
      background: #1976d2;
      color: white;
    }
    .result {
      text-align: center;
      padding: 40px 20px;
      display: none;
    }
    .high-risk { background: #ffebee; color: #c62828; }
    .low-risk { background: #e8f5e9; color: #2e7d32; }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>Тест: Факторы риска диабета 2 типа</h1>
      <p>Отвечай честно • 8 вопросов</p>
    </header>
    <div class="quiz" id="quiz"></div>
    <div class="result" id="result"></div>
  </div>

  <script>
    const questions = [
      { q: "Тебе больше 40 лет?", name: "q1" },
      { q: "У тебя есть избыточный вес или ожирение? (ИМТ больше 25)", name: "q2" },
      { q: "У твоих родителей, братьев или сестёр есть сахарный диабет?", name: "q3" },
      { q: "Ты мало двигаешься? (меньше 30 минут активности в день)", name: "q4" },
      { q: "Ты часто ешь сладкое, фастфуд и сладкие напитки?", name: "q5" },
      { q: "У тебя повышенное артериальное давление (выше 130/85)?", name: "q6" },
      { q: "У тебя когда-нибудь был повышенный сахар в крови при анализе?", name: "q7" },
      { q: "Ты куришь или раньше курил?", name: "q8" }
    ];

    let currentQuestion = 0;
    let score = 0;
    let answers = {};

    const quizDiv = document.getElementById('quiz');
    const resultDiv = document.getElementById('result');

    function renderQuestion() {
      quizDiv.innerHTML = `
        <h2>Вопрос ${currentQuestion + 1} из 8</h2>
        <p style="font-size:18px; margin:25px 0;">${questions[currentQuestion].q}</p>
        
        <label><input type="radio" name="answer" value="1"> Да</label>
        <label><input type="radio" name="answer" value="0"> Нет</label>
        
        <div style="margin-top:35px;">
          ${currentQuestion > 0 ? '<button onclick="prevQuestion()" style="background:#666;color:white;">← Назад</button>' : ''}
          <button onclick="nextQuestion()" class="next-btn">Далее →</button>
        </div>
      `;
    }

    function nextQuestion() {
      const selected = document.querySelector('input[name="answer"]:checked');
      if (!selected) {
        alert("Пожалуйста, выбери Да или Нет!");
        return;
      }
      answers[currentQuestion] = parseInt(selected.value);
      if (selected.value == "1") score++;
      currentQuestion++;
      
      if (currentQuestion < questions.length) {
        renderQuestion();
      } else {
        showResult();
      }
    }

    function prevQuestion() {
      currentQuestion--;
      score = Object.values(answers).reduce((a, b) => a + b, 0);
      renderQuestion();
    }

    function showResult() {
      quizDiv.style.display = "none";
      resultDiv.style.display = "block";
      
      const riskLevel = score >= 5 ? "high-risk" : "low-risk";
      const title = score >= 5 
        ? "⚠️ Ты в группе повышенного риска!" 
        : "✅ Риск низкий или средний";

      resultDiv.innerHTML = `
        <div class="${riskLevel}" style="padding:40px; border-radius:15px;">
          <h2>${title}</h2>
          <p style="font-size:22px; margin:20px 0;">У тебя ${score} из 8 факторов риска.</p>
          ${score >= 5 ? 
            `<p><strong>Рекомендуется:</strong><br>
            • Сдать анализ крови на сахар и гликированный гемоглобин<br>
            • Обратиться к терапевту или эндокринологу<br>
            • Изменить образ жизни</p>` : 
            `<p>Хороший результат! Продолжай вести здоровый образ жизни.</p>`}
          <button onclick="location.reload()" style="margin-top:25px; padding:14px 30px; background:#1976d2; color:white; font-size:17px; border-radius:8px;">
            Пройти тест заново
          </button>
        </div>
      `;
    }

    renderQuestion();
  </script>
</body>
</html>
