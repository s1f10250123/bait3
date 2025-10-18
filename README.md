<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>日ごとの金額計算ツール</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {
      font-family: "Segoe UI", "Hiragino Sans", sans-serif;
      margin: 20px;
      background: linear-gradient(135deg, #f4f6f8, #e9ecef);
      color: #333;
    }
    h1 {
      text-align: center;
      margin-bottom: 20px;
      color: #2c3e50;
    }
    .day {
      background: #fff;
      padding: 15px;
      border-radius: 16px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      margin-bottom: 20px;
    }
    label {
      display: inline-block;
      width: 70px;
      font-weight: bold;
    }
    input {
      width: 80px;
      padding: 6px;
      text-align: right;
      border-radius: 8px;
      border: 1px solid #ccc;
      margin: 4px;
    }
    button {
      display: inline-block;
      margin: 10px 5px;
      padding: 10px 18px;
      background-color: #007bff;
      color: #fff;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
      transition: 0.2s;
    }
    button:hover {
      background-color: #0056b3;
    }
    .result {
      background: #fff;
      padding: 15px;
      border-radius: 16px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      font-size: 17px;
    }
    table {
      border-collapse: collapse;
      width: 100%;
      margin-top: 8px;
    }
    th, td {
      border: 1px solid #ccc;
      padding: 6px 10px;
      text-align: right;
    }
    th {
      background-color: #f0f0f0;
    }
    .summary-table th, .summary-table td {
      text-align: right;
    }
    .summary-table th:first-child, .summary-table td:first-child {
      text-align: left;
    }
  </style>
</head>
<body>

<h1>日ごとの金額計算ツール</h1>

<div id="inputs"></div>

<button onclick="addDay()">日を追加</button>
<button onclick="calculate()">計算する</button>

<div id="result" class="result"></div>

<script>
  let dayCount = 0;

  const denominations = [
    { label: "10000円", class: "yen10000", value: 10000 },
    { label: "5000円", class: "yen5000", value: 5000 },
    { label: "1000円", class: "yen1000", value: 1000 },
    { label: "500円", class: "yen500", value: 500 },
    { label: "100円", class: "yen100", value: 100 },
    { label: "50円", class: "yen50", value: 50 },
    { label: "10円", class: "yen10", value: 10 },
    { label: "5円", class: "yen5", value: 5 },
    { label: "1円", class: "yen1", value: 1 }
  ];

  addDay();

  function addDay() {
    dayCount++;
    const div = document.createElement("div");
    div.classList.add("day");
    
    denominations.forEach(d => {
      const label = document.createElement("label");
      label.textContent = d.label + ":";
      const input = document.createElement("input");
      input.type = "number";
      input.className = d.class;
      input.min = 0;
      input.value = "";

      input.addEventListener("keydown", function(e) {
        if (e.key === "Enter") {
          e.preventDefault();
          const inputs = Array.from(div.querySelectorAll("input"));
          const index = inputs.indexOf(this);
          if (index < inputs.length - 1) {
            inputs[index + 1].focus();
          } else {
            calculate();
          }
        }
      });

      div.appendChild(label);
      div.appendChild(input);
      div.appendChild(document.createElement("br"));
    });

    document.getElementById("inputs").appendChild(div);
  }

  function calculate() {
    const allDays = document.querySelectorAll(".day");
    let totals = {};
    denominations.forEach(d => totals[d.class] = 0);

    let grandTotal = 0;

    // 入力を集計
    allDays.forEach(day => {
      denominations.forEach(d => {
        const input = day.querySelector(`.${d.class}`);
        let num = Number(input.value) || 0;
        totals[d.class] += num;
        grandTotal += num * d.value;
      });
    });

    // 総合計のみ表示
    let summaryHTML = `<b>総合計：</b><table class="summary-table">
      <tr><th>金額</th><th>枚数</th><th>小計</th></tr>`;

    denominations.forEach(d => {
      const totalPieces = totals[d.class];
      summaryHTML += `<tr>
        <td>${d.label}</td>
        <td>${totalPieces}</td>
        <td>${(totalPieces * d.value).toLocaleString()}円</td>
      </tr>`;
    });

    summaryHTML += `<tr>
      <td colspan="2"><b>合計金額</b></td>
      <td><b>${grandTotal.toLocaleString()}円</b></td>
    </tr></table>`;

    document.getElementById("result").innerHTML = summaryHTML;
  }
</script>

</body>
</html>
