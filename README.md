<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="format-detection" content="telephone=no">
  <title>Тест: Риск диабета</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
      margin: 0;
      padding: 10px 8px;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .container {
      width: 100%;
      max-width: 420px;     /* Узкая мобильная ширина */
      background: white;
      border-radius: 16px;
      box-shadow: 0 10px 30px rgba(0,0,0,0.25);
      overflow: hidden;
      margin: 0 auto;
    }

    header {
      background: #d32f2f;
      color: white;
      padding: 22px 15px;
      text-align: center;
    }

    .quiz {
      padding: 24px 20px;
    }

    .progress {
      height: 8px;
      background: #1976d2;
      border-radius: 4px;
      margin-bottom: 20px;
    }

    h2 {
      font-size: 1.35rem;
      color: #222;
      margin: 0 0 18px 0;
    }

    p {
      font-size: 17px;
      line-height: 1.5;
      margin-bottom: 20px;
    }

    label {
      display: block;
      padding: 16px 18px;
      margin: 12px 0;
      background: #f8f9fa;
      border-radius: 12px;
      cursor: pointer;
      font-size: 16.5px;
      transition: all 0.2s;
    }

    label:hover, label:active {
      background: #e3f2fd;
    }

    button {
      padding: 15px 20px;
      font-size: 16.5px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
    }

    .next-btn {
      background: #1976d2;
      color: white;
      flex: 1;
    }

    .prev-btn {
      background: #666;
      color: white;
    }

    .result {
      padding: 35px 20px;
      display: none;
      text-align: center;
    }

    .high-risk {
      background: #ffebee;
      color: #c62828;
    }

    .low-risk {
      background: #e8f5e9;
      color: #2e7d32;
    }

    /* Дополнительно для очень маленьких экранов */
    @media (max-width: 360px) {
      .quiz {
        padding: 20px 16px;
      }
      label {
        padding: 14px 16px;
        font-size: 16px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>Тест: Риск диабета 2 типа</h1>
      <p>8 вопросов • Отвечай честно</p>
    </header>
    
    <div class="quiz" id="quiz"></div>
    <div class="result" id="result"></div>
  </div>

  <script>
    const questions = [
      { q: "Тебе больше 40 лет?", name: "q1" },
      { q: "У тебя есть избыточный вес или ожирение? (ИМТ > 25)", name: "q2" },
      { q: "У твоих родителей, братьев или сестёр есть сахарный диабет?", name: "q3" },
      { q: "Ты мало двигаешься? (меньше 30 минут активности в день)", name: "q4" },
      { q: "Ты часто ешь сладкое, фастфуд и сладкие напитки?", name: "q5" },
      { q: "У тебя повышенное давление (выше 130/85)?", name: "q6" },
      { q: "У тебя когда-нибудь был повышенный сахар в крови?", name: "q7" },
      { q: "Ты куришь или раньше курил?", name: "q8" }
    ];

    let currentQuestion = 0;
    let score = 0;
    let answers = {};

    const quizDiv = document.getElementById('quiz');
    const resultDiv = document.getElementById('result');

    function renderQuestion() {
      const progress = Math.round(((currentQuestion) / questions.length) * 100);

      quizDiv.innerHTML = `
        <div class="progress" style="width:${progress}%"></div>
        
        <h2>Вопрос ${currentQuestion + 1} из 8</h2>
        <p>${questions[currentQuestion].q}</p>
        
        <label><input type="radio" name="answer" value="1"> Да</label>
        <label><input type="radio" name="answer" value="0"> Нет</label>
        
        <div style="margin-top:30px; display:flex; gap:12px;">
          ${currentQuestion > 0 ? `<button onclick="prevQuestion()" class="prev-btn">← Назад</button>` : ''}
          <button onclick="nextQuestion()" class="next-btn">
            ${currentQuestion === 7 ? 'Завершить тест' : 'Далее →'}
          </button>
        </div>
      `;

      setTimeout(() => {
        if (answers[currentQuestion] !== undefined) {
          document.querySelectorAll('input[name="answer"]').forEach(radio => {
            if (parseInt(radio.value) === answers[currentQuestion]) radio.checked = true;
          });
        }
      }, 10);
    }

    function nextQuestion() {
      const selected = document.querySelector('input[name="answer"]:checked');
      if (!selected) {
        alert("Выберите Да или Нет!");
        return;
      }

      const value = parseInt(selected.value);
      if (answers[currentQuestion] !== undefined) score -= answers[currentQuestion];
      score += value;
      answers[currentQuestion] = value;

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

      const highRisk = score >= 5;

      resultDiv.innerHTML = `
        <div class="${highRisk ? 'high-risk' : 'low-risk'}" style="border-radius:16px; padding:35px 20px;">
          <h2>${highRisk ? '⚠️ Вы в группе повышенного риска!' : '✅ Риск низкий'}</h2>
          <p style="font-size:21px; margin:20px 0;">${score} из 8 факторов риска</p>
          
          ${highRisk ? 
            `<p><strong>Рекомендуется:</strong><br>
             • Сдать анализ крови на сахар<br>
             • Обратиться к врачу<br>
             • Изменить питание и больше двигаться` : 
            `<p>Хороший результат!<br>Продолжайте вести здоровый образ жизни.</p>`}
          
          <button onclick="location.reload()" style="margin-top:25px; padding:15px 30px; background:#1976d2; color:white; font-size:16.5px; border-radius:10px;">
            Пройти тест заново
          </button>
        </div>
      `;
    }

    renderQuestion();
  </script>
</body>
</html>
